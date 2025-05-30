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

### Typage avancé des props

Vous pouvez typer finement les props, y compris les callbacks et les objets imbriqués :

```typescript
interface ButtonProps {
  label: string;
  onClick: (event: React.MouseEvent<HTMLButtonElement>) => void;
  style?: React.CSSProperties;
}

export function Button({ label, onClick, style }: ButtonProps) {
  return <button style={style} onClick={onClick}>{label}</button>;
}
```

## Gestion des props par défaut et children

```typescript
interface CardProps {
  title: string;
  children?: React.ReactNode;
}

export function Card({ title, children }: CardProps) {
  return (
    <div className="card">
      <h2>{title}</h2>
      <div>{children}</div>
    </div>
  );
}
```

## Routing avec React Router

Pour gérer la navigation entre pages, utilisez [react-router-dom](https://reactrouter.com/):

```sh
npm install react-router-dom
```

Exemple d’utilisation :
```typescript
import { BrowserRouter, Routes, Route, Link } from 'react-router-dom';

export function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Accueil</Link>
        <Link to="/about">À propos</Link>
      </nav>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </BrowserRouter>
  );
}
```

## Gestion des formulaires

Pour gérer les formulaires, utilisez le state et les événements :

```typescript
import { useState } from 'react';

export function ContactForm() {
  const [email, setEmail] = useState("");
  const [message, setMessage] = useState("");

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    // Envoyer les données
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="email" value={email} onChange={e => setEmail(e.target.value)} />
      <textarea value={message} onChange={e => setMessage(e.target.value)} />
      <button type="submit">Envoyer</button>
    </form>
  );
}
```

## Tests unitaires avec React Testing Library

Pour tester vos composants :

```sh
npm install --save-dev @testing-library/react @testing-library/jest-dom
```

Exemple de test :
```typescript
import { render, screen } from '@testing-library/react';
import { Button } from './Button';

test('affiche le label', () => {
  render(<Button label="OK" onClick={() => {}} />);
  expect(screen.getByText('OK')).toBeInTheDocument();
});
```

## Bonnes pratiques
- Structurez votre projet avec des dossiers `/components`, `/pages`, `/hooks`, `/utils`.
- Utilisez des hooks personnalisés pour factoriser la logique réutilisable.
- Préférez les fonctions pures et le typage strict.
- Documentez les props et les hooks avec des commentaires JSDoc.
- Utilisez des outils comme ESLint et Prettier pour la qualité du code.

## Ressources utiles
- [Documentation officielle React](https://fr.react.dev/)
- [React TypeScript Cheatsheets](https://react-typescript-cheatsheet.netlify.app/)
- [React Router](https://reactrouter.com/)
- [Testing Library](https://testing-library.com/docs/react-testing-library/intro/)