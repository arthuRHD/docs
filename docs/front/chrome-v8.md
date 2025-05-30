# Comprendre le fonctionnement de Chrome V8

Le moteur V8 est le moteur JavaScript open source développé par Google, utilisé dans Chrome et Node.js. Il compile le code JavaScript en code machine natif pour offrir des performances optimales. V8 gère la mémoire, le garbage collector, l’optimisation du code, et supporte les dernières fonctionnalités ECMAScript.

## Promises

Les Promises permettent de gérer l’asynchronisme de façon plus lisible que les callbacks imbriqués.

```js
function fetchData() {
  return new Promise((resolve, reject) => {
    setTimeout(() => resolve('Données reçues'), 1000);
  });
}

fetchData().then(data => console.log(data));
```

## Async/Await

Async/Await est une syntaxe plus moderne pour travailler avec les Promises, rendant le code asynchrone plus lisible.

```js
async function main() {
  const data = await fetchData();
  console.log(data);
}
main();
```

## Callbacks

Les callbacks sont des fonctions passées en argument pour être exécutées plus tard, souvent utilisées avant l’arrivée des Promises.

```js
function fetchDataCallback(cb) {
  setTimeout(() => cb('Données reçues'), 1000);
}

fetchDataCallback(data => console.log(data));
```

## Generators

Les generators permettent de créer des fonctions qui peuvent être interrompues et reprises, utiles pour gérer des flux de données ou de l’asynchronisme contrôlé.

```js
function* compteur() {
  yield 1;
  yield 2;
  yield 3;
}

for (let n of compteur()) {
  console.log(n);
}
```

## Observables

Les Observables (ex: RxJS) permettent de gérer des flux de données asynchrones multiples (streams), avec la possibilité de s’abonner, transformer et combiner les flux.

```js
import { Observable } from 'rxjs';

const obs = new Observable(subscriber => {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.complete();
});

obs.subscribe({
  next: v => console.log(v),
  complete: () => console.log('Fini')
});
```

## EventEmitter

L’EventEmitter (Node.js) permet de gérer des événements personnalisés et d’y réagir via des listeners.

```js
const EventEmitter = require('events');
const emitter = new EventEmitter();

emitter.on('data', (msg) => console.log('Reçu:', msg));
emitter.emit('data', 'Hello Event!');
```

## Async Iterators

Les Async Iterators permettent d’itérer sur des données asynchrones avec la syntaxe `for await...of`.

```js
async function* asyncGen() {
  yield 'A';
  yield 'B';
}

(async () => {
  for await (const val of asyncGen()) {
    console.log(val);
  }
})();
```

## Comparatif rapide

| Concept         | Usage principal                | Syntaxe moderne | Gestion erreurs | Multi-événements |
|-----------------|-------------------------------|-----------------|----------------|------------------|
| Callback        | Asynchrone simple              | Non             | try/catch      | Non              |
| Promise         | Asynchrone, chaînage           | Oui             | catch()        | Non              |
| Async/Await     | Asynchrone, code lisible       | Oui             | try/catch      | Non              |
| Generator       | Flux contrôlés, pausable       | Oui             | try/catch      | Non              |
| Observable      | Streams, multi-événements      | Oui (RxJS)      | catchError     | Oui              |
| EventEmitter    | Événements personnalisés       | Oui (Node.js)   | try/catch      | Oui              |
| Async Iterator  | Itération asynchrone           | Oui             | try/catch      | Oui              |

## Quand utiliser chaque concept ? Cas d’usage et recommandations

- **Callback** : À éviter dans les nouveaux projets, car ils rendent le code difficile à maintenir (callback hell). Utilisez-les uniquement pour des APIs anciennes ou des cas très simples.
  ```js
  // À éviter pour du code moderne
  fs.readFile('file.txt', (err, data) => { /* ... */ });
  ```

- **Promise** : À privilégier pour toute opération asynchrone moderne (requêtes HTTP, accès fichiers, etc). Permet le chaînage et une gestion d’erreur centralisée.
  ```js
  fetch('/api/data').then(res => res.json()).then(data => console.log(data));
  ```

- **Async/Await** : Idéal pour écrire du code asynchrone lisible, notamment dans les fonctions métiers complexes ou les traitements séquentiels.
  ```js
  async function process() {
    const user = await fetchUser();
    const posts = await fetchPosts(user.id);
    return posts;
  }
  ```

- **Generator** : À utiliser pour créer des itérateurs personnalisés, des pipelines de traitement ou des flux pausable (ex : parsing, lazy loading).
  ```js
  function* range(n) { for (let i = 0; i < n; i++) yield i; }
  for (let x of range(3)) console.log(x); // 0, 1, 2
  ```

- **Observable** : Recommandé pour gérer des flux d’événements multiples, des WebSockets, ou des interfaces réactives complexes (RxJS). Idéal pour les applications frontend avec beaucoup d’interactions.
  ```js
  // Ex : écoute d’un flux de notifications
  notifications$.subscribe(msg => afficher(msg));
  ```

- **EventEmitter** : À utiliser côté Node.js pour la communication entre modules, la gestion d’événements système ou applicatifs (ex : serveur HTTP, file d’attente).
  ```js
  emitter.on('jobDone', () => console.log('Traitement terminé !'));
  ```

- **Async Iterator** : Parfait pour consommer des flux de données asynchrones (streams, API paginées, lecture de fichiers volumineux) avec une syntaxe moderne.
  ```js
  for await (const chunk of readableStream) { traiter(chunk); }
  ```

Résumé :
- Privilégiez **Promise** et **Async/Await** pour la majorité des traitements asynchrones métier.
- Utilisez **Observable** pour les flux complexes et **EventEmitter** pour la gestion d’événements côté serveur.
- **Callback** est à éviter sauf contrainte technique.
- **Generator** et **Async Iterator** sont utiles pour des cas avancés de flux ou de traitement pausable.

## Ressources utiles
- [V8 Documentation](https://v8.dev/docs)
- [MDN Promises](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Promise)
- [MDN Async functions](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Statements/async_function)
- [Node.js EventEmitter](https://nodejs.org/api/events.html)
- [RxJS](https://rxjs.dev/)