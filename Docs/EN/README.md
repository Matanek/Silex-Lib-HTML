# HTML for Silex

The `HTML` package builds, parses, selects, transforms, and serializes HTML
documents. Text and attribute values are escaped by default, void elements use
HTML syntax, and mixed content keeps its original order.

[Lire cette documentation en français.](../FR/README.md)

## Install the package

```text
silex install HTML
```

HTML requires Silex 0.39.0 or newer.

## Select and transform

The ordinary path parses a document, selects its elements, then changes them
as a group:

```sx
use HTML

func main() {
    var page = HTML.parse(
        "<main><article class='card' data-state=ready>" +
        "<a href=/docs>Documentation</a></article></main>"
    )
    var cards = page.select("main > article.card[data-state=ready]")
    cards.add_class("visible")
    cards.find("a[href]").set_attribute("rel", "noopener")

    print(HTML.serialize(page))
}
```

The program prints:

```html
<main><article class="card visible" data-state="ready"><a href="/docs" rel="noopener">Documentation</a></article></main>
```

`Node.select` and `Selection.find` accept a deliberately bounded CSS subset:

- element names and the universal selector `*`;
- `#id` and `.class`;
- `[attribute]` and `[attribute=value]`, with quoted or unquoted values;
- compound selectors such as `article.card[data-kind=news]`;
- descendant and direct-child (`>`) combinators;
- comma-separated selector groups.

`Selection` keeps document order and removes duplicates. It provides `filter`,
`children`, `parent`, `parents`, `siblings`, `next`, `previous`, and `closest`,
plus `first`, `at`, `nodes`, `count`, and `text_content`. Parent links are
derived from the selection root and therefore create no ownership cycles in
the DOM.

Direct methods stop on an invalid selector. `try_select`, `try_find`,
`try_filter`, `try_children`, and `try_closest` return a `SelectorError` when a
selector comes from a user or external system. Pseudo-classes, pseudo-elements,
CSS escapes, sibling combinators, and attribute operators other than exact
equality are outside version 0.4.

## Change the structure

A selection can apply `set_attribute`, `remove_attribute`, `add_class`,
`remove_class`, `toggle_class`, and `set_text` to every match. It can also use
`append`, `append_all`, `prepend`, `remove`, or `replace_with`.

Inserted nodes are deep-copied for every target. The same mutable node therefore
cannot be shared accidentally between several DOM locations.

Fragments can be parsed without inventing a wrapper:

```sx
var list = page.select("ul.results")
list.append_all(HTML.parse_fragment("<li>First</li><li>Second</li>"))
```

`try_parse_fragment` provides the recoverable form.

## Parse and serialize

`try_parse` accepts explicit `ParseOptions` when an application must recover
from byte, node, or nesting limits. Parsing remains deliberately forgiving for
ordinary mismatched closing tags, repeated attributes, and incomplete
comments.

`append_text` is suitable for ordinary content. `append_raw_text` is an
explicit escape hatch reserved for trusted `script` and `style` source; it
bypasses HTML escaping. The parser creates these raw nodes when it reads those
elements.

The parser covers document structure, quoted and unquoted attributes,
comments, doctypes, standard numeric entities, and the common named entities
`amp`, `lt`, `gt`, `quot`, `apos`, and `nbsp`. It does not yet implement the
full HTML Living Standard tree-construction algorithm; browser-specific foster
parenting and the complete named-entity catalog remain outside version 0.4.

## Choose a recipe

- [Build then parse a page](Recipes/Build-and-parse.md)
- [Select and transform a document](Recipes/Select-and-transform.md)

The generated-HTML demonstration application belongs to the central
[Silex-Examples](https://github.com/Matanek/Silex-Examples) repository.

## Develop the package

From the Silex workspace root:

```text
silex link Packages/HTML
silex test Packages/HTML/Tests
```
