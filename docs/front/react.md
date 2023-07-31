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

__useEffect__ effectue une tâche lorsqu'une valeur stockée dans un state est altérée. Si nous passons un tableau vide en paramètre, la tâche s'effectura à chaque rechargement du DOM si le composant s'y trouve.

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

