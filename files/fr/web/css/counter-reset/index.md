---
title: counter-reset
slug: Web/CSS/counter-reset
---

{{CSSRef}}

La propriété **`counter-reset`** permet de réinitialiser un [compteur CSS](/fr/docs/Web/CSS/CSS_counter_styles/Using_CSS_counters) avec une valeur donnée.

{{InteractiveExample("CSS Demo: counter-reset")}}

```css interactive-example-choice
counter-reset: none;
```

```css interactive-example-choice
counter-reset: chapter-count 0;
```

```css interactive-example-choice
counter-reset: chapter-count;
```

```css interactive-example-choice
counter-reset: chapter-count 5;
```

```css interactive-example-choice
counter-reset: chapter-count -5;
```

```html interactive-example
<section class="default-example" id="default-example">
  <div class="transition-all" id="chapters">
    <h1>Alice's Adventures in Wonderland</h1>
    <h2>Down the Rabbit-Hole</h2>
    <h2 id="example-element">The Pool of Tears</h2>
    <h2>A Caucus-Race and a Long Tale</h2>
    <h2>The Rabbit Sends in a Little Bill</h2>
  </div>
</section>
```

```css interactive-example
#default-example {
  text-align: left;
  counter-reset: chapter-count;
}

#example-element {
  background-color: lightblue;
  color: black;
}

h2 {
  counter-increment: chapter-count;
  font-size: 1em;
}

h2::before {
  content: "Chapter " counters(chapter-count, ".") ": ";
}
```

> [!NOTE]
> Pour incrémenter ou décrémenter le compteur, on pourra utiliser la propriété CSS {{cssxref("counter-increment")}}.

## Syntaxe

```css
/* On réinitialise nom-compteur à 0 */
counter-reset: nom-compteur;

/* On réinitialise nom-compteur à -1 */
counter-reset: nom-compteur -1;

/* On initialise compteur1 à 1 et compteur2 à 4 */
counter-reset: compteur1 1 compteur2 4;

/* On annule toute réinitialisation qui aurait
   pu être déclarée avec des règles moins spé-
   cifiques */
counter-reset: none;

/* Valeurs globales */
counter-reset: inherit;
counter-reset: initial;
counter-reset: unset;
```

Cette propriété se définit grâce à :

- un identifiant (`<custom-ident>`) qui représente le compteur, éventuellement suivi par un entier (`<integer>`). Cela peut être répété plusieurs fois avec un espace en tant que séparateur.
- ou grâce au mot-clé `none`.

### Valeurs

- {{cssxref("custom-ident", "&lt;custom-ident&gt;")}}
  - : Le nom du compteur qu'on souhaite incrémenter. L'identifiant peut se composer d'une combinaison de lettres entre `a` et `z` (sensible à la casse), de nombres compris entre `0` et `9`, de tirets bas (`_` ou _underscores_) et/ou de tirets. Le premier caractère qui n'est pas un tiret doit être une lettre. Il est également interdit d'utiliser deux tirets au début d'un identifiant. Enfin, un identifiant ne doit pas être `none`, `unset`, `initial` ou `inherit` quelque soit la combinaison de la casse.
- {{cssxref("&lt;integer&gt;")}}
  - : La valeur avec laquelle on souhaite réinitialiser le compteur pour chaque occurrence de l'élément. La valeur par défaut est `0`.
- `none`
  - : Un mot-clé indiquant qu'il ne faut pas réinitialiser le compteur. Cette valeur peut être utilisée pour masquer des réinitialisations provenant de règles moins spécifiques.

## Définition formelle

{{CSSInfo}}

## Syntaxe formelle

{{CSSSyntax}}

## Exemples

### CSS

```css
ol {
  list-style-type: none;
  counter-reset: compteListe;
}

li::before {
  counter-increment: compteListe;
  content: counter(compteListe) " ";
}

.reinit {
  counter-reset: compteListe;
}
```

### HTML

```html
<ol>
  <li>Et de un</li>
  <li>Et de deux</li>
  <li>Et de trois</li>
  <li class="reinit">Et ça repart</li>
  <li>Et de deux</li>
</ol>
```

### Résultat

{{EmbedLiveSample("Exemples")}}

## Spécifications

{{Specifications}}

## Compatibilité des navigateurs

{{Compat}}

## Voir aussi

- [Les compteurs CSS](/fr/docs/Web/CSS/CSS_counter_styles/Using_CSS_counters)
- {{cssxref("counter-increment")}}
- {{cssxref("@counter-style")}}
- {{cssxref("counter-set")}}
- {{cssxref("counter")}} et {{cssxref("counters")}}
- {{cssxref("content")}}
