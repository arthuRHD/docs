# Jointures SQL

Il y a plusieurs types de jointures que l'on peut faire lorsque deux ou plusieurs tables sont en relation. Il est facile de vite les confondre.
C'est pourquoi j'écris cette note pour éviter que cela m'arrive à l'avenir.

![sql-joins](./sql-joins.jpg)

Voici la liste des différentes techniques de jointure qui sont utilisées :

- INNER JOIN : jointure interne pour retourner les enregistrements quand la condition est vrai dans les 2 tables. C’est l’une des jointures les plus communes.
- CROSS JOIN : jointure croisée permettant de faire le produit cartésien de 2 tables. En d’autres mots, permet de joindre chaque lignes d’une table avec chaque lignes d’une seconde table. Attention, le nombre de résultats est en général très élevé.
- LEFT JOIN (ou LEFT OUTER JOIN) : jointure externe pour retourner tous les enregistrements de la table de gauche (LEFT = gauche) même si la condition n’est pas vérifié dans l’autre table.
- RIGHT JOIN (ou RIGHT OUTER JOIN) : jointure externe pour retourner tous les enregistrements de la table de droite (RIGHT = droite) même si la condition n’est pas vérifié dans l’autre table.
- FULL JOIN (ou FULL OUTER JOIN) : jointure externe pour retourner les résultats quand la condition est vrai dans au moins une des 2 tables.
- SELF JOIN : permet d’effectuer une jointure d’une table avec elle-même comme si c’était une autre table.
- NATURAL JOIN : jointure naturelle entre 2 tables s’il y a au moins une colonne qui porte le même nom entre les 2 tables SQL
