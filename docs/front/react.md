# React

React est une librairie JS permettant de faire du rechargement réactif à l'aide d'un DOM virtuel. Cela permet d'avoir des applications web dynamiques sans rechargements de la page. C'est l'outil le plus utilisé pour faire des sites en JS/TS d'après NPM.

## Génération d'un projet React en TypeScript

```sh
npx create-react-app my-app --template typescript
npm start
```

## Création d'un composant simple

Avec TypeScript, on doit spécifier les types de chaques attributs/variables/fonctions. Il en va de même pour les composants React.

```typescript
interface ComponentProps { text?: string }

export function CustomComponent({ text }: ComponentProps) {
  return (<div>{text}</div>);
}
```

Utilisation au sein d'un autre composant :

```typescript
import { CustomComponent } from './CustomComponent.jsx';

export function App() {
  let innerText: string = "Hello World!";
  return (<CustomComponent text={innerText}/>);
}
```

## Hooks

### useState

__useState__ permet de stocker une valeur mutable (par exemple, un input ou un fichier). Le DOM est rechargé à chaque fois que cette valeur stockée est modifiée.

```typescript
import React from 'react';
import { CustomComponent } from './CustomComponent.jsx';

export function App() {
  const [innerText, setInnerText] = useState<string>("");

  return (
      <div>
          <CustomComponent text={innerText}/>
          <input value={innerHtml} onChange={e => setInnerHtml(e.target.value)}/>
      </div>
  );
}
```

### useEffect

__useEffect__ effectue une tâche lorsqu'une valeur stockée dans un state est altérée. Si nous passons un tableau vide en paramètre, la tâche s'effectura une seule fois au chargement du DOM.

```typescript
import React from 'react';
import { CustomComponent } from './CustomComponent.jsx';

export function App() {

  const [count, setCount] = useState<number>(0);
  const [innerText, setInnerText] = useState<string>("");

  useEffect(() => console.log("App component initialized"), [])

  useEffect(() => {
    fetch('https://jsonplaceholder.typicode.com/todos/' + count)
      .then(response => response.json())
      .then(json => setInnerText(json.title))
  }, [count, setInnerText]);

  const increment = () => setCount(count + 1)

  return (
      <div>
          <CustomComponent text={innerText}/>

          <button onClick={increment}>Click hear {count}</button>
      </div>
  );
}
```

## Gestion des appels API

C'est pratique de savoir comment créer des composants graphiques, mais que fait-on si nous devons récupérer de la données depuis un serveur ? il faut être en mesure de pouvoir organiser les appels en suivant les bonnes pratiques. Cela signifie:

- Ne pas stocker de clés privées ou de clés API côté client pour la sécurité.
- Eviter de dupliquer les appels dans chaque composant, il faut les centraliser dans un DAO.

### Utilisation de Redux avec Thunk

Utiliser Redux nous permet découpler l'UI de la données. On utilise Thunk pour ajouter un middleware qui nous permet d'abstraire les changements de state dûs à la réponse API (loading, error, etc).
