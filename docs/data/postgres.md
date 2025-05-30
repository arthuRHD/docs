# Entretien d'une base PostgreSQL

L’entretien régulier d’une base PostgreSQL est essentiel pour garantir de bonnes performances, éviter la saturation du disque et assurer la fiabilité des données. Voici les principales opérations et bonnes pratiques à connaître.

## Récupération de l'espace disque après suppression

Quand une ligne est supprimée, l'espace disque n'est pas directement rendue. Il y a
un méchanisme de défragmentation de la base s'appelant VACUUM permettant de libérer
l'allocation disque

Si vous ne possèdez pas ou peu de place disque de base, les processus d'autovacuum ne se
déclencheront pas avant 200.000.000 transactions SQL. C'est beaucoup de transactions et pour la
plupart des applications, ce seuil ne sera jamais atteint.

Voici la requête permettant de récupérer les tables concernées qui nécessite une libération mémoire

```sql
SELECT relname FROM pg_stat_user_tables WHERE n_dead_tup > 0
```

Cette requête récupére tous les noms des tables ayant des lignes de données mortes. Il vous suffit par la suite d'effectuer

```sql
VACUUM ANALYSE <table_name>
```

## Les types de VACUUM

Il existe 4 types de vacuum.

- Le `VACUUM` classique, il vient lancer un requête de nettoyage.
- Le `VACUUM FULL`, à déconseiller si c'est une base en production car celui vient lock toutes les transactions sur la table ou la base ciblée par le vacuum.
- Le `VACUUM ANALYSE`, il permet en même temps de recalculer les données inscrites dans les tables techniques du serveur PostgreSQL.
- Le `VACUUM VERBOSE`, affichant plus de détail concernant l'opération.

## Surveillance de l’activité et de la santé de la base

PostgreSQL fournit de nombreuses vues pour surveiller l’état de la base :

- `pg_stat_activity` : affiche les connexions et requêtes en cours.
- `pg_stat_user_tables` : donne des statistiques sur les tables (nombre de lignes mortes, etc).
- `pg_locks` : liste les verrous actifs.

Exemple pour surveiller les requêtes longues :
```sql
SELECT pid, now() - pg_stat_activity.query_start AS duree, query
FROM pg_stat_activity
WHERE state = 'active' AND now() - pg_stat_activity.query_start > interval '5 minutes';
```

## Gestion des index

Les index accélèrent les requêtes mais doivent être entretenus :
- Vérifiez les index inutilisés :
  ```sql
  SELECT * FROM pg_stat_user_indexes WHERE idx_scan = 0;
  ```
- Recréez les index fragmentés :
  ```sql
  REINDEX INDEX nom_de_l_index;
  ```
- Supprimez les index obsolètes pour économiser de l’espace disque.

## Sauvegarde et restauration

- Utilisez `pg_dump` pour sauvegarder une base :
  ```sh
  pg_dump -U utilisateur -h hote -Fc nom_base > sauvegarde.dump
  ```
- Restaurez avec `pg_restore` :
  ```sh
  pg_restore -U utilisateur -h hote -d nouvelle_base sauvegarde.dump
  ```
- Automatisez les sauvegardes régulières (cron, scripts, outils comme `pgBackRest`).

## Bonnes pratiques
- Planifiez des `VACUUM` réguliers si l’autovacuum n’est pas suffisant.
- Surveillez l’espace disque et la croissance des tables.
- Mettez à jour PostgreSQL pour bénéficier des dernières optimisations et correctifs de sécurité.
- Documentez les opérations de maintenance dans un journal ou un outil de suivi.

## Ressources utiles
- [Documentation officielle PostgreSQL](https://www.postgresql.org/docs/)
- [pg_stat_statements](https://www.postgresql.org/docs/current/pgstatstatements.html) pour l’analyse des requêtes lentes
- [pgBackRest](https://pgbackrest.org/) pour la sauvegarde avancée
