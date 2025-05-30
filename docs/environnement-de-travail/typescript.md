# Installer le tooling de TypeScript

TypeScript est un sur-ensemble de JavaScript qui ajoute le typage statique. Pour profiter pleinement de l’écosystème, il est recommandé d’installer et de configurer plusieurs outils pour le développement, la compilation et la qualité du code.

## Installation de TypeScript

Installez TypeScript globalement ou localement dans votre projet :

```sh
npm install -g typescript # global
# ou
npm install --save-dev typescript # local au projet
```

Vérifiez l’installation :
```sh
tsc --version
```

## NVM pour switcher de version NodeJS

[NVM](https://github.com/nvm-sh/nvm) permet de gérer plusieurs versions de Node.js sur la même machine.

Installation :
```sh
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.bashrc
```

Utilisation :
```sh
nvm install 20
nvm use 20
nvm ls
```

## Initialisation du package.json

Initialisez un projet Node.js :
```sh
npm init -y
```

Ajoutez TypeScript et d’autres dépendances :
```sh
npm install --save-dev typescript @types/node
```

## Mise en place du tsconfig.json

Générez un fichier de configuration TypeScript :
```sh
tsc --init
```

Exemple minimal :
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "outDir": "dist",
    "rootDir": "src",
    "strict": true
  },
  "include": ["src"]
}
```

## Nodemon pour le hot reload

[Nodemon](https://nodemon.io/) permet de relancer automatiquement votre application à chaque modification.

Installation :
```sh
npm install --save-dev nodemon ts-node
```

Ajoutez un script dans `package.json` :
```json
"scripts": {
  "dev": "nodemon --watch src --exec ts-node src/index.ts"
}
```

Lancez le mode développement :
```sh
npm run dev
```

## ESLint

ESLint permet d’assurer la qualité et la cohérence du code TypeScript.

Installation :
```sh
npm install --save-dev eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
```

### .eslintrc

Exemple de configuration :
```json
{
  "parser": "@typescript-eslint/parser",
  "plugins": ["@typescript-eslint"],
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended"
  ],
  "env": {
    "node": true,
    "es2020": true
  }
}
```

### .eslintignore

Ajoutez les dossiers à ignorer :
```
dist/
node_modules/
```

## Bonnes pratiques
- Placez votre code source dans un dossier `src/`.
- Compilez dans un dossier `dist/`.
- Utilisez des types explicites et activez les options strictes dans `tsconfig.json`.
- Ajoutez des scripts npm pour automatiser les tâches courantes (build, lint, dev).

## Ressources utiles
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- [ESLint TypeScript](https://typescript-eslint.io/)
- [Nodemon](https://nodemon.io/)
- [NVM](https://github.com/nvm-sh/nvm)
