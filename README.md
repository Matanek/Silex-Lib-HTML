# HTML for Silex

`HTML` builds, parses, traverses and serializes HTML documents. Text and
attribute values are escaped by default, void elements use HTML syntax, and
mixed content keeps its original order.

```sx
use HTML

var page = HTML.html_document("Welcome")
var body = page.elements("body")[0]
body.append_element("h1").append_text("Hello <Silex>")

print(HTML.serialize(page, HTML.Format.pretty))
```

Parse received or embedded markup with bounded defaults:

```sx
let document = HTML.parse(source)
for link in document.elements("a") {
    if let destination = link.attribute("href") { print(destination) }
}
```

`try_parse` accepts explicit `ParseOptions` when an application must recover
from byte, node or nesting limits. Parsing is deliberately forgiving for
ordinary mismatched closing tags, repeated attributes and incomplete comments.

`append_text` is safe for ordinary content. `append_raw_text` is an explicit
escape hatch intended for trusted `script` and `style` source; it bypasses HTML
escaping. The parser uses raw nodes when it reads those elements.

The current parser covers document structure, quoted and unquoted attributes,
comments, doctypes, standard numeric entities and the common named entities
`amp`, `lt`, `gt`, `quot`, `apos` and `nbsp`. It does not claim the full HTML
Living Standard tree-construction algorithm yet; browser-specific foster
parenting and the complete named-entity catalog remain outside version 0.2.

## Development

```text
silex link .
silex test "$PWD/Tests"
silex run Examples/BuildAndParse.sx
```
