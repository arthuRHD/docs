# Entretien d'une base PostgreSQL

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

## les types de VACUUM

Il existe 4 types de vacuum.

- Le `VACUUM` classique, il vient lancer un requête de nettoyage.
- Le `VACUUM FULL`, à déconseiller si c'est une base en production car celui vient lock toutes les transactions sur la table ou la base ciblée par le vacuum.
- Le `VACUUM ANALYSE`, il permet en même temps de recalculer les données inscrites dans les tables techniques du serveur PostgreSQL.
- Le `VACUUM VERBOSE`, affichant plus de détail concernant l'opération.
