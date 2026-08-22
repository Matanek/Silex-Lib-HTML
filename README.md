# HTML for Silex

`HTML` builds, parses, selects, transforms and serializes HTML documents. Text
and attribute values are escaped by default, void elements use HTML syntax, and
mixed content keeps its original order.

```sx
use HTML

var page = HTML.parse(source)
var cards = page.select("main > article.card[data-state=ready]")
cards.add_class("visible")
cards.find("a[href]").set_attribute("rel", "noopener")

print(HTML.serialize(page))
```

## Selecting and traversing

`Node.select` and `Selection.find` accept a deliberately bounded CSS subset:

- element names and the universal selector `*`;
- `#id` and `.class`;
- `[attribute]` and `[attribute=value]`, with quoted or unquoted values;
- compound selectors such as `article.card[data-kind=news]`;
- descendant and direct-child (`>`) combinators;
- comma-separated selector groups.

`Selection` keeps document order and removes duplicates. It provides `filter`,
`children`, `parent`, `parents`, `siblings`, `next`, `previous` and `closest`,
plus `first`, `at`, `nodes`, `count` and `text_content` for reading results.
Parent links are derived from the selection root instead of being stored in
nodes, so the DOM does not create ownership cycles.

The direct methods panic on an invalid selector. `try_select`, `try_find`,
`try_filter`, `try_children` and `try_closest` return a `SelectorError` for input
that comes from users or external systems. Pseudo-classes, pseudo-elements, CSS
escapes, sibling combinators and attribute operators other than exact equality
are not part of version 0.4.

## Transforming a document

A selection can update every matched element with `set_attribute`,
`remove_attribute`, `add_class`, `remove_class`, `toggle_class` and `set_text`.
It can also `append`, `append_all`, `prepend`, `remove` or `replace_with` nodes.
Inserted nodes are deep-copied for each target, which prevents one mutable node
from accidentally being shared between several DOM locations.

Fragments can be parsed without inventing a wrapper:

```sx
var list = page.select("ul.results")
list.append_all(HTML.parse_fragment("<li>First</li><li>Second</li>"))
```

`try_parse_fragment` provides the recoverable equivalent.

## Parsing and serialization

`try_parse` accepts explicit `ParseOptions` when an application must recover
from byte, node or nesting limits. Parsing is deliberately forgiving for
ordinary mismatched closing tags, repeated attributes and incomplete comments.

`append_text` is safe for ordinary content. `append_raw_text` is an explicit
escape hatch intended for trusted `script` and `style` source; it bypasses HTML
escaping. The parser uses raw nodes when it reads those elements.

The parser covers document structure, quoted and unquoted attributes, comments,
doctypes, standard numeric entities and the common named entities `amp`, `lt`,
`gt`, `quot`, `apos` and `nbsp`. It does not claim the full HTML Living Standard
tree-construction algorithm yet; browser-specific foster parenting and the
complete named-entity catalog remain outside version 0.4.

## Development

From the Silex project workspace root:

```text
silex link Packages/HTML
silex test Packages/HTML/Tests
silex run Packages/HTML/Examples/BuildAndParse.sx
silex run Packages/HTML/Examples/SelectAndTransform.sx
```
