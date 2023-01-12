# Visual Studio Code

Il est possible de facilement configurer l'environnement avec seulement un seul fichier.
Ce fichier JSON doit avoir finir par l'extension `<my_workspace>.code-workspace` afin d'être reconnu par l'éditeur.

## Environnement de travail multi-repo

Au sein même du fichier, il faut insérer sous la clé `folders`, les dossiers des multiples projets utilisés
dans votre environnement de travail. Voici un exemple:

```json
{
  "folders": [
    {
      "name": "Backend",
      "path": "back-end-folder-name"
    },
    {
      "name": "Frontend",
      "path": "front-end-folder-name"
    },
  ]
}
```

L'idéal est d'avoir les dossiers des multiples projets sous un même dossier parent lorsque vous les cloner, afin de faciliter la configuration.

## Automatisation des actions

TODO

## Configuration du mode debug

TODO

## Configuration générales de l'éditeur

TODO

## Extensions

TODO
