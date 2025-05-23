---
title: list-style-image
slug: Web/CSS/list-style-image
---

{{CSSRef}}

La propriété [CSS](/fr/docs/Web/CSS) **`list-style-image`** définit l'image utilisée comme puce devant les [éléments de listes](/fr/docs/Web/HTML/Element/li).

On peut également utiliser la propriété raccourcie [`list-style`](/fr/docs/Web/CSS/list-style).

{{InteractiveExample("CSS Demo: list-style-image")}}

```css interactive-example-choice
list-style-image: url("/shared-assets/images/examples/rocket.svg");
```

```css interactive-example-choice
list-style-image: none;
```

```html interactive-example
<section class="default-example" id="default-example">
  <div>
    <p>NASA Notable Missions</p>
    <ul class="transition-all unhighlighted" id="example-element">
      <li>Apollo</li>
      <li>Hubble</li>
      <li>Chandra</li>
      <li>Cassini-Huygens</li>
      <li>Spitzer</li>
    </ul>
  </div>
</section>
```

```css interactive-example
.default-example {
  font-size: 1.2rem;
}

#example-element {
  width: 100%;
  background: #be094b;
  color: white;
}

section {
  text-align: left;
  flex-direction: column;
}

hr {
  width: 50%;
  color: lightgray;
  margin: 0.5em;
}

.note {
  font-size: 0.8rem;
}

.note a {
  color: #009e5f;
}

@counter-style space-counter {
  symbols: "\1F680" "\1F6F8" "\1F6F0" "\1F52D";
  suffix: " ";
}
```

> [!NOTE]
> Cette propriété s'applique aux éléments d'une liste (c'est-à-dire aux éléments pour lesquels [`display`](/fr/docs/Web/CSS/display) vaut `list-item`). Par défaut, cela inclut les éléments [`<li>`](/fr/docs/Web/HTML/Element/li). Cette propriété peut être héritée par les éléments et si on veut donc gérer une liste de façon uniforme, on pourra appliquer la propriété à l'élément parent (qui correspond en général à [`<ol>`](/fr/docs/Web/HTML/Element/ol) ou à [`<ul>`](/fr/docs/Web/HTML/Element/ul)).

## Syntaxe

```css
/* Valeurs avec un mot-clé*/
list-style-image: none;

/*  Valeurs pointant vers une image */
list-style-image: url("star-solid.gif");

/* Valeurs avec une image */
list-style-image: linear-gradient(to left bottom, red, blue);

/* Valeurs globales */
list-style-image: inherit;
list-style-image: initial;
list-style-image: revert;
list-style-image: unset;
```

### Valeurs

- [`<image>`](/fr/docs/Web/CSS/image)
  - : Une valeur représentant une image valide, qui sera utilisée comme puce.
- `none`
  - : Aucune image ne sera utilisée comme puce. Si cette valeur est définie, le marqueur défini avec [`list-style-type`](/fr/docs/Web/CSS/list-style-type) sera utilisé à la place.

## Définition formelle

{{cssinfo}}

## Syntaxe formelle

{{csssyntax}}

## Exemples

### Utiliser une URL

#### HTML

```html
<ul>
  <li>Élément 1</li>
  <li>Élément 2</li>
</ul>
```

#### CSS

```css
ul {
  list-style-image: url("star-solid.gif");
}
```

#### Résultat

{{EmbedLiveSample('Utiliser_une_URL')}}

### Utiliser un dégradé

#### HTML

```html
<ul>
  <li>Élément 1</li>
  <li>Élément 2</li>
</ul>
```

#### CSS

```css
ul {
  font-size: 200%;
  list-style-image: linear-gradient(to left bottom, red, blue);
}
```

#### Résultat

{{EmbedLiveSample('Utiliser_un_dégradé')}}

## Spécifications

{{Specifications}}

## Compatibilité des navigateurs

{{Compat}}

## Voir aussi

- [`list-style`](/fr/docs/Web/CSS/list-style)
- [`list-style-type`](/fr/docs/Web/CSS/list-style-type)
- [`list-style-position`](/fr/docs/Web/CSS/list-style-position)
- La fonction [`url()`](/fr/docs/Web/CSS/url_value)
