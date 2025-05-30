# Personnaliser VSCode

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

VSCode permet d’automatiser des tâches courantes via le fichier `tasks.json` dans le dossier `.vscode/` de votre projet. Exemple pour lancer un build TypeScript :

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "build-ts",
      "type": "shell",
      "command": "tsc",
      "group": "build",
      "problemMatcher": "$tsc"
    }
  ]
}
```

Vous pouvez ensuite lancer la tâche via `Ctrl+Maj+B` ou la palette de commandes.

## Configuration du mode debug

Le fichier `.vscode/launch.json` permet de configurer des scénarios de débogage adaptés à chaque projet.

Exemple pour Node.js :
```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Lancer le programme",
      "program": "${workspaceFolder}/src/index.ts",
      "preLaunchTask": "build-ts",
      "outFiles": ["${workspaceFolder}/dist/**/*.js"]
    }
  ]
}
```

Pour d’autres langages (Python, Go, Java…), adaptez le type et les options selon l’extension installée.

## Configuration générales de l'éditeur

Vous pouvez personnaliser l’éditeur via le fichier `settings.json` (global ou par workspace).

Exemple de réglages utiles :
```json
{
  "editor.tabSize": 2,
  "editor.formatOnSave": true,
  "files.exclude": {
    "node_modules": true,
    "dist": true
  },
  "terminal.integrated.defaultProfile.linux": "bash"
}
```

- Utilisez la palette de commandes (`Ctrl+Maj+P`) pour accéder rapidement aux réglages.
- Les paramètres peuvent être surchargés par workspace pour s’adapter à chaque projet.

## Extensions

VSCode dispose d’un marketplace très riche. Quelques extensions recommandées :

- **Prettier** : formatage automatique du code.
- **ESLint** : linting JavaScript/TypeScript.
- **GitLens** : outils avancés pour Git.
- **Docker** : gestion des conteneurs et images.
- **Remote - Containers** : développement dans des environnements Docker isolés.
- **Python** : support complet pour Python.
- **Markdown All in One** : édition avancée de Markdown.

Pour installer une extension :
```sh
code --install-extension ms-python.python
```

Ou via l’interface graphique (onglet Extensions).

## Bonnes pratiques
- Versionnez les fichiers `.vscode/` utiles à l’équipe (tasks, launch, settings spécifiques).
- Utilisez des snippets pour accélérer la rédaction de code répétitif.
- Explorez les raccourcis clavier pour gagner en productivité.

## Ressources utiles
- [VSCode Docs](https://code.visualstudio.com/docs)
- [Marketplace Extensions](https://marketplace.visualstudio.com/vscode)
- [Exemples de configurations avancées](https://github.com/microsoft/vscode-recipes)
