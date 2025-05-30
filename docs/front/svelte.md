# Faire un front avec Svelte + SvelteKit

Svelte est un framework JavaScript moderne qui compile les composants en code natif très performant. SvelteKit est le méta-framework officiel pour créer des applications web complètes (SSR, SSG, SPA) avec Svelte.

## Création d’un projet SvelteKit

Installez le CLI si besoin :
```sh
npm create svelte@latest mon-projet-svelte
cd mon-projet-svelte
npm install
npm run dev
```

## Structure d’un projet SvelteKit

- `src/routes/` : pages et endpoints (chaque fichier `.svelte` devient une route)
- `src/lib/` : composants réutilisables
- `src/app.html` : template HTML principal
- `static/` : fichiers statiques (images, favicon, etc)

## Exemple de composant Svelte

```svelte
<script lang="ts">
  export let name: string = "World";
</script>

<h1>Hello {name}!</h1>
```

Utilisation :
```svelte
<!-- src/routes/+page.svelte -->
<script>
  import Hello from '$lib/Hello.svelte';
</script>
<Hello name="Arthur" />
```

## Routing et navigation

Chaque fichier `.svelte` dans `src/routes/` correspond à une route. Exemple :
- `src/routes/+page.svelte` → `/`
- `src/routes/about/+page.svelte` → `/about`

Pour naviguer :
```svelte
<a href="/about">À propos</a>
```

## Stores (état global)

Svelte propose des stores pour partager l’état entre composants :
```typescript
// src/lib/store.ts
import { writable } from 'svelte/store';
export const count = writable(0);
```
Utilisation :
```svelte
<script lang="ts">
  import { count } from '$lib/store';
</script>
<button on:click={() => $count++}>{$count}</button>
```

## Appels API et chargement de données

Utilisez les fonctions `load` de SvelteKit pour charger des données côté serveur ou client :
```typescript
// src/routes/posts/+page.ts
export async function load() {
  const res = await fetch('https://jsonplaceholder.typicode.com/posts');
  const posts = await res.json();
  return { posts };
}
```
Dans le composant :
```svelte
<script lang="ts">
  export let data;
</script>
{#each data.posts as post}
  <div>{post.title}</div>
{/each}
```

## Formulaires et actions

SvelteKit facilite la gestion des formulaires avec les actions :
```svelte
<form method="POST">
  <input name="email" type="email" />
  <button type="submit">Envoyer</button>
</form>
```
Dans `+page.server.ts` :
```typescript
export const actions = {
  default: async ({ request }) => {
    const data = await request.formData();
    const email = data.get('email');
    // Traitement...
    return { success: true };
  }
};
```

## Bonnes pratiques
- Utilisez TypeScript pour le typage des props et des stores.
- Structurez votre projet avec `/lib` pour les composants et stores partagés.
- Privilégiez les endpoints SvelteKit pour la logique serveur/API.
- Utilisez les hooks (`onMount`, `beforeUpdate`, etc) pour gérer le cycle de vie.
- Testez vos composants avec [Playwright](https://playwright.dev/) ou [Vitest](https://vitest.dev/).

## Ressources utiles
- [Documentation Svelte](https://svelte.dev/docs)
- [Documentation SvelteKit](https://kit.svelte.dev/docs)
- [Templates SvelteKit](https://github.com/sveltejs/kit/tree/master/packages/create-svelte/templates)
