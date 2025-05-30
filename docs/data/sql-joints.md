# Jointures SQL

Il y a plusieurs types de jointures que l'on peut faire lorsque deux ou plusieurs tables sont en relation. Il est facile de vite les confondre.
C'est pourquoi j'écris cette note pour éviter que cela m'arrive à l'avenir.

![sql-joins](./sql-joins.jpg)

Voici la liste des différentes techniques de jointure qui sont utilisées :

- **INNER JOIN** : jointure interne pour retourner les enregistrements quand la condition est vraie dans les 2 tables. C’est l’une des jointures les plus communes.
- **CROSS JOIN** : jointure croisée permettant de faire le produit cartésien de 2 tables. En d’autres mots, permet de joindre chaque ligne d’une table avec chaque ligne d’une seconde table. Attention, le nombre de résultats est en général très élevé.
- **LEFT JOIN (ou LEFT OUTER JOIN)** : jointure externe pour retourner tous les enregistrements de la table de gauche (LEFT = gauche) même si la condition n’est pas vérifiée dans l’autre table.
- **RIGHT JOIN (ou RIGHT OUTER JOIN)** : jointure externe pour retourner tous les enregistrements de la table de droite (RIGHT = droite) même si la condition n’est pas vérifiée dans l’autre table.
- **FULL JOIN (ou FULL OUTER JOIN)** : jointure externe pour retourner les résultats quand la condition est vraie dans au moins une des 2 tables.
- **SELF JOIN** : permet d’effectuer une jointure d’une table avec elle-même comme si c’était une autre table.
- **NATURAL JOIN** : jointure naturelle entre 2 tables s’il y a au moins une colonne qui porte le même nom entre les 2 tables SQL.

## Exemples pratiques

Supposons deux tables simples :

```sql
-- Table utilisateurs
CREATE TABLE utilisateurs (
  id INT PRIMARY KEY,
  nom VARCHAR(50)
);

-- Table commandes
CREATE TABLE commandes (
  id INT PRIMARY KEY,
  utilisateur_id INT,
  montant DECIMAL(10,2)
);
```

### INNER JOIN

```sql
SELECT u.nom, c.montant
FROM utilisateurs u
INNER JOIN commandes c ON u.id = c.utilisateur_id;
```
*Retourne uniquement les utilisateurs ayant passé au moins une commande.*

### LEFT JOIN

```sql
SELECT u.nom, c.montant
FROM utilisateurs u
LEFT JOIN commandes c ON u.id = c.utilisateur_id;
```
*Retourne tous les utilisateurs, même ceux sans commande (montant sera NULL).* 

### RIGHT JOIN

```sql
SELECT u.nom, c.montant
FROM utilisateurs u
RIGHT JOIN commandes c ON u.id = c.utilisateur_id;
```
*Retourne toutes les commandes, même si aucun utilisateur correspondant n’existe (rare mais possible si données orphelines).* 

### FULL OUTER JOIN

```sql
SELECT u.nom, c.montant
FROM utilisateurs u
FULL OUTER JOIN commandes c ON u.id = c.utilisateur_id;
```
*Retourne tous les utilisateurs et toutes les commandes, même s’il n’y a pas de correspondance.*

### CROSS JOIN

```sql
SELECT u.nom, c.montant
FROM utilisateurs u
CROSS JOIN commandes c;
```
*Produit cartésien : chaque utilisateur est associé à chaque commande.*

### SELF JOIN

```sql
SELECT a.nom AS nom1, b.nom AS nom2
FROM utilisateurs a
INNER JOIN utilisateurs b ON a.id <> b.id;
```
*Exemple : trouver toutes les paires d’utilisateurs différents.*

### NATURAL JOIN

Supposons deux tables ayant une colonne commune `id` :

```sql
SELECT *
FROM table1
NATURAL JOIN table2;
```
*La jointure se fait automatiquement sur les colonnes de même nom.*

## Conseils et bonnes pratiques
- Précisez toujours les conditions de jointure pour éviter les produits cartésiens involontaires.
- Utilisez des alias pour plus de lisibilité.
- Vérifiez la cardinalité des résultats pour éviter les doublons inattendus.
- Testez vos requêtes sur un sous-ensemble de données avant de les exécuter en production.

## Ressources utiles
- [SQL Joins Visualizer](https://sql-joins.leopard.in.ua/)
- [W3Schools SQL JOINs](https://www.w3schools.com/sql/sql_join.asp)
