# Architecture CSS

Il existe différentes manières d'organiser ses fichiers CSS dans les projets. Bien entendu, ces architectures peuvent être implémentées dans un même projet en fonction du besoin.

## OOCSS

L'approche orientée objet CSS consiste à écrire des blocks CSS uniques et réutilisables. Le but est de les importer dans les balises concernées sans dupliquer du code dans le fichier CSS pour une classe spécifique. On fonctionne par composition.

Exemple :
```css
/* Block générique */
.box {
  border: 1px solid #ccc;
  padding: 1rem;
}
/* Extension réutilisable */
.rounded {
  border-radius: 8px;
}
```
Utilisation :
```html
<div class="box rounded">Contenu</div>
```

## BEM

Block, Element & Modifiers. C'est une architecture permettant de traiter des cas spécifiques tels que des animations complexes tout en maintenant un niveau de lisibilité convenable.

Exemple :
```css
.button {
  background: #007bff;
  color: white;
}
.button--large {
  font-size: 1.5rem;
}
.button__icon {
  margin-right: 0.5rem;
}
```
Utilisation :
```html
<button class="button button--large">
  <span class="button__icon">🔍</span>
  Rechercher
</button>
```

## SMACSS

Scalar and Modular Architecture for CSS. Cela consiste à découpler un élément afin d'appliquer des contraintes sur le conteneur de celui-ci et sur son bloc complet. Cela demande beaucoup plus de discipline que les deux autres méthodes car il ne faut pas mettre de contraintes de padding/margin au sein du conteneur par exemple, cela pourrait interférer avec les autres cas d'utilisation de celui-ci.

Exemple :
```css
/* Layouts */
.layout-header {
  background: #f5f5f5;
  padding: 1rem 0;
}
/* Modules */
.card {
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
  background: white;
}
/* States */
.is-active {
  border: 2px solid #007bff;
}
```
Utilisation :
```html
<header class="layout-header">
  <div class="card is-active">Bienvenue</div>
</header>
```

## Conseils pour structurer vos CSS
- Privilégiez la composition et la réutilisabilité.
- Documentez vos conventions de nommage dans le projet.
- Utilisez des préprocesseurs (Sass, Less) pour faciliter la modularité.
- Séparez les fichiers par type (base, layout, modules, states, themes).
- Testez vos styles sur différents navigateurs et tailles d’écran.

## Ressources utiles
- [OOCSS](https://github.com/stubbornella/oocss/wiki)
- [BEM](http://getbem.com/introduction/)
- [SMACSS](https://smacss.com/)
- [CSS Tricks: CSS Architecture](https://css-tricks.com/css-architecture/)