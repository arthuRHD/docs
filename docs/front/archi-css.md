# Architecture CSS

Il existe différentes manières d'organiser ses fichiers CSS dans les projets. Bien entendu, ces architectures peuvent être implémentées dans un même projet en fonction du besoin.

## OOCSS

L'approche orienté objet CSS consiste a écrire des blocks CSS uniques et réutilisables. Le but est des les importer dans les balises concernées sans dupliquer du code dans le fichier CSS pour une classe spécifique. On fonctionne par composition.

## BEM

Block, Element & Modifiers. C'est une architecture permettant de traiter des cas spécifiques tel que des animations complèxes tout en maintenant un niveau de lisibilité convenable.

## SMACSS

Scalar and Modular Architecture for CSS. Cela consiste à découpler un élément afin d'appliquer des contraintes sur le conteneur de celui-ci et sur son bloc complet. Cela demande beaucoup + de discipline que les deux autres méthodes car il ne faut pas mettre de contraintes de padding/margin au sein du conteneur par exemple, cela pourrait interférer avec les autres cas d'utilisation de celui-ci.