# HTMX

HTMX est une librairie JavaScript légère qui permet d’ajouter des interactions dynamiques à vos pages HTML sans écrire de code JS complexe. Elle facilite la création d’applications web réactives en utilisant des attributs HTML pour déclencher des requêtes AJAX, du WebSocket, du SSE ou des requêtes via le protocole HTTP standard.

## What is HTMX?

HTMX permet de rendre n’importe quel élément HTML interactif en ajoutant des attributs spécifiques. Vous pouvez charger dynamiquement du contenu, soumettre des formulaires, ou mettre à jour des parties de la page sans recharger l’ensemble du document.

## Installation

Ajoutez simplement la librairie dans votre page HTML :

```html
<script src="https://unpkg.com/htmx.org@1.9.12"></script>
```

Ou via npm :
```sh
npm install htmx.org
```

## Usage

Pour charger dynamiquement du contenu dans un élément :

```html
<button hx-get="/hello" hx-target="#result">Dire bonjour</button>
<div id="result"></div>
```

Quand l’utilisateur clique sur le bouton, une requête GET est envoyée à `/hello` et la réponse HTML est injectée dans le div `#result`.

## Examples

### Soumettre un formulaire sans rechargement
```html
<form hx-post="/api/login" hx-target="#message" hx-swap="outerHTML">
  <input name="user" />
  <input name="pass" type="password" />
  <button type="submit">Connexion</button>
</form>
<div id="message"></div>
```

### Pagination dynamique
```html
<a hx-get="/page/2" hx-target="#content" hx-push-url="true">Page suivante</a>
<div id="content">...</div>
```

## HTMX Attributes

- `hx-get`, `hx-post`, `hx-put`, `hx-delete` : type de requête HTTP à envoyer.
- `hx-target` : élément cible à mettre à jour avec la réponse.
- `hx-swap` : mode d’injection du contenu (`innerHTML`, `outerHTML`, `beforebegin`, etc).
- `hx-trigger` : événement qui déclenche la requête (ex : `click`, `change`, `load`).
- `hx-vals` : données supplémentaires à envoyer.
- `hx-headers` : headers personnalisés.
- `hx-push-url` : met à jour l’URL du navigateur.

## HTMX Events

HTMX émet de nombreux événements personnalisés pour réagir à chaque étape du cycle de vie d’une requête :

```js
document.body.addEventListener('htmx:afterSwap', function(evt) {
  console.log('Contenu mis à jour !');
});
```

Principaux événements :
- `htmx:configRequest`
- `htmx:beforeRequest`
- `htmx:afterRequest`
- `htmx:beforeSwap`
- `htmx:afterSwap`
- `htmx:responseError`

## HTMX Extensions

HTMX propose des extensions pour enrichir les fonctionnalités (par exemple, gestion de WebSocket, validation, etc). Exemple d’utilisation :

```html
<script src="https://unpkg.com/htmx.org/dist/ext/ws.js"></script>
<div hx-ext="ws" ws-connect="wss://example.com/socket"></div>
```

## HTMX and Go templates

HTMX s’intègre parfaitement avec les templates Go (html/template). Il suffit de générer des fragments HTML côté serveur et de les retourner en réponse aux requêtes HTMX.

Exemple côté Go :
```go
func HelloHandler(w http.ResponseWriter, r *http.Request) {
    tmpl := template.Must(template.ParseFiles("hello.html"))
    tmpl.Execute(w, map[string]string{"Name": "Arthur"})
}
```

Exemple côté HTML :
```html
<button hx-get="/hello" hx-target="#result">Dire bonjour</button>
<div id="result"></div>
```

## Bonnes pratiques
- Privilégiez les fragments HTML pour les réponses serveur.
- Utilisez les événements HTMX pour gérer les loaders, erreurs, etc.
- Combinez HTMX avec Alpine.js pour des interactions avancées sans framework lourd.

## Ressources utiles
- [Documentation officielle HTMX](https://htmx.org/docs/)
- [Liste des extensions HTMX](https://htmx.org/extensions/)
- [Exemples HTMX + Go](https://htmx.org/examples/go/)