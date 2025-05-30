# The Twelve Factors App

Le manifeste [12-Factor App](https://12factor.net/fr/) définit 12 principes pour concevoir des applications web modernes, portables, maintenables et scalables, principalement destinées au cloud. Voici un résumé de chaque facteur, leur valeur ajoutée et un exemple d’application respectant ces principes.

## Les 12 facteurs détaillés

1. **Codebase** :
   - *Principe* : Une seule base de code versionnée (git, svn…), déployée sur plusieurs environnements (dev, staging, prod).
   - *Valeur* : Garantit la cohérence, la traçabilité et la facilité de maintenance. Évite la dérive de versions entre environnements.

2. **Dependencies** :
   - *Principe* : Déclarer explicitement toutes les dépendances dans un fichier dédié (ex : `package.json`, `requirements.txt`).
   - *Valeur* : Permet de reproduire l’environnement partout, évite les bugs liés à des dépendances implicites ou manquantes.

3. **Config** :
   - *Principe* : Stocker la configuration (URL, clés, secrets) dans des variables d’environnement, jamais en dur dans le code.
   - *Valeur* : Sépare le code de la configuration, facilite le déploiement sur différents environnements sans modifier le code.

4. **Backing services** :
   - *Principe* : Considérer les services externes (BDD, cache, file storage, mail) comme des ressources attachées, accessibles via une URL/config.
   - *Valeur* : Permet de remplacer ou déplacer un service sans modifier le code, favorise la portabilité et la résilience.

5. **Build, release, run** :
   - *Principe* : Séparer les étapes de build (compilation), release (assemblage de la config) et run (exécution du code).
   - *Valeur* : Facilite les rollbacks, la reproductibilité et la traçabilité des déploiements.

6. **Processes** :
   - *Principe* : L’application s’exécute comme un ou plusieurs processus stateless, sans stockage local persistant (pas de session en mémoire, pas de fichiers locaux).
   - *Valeur* : Permet le scaling horizontal, la tolérance aux pannes et la simplicité d’architecture.

7. **Port binding** :
   - *Principe* : L’application exporte ses services via un port (ex : serveur HTTP intégré), sans dépendre d’un serveur web externe (Apache, Nginx).
   - *Valeur* : Rend l’application autonome, facilite le déploiement dans des environnements variés (cloud, conteneurs).

8. **Concurrency** :
   - *Principe* : Gérer la scalabilité en multipliant les processus (workers, threads, etc.), chaque processus étant indépendant.
   - *Valeur* : Permet d’adapter dynamiquement la capacité de l’application à la charge, favorise la haute disponibilité.

9. **Disposability** :
   - *Principe* : Les processus doivent démarrer et s’arrêter rapidement, être robustes face aux interruptions.
   - *Valeur* : Facilite le scaling, les déploiements continus, la résilience et la récupération après incident.

10. **Dev/prod parity** :
    - *Principe* : Garder les environnements de développement, staging et production aussi similaires que possible (mêmes versions, mêmes services).
    - *Valeur* : Réduit les bugs liés aux différences d’environnement, accélère la livraison et la résolution d’incidents.

11. **Logs** :
    - *Principe* : Considérer les logs comme un flux d’événements, à rediriger vers un système d’agrégation externe (ELK, Datadog, etc.).
    - *Valeur* : Permet l’analyse centralisée, la surveillance et l’alerte sans modifier l’application.

12. **Admin processes** :
    - *Principe* : Exposer les tâches d’administration (migrations, scripts ponctuels) comme des commandes séparées du runtime principal.
    - *Valeur* : Facilite la maintenance, la reproductibilité et la sécurité des opérations d’administration.

## Exemple d’application 12-Factor (Node.js/Express)

### Structure du projet
```
myapp/
├── src/
│   └── index.js
├── package.json
├── .env
└── README.md
```

### Dépendances explicites
`package.json` :
```json
{
  "name": "myapp",
  "version": "1.0.0",
  "main": "src/index.js",
  "dependencies": {
    "express": "^4.18.2",
    "dotenv": "^16.0.0"
  }
}
```

### Configuration via variables d’environnement
`.env` :
```
PORT=3000
MESSAGE=Hello 12-Factor!
```

### Code principal
`src/index.js` :
```js
require('dotenv').config();
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send(process.env.MESSAGE);
});

const port = process.env.PORT || 8080;
app.listen(port, () => console.log(`App listening on port ${port}`));
```

### Lancement
```sh
npm install
npm start
```

- **Logs** : Les logs sont envoyés sur la sortie standard (stdout), à rediriger vers un agrégateur (ex : ELK, Datadog).
- **Backing services** : Ajoutez une base de données via une URL en variable d’environnement (ex : `DATABASE_URL`).
- **Admin process** : Ajoutez un script de migration dans `package.json` (ex : `npm run migrate`).

## Bonnes pratiques
- Utilisez des outils comme Docker pour garantir la parité dev/prod.
- Stockez la configuration sensible hors du code source.
- Privilégiez les services managés et interchangeables.

## Ressources utiles
- [12factor.net (FR)](https://12factor.net/fr/)
- [Exemple Node.js 12-Factor](https://github.com/heroku/node-js-sample)