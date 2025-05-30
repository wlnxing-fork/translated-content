---
title: "<pre>: Предварительно отформатированный текстовый элемент"
slug: Web/HTML/Reference/Elements/pre
---

{{HTMLSidebar}}

HTML-элемент **`<pre>`** представляет собой предварительно отформатированный текст, который должен быть представлен точно так, как написано в HTML-файле. Текст обычно отображается с использованием непропорционального ("[monospace](/ru/docs/XUL/Style/monospace)") шрифта. Пробелы внутри этого элемента отображаются как записанные.

{{InteractiveExample("HTML Demo: &lt;pre&gt;", "tabbed-standard")}}

```html interactive-example
<pre>
  L          TE
    A       A
      C    V
       R A
       DOU
       LOU
      REUSE
      QUE TU
      PORTES
    ET QUI T'
    ORNE O CI
     VILISÉ
    OTE-  TU VEUX
     LA    BIEN
    SI      RESPI
            RER       - Apollinaire
</pre>
```

```css interactive-example
pre {
  font-size: 0.7rem;
  margin: 0;
}
```

| [Категории контента](/ru/docs/Web/HTML/Guides/Content_categories) | [Основной поток](/ru/docs/Web/HTML/Guides/Content_categories#%D0%9E%D1%81%D0%BD%D0%BE%D0%B2%D0%BD%D0%BE%D0%B9_%D0%BF%D0%BE%D1%82%D0%BE%D0%BA), [Явный контент](/ru/docs/Web/HTML/Guides/Content_categories#%D0%AF%D0%B2%D0%BD%D1%8B%D0%B9_%D0%BA%D0%BE%D0%BD%D1%82%D0%B5%D0%BD%D1%82) |
| ----------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Допустимое содержимое                                             | [Фразовый контент](/ru/docs/Web/HTML/Guides/Content_categories#phrasing_content)                                                                                                                                                                                                      |
| Пропуск тегов                                                     | Нет, открывающий и закрывающий теги обязательны.                                                                                                                                                                                                                                      |
| Допустимые родители                                               | Любой элемент, который принимает [содержимое потока](/ru/docs/Web/HTML/Guides/Content_categories#%D0%9E%D1%81%D0%BD%D0%BE%D0%B2%D0%BD%D0%BE%D0%B9_%D0%BF%D0%BE%D1%82%D0%BE%D0%BA)                                                                                                     |
| Неявные ARIA-роли                                                 | [Нет соответствующей роли](https://www.w3.org/TR/html-aria/#dfn-no-corresponding-role)                                                                                                                                                                                                |
| Допустимые ARIA-роли                                              | Любые                                                                                                                                                                                                                                                                                 |
| DOM-интерфейс                                                     | {{domxref("HTMLPreElement")}}                                                                                                                                                                                                                                                         |

## Атрибуты

Этот элемент включает в себя только [глобальные атрибуты](/ru/docs/Web/HTML/Reference/Global_attributes).

- `cols` {{non-standard_inline}}
  - : Содержит _предпочтительное_ количество символов, которое должна иметь строка. Это был нестандартный синоним [`width`](#width). Чтобы добиться такого эффекта, используйте CSS {{Cssxref("width")}}.
- `width`
  - : Содержит _предпочтительное_ количество символов, которое должна иметь строка. Хотя технически он все ещё реализован, этот атрибут не имеет визуального эффекта; чтобы достичь такого эффекта, используйте CSS {{Cssxref("width")}}.
- `wrap` {{non-standard_inline}}
  - : Подсказка, указывающая, как должен происходить перенос. В современных браузерах этот атрибут игнорируется, и никакого визуального эффекта не приводит; чтобы достичь такого эффекта, используйте CSS {{Cssxref("white-space")}}.

## Пример

### HTML

```html
<p>Использовать CSS для изменения цвета шрифта легко.</p>
<pre>
body {
  color: red;
}
</pre>
```

### Результат

{{EmbedLiveSample("Пример")}}

## Доступность

Важно предоставить альтернативное описание для любых изображений или диаграмм, созданных с использованием предварительно отформатированного текста. Альтернативное описание должно чётко и лаконично описывать содержание изображения или диаграммы.

Люди, испытывающие проблемы с плохим зрением и просматривающие страницы с помощью вспомогательных технологий, таких как программа чтения с экрана, могут не понимать, что представляют собой предварительно отформатированные текстовые символы, когда они читаются последовательно.

Комбинация элементов {{HTMLElement("figure")}} и {{HTMLElement("figcaption")}}, дополненная [`id`](/ru/docs/Web/HTML/Reference/Global_attributes#id) и [ARIA](/ru/docs/Web/Accessibility/ARIA) `role` и `aria-labelledby`, позволяет объявить предварительно отформатированный текст как изображение, а `figcaption` служит альтернативным описанием изображения.

### Пример

```html
<figure role="img" aria-labelledby="cow-caption">
  <pre>
  ____________________________
<  Я эксперт в своей области.  >
  ----------------------------
         \   ^__^
          \  (oo)\_______
             (__)\       )\/\
                 ||----w |
                 ||     ||
  </pre>
  <figcaption id="cow-caption">
    Корова говорит: «Я эксперт в своей области». Корова проиллюстрирована с
    использованием предварительно отформатированных текстовых символов.
  </figcaption>
</figure>
```

- [MDN Understanding WCAG, Guideline 1.1 explanations](/ru/docs/Web/Accessibility/Understanding_WCAG/Perceivable#Guideline_1.1_—_Providing_text_alternatives_for_non-text_content)
- [H86: Providing text alternatives for ASCII art, emoticons, and leetspeak | W3C Techniques for WCAG 2.0](https://www.w3.org/TR/WCAG20-TECHS/H86.html)

## Характеристики

{{Specifications}}

## Совместимость с браузерами

{{Compat}}

## Смотрите также

- CSS: {{Cssxref('white-space')}}, {{Cssxref('word-break')}}
- Связанный элемент: {{HTMLElement("code")}}
