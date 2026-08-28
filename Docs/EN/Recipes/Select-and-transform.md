# Select and transform a document

This recipe combines selectors, a group change, sibling navigation, and
fragment insertion.

```sx
use HTML

func main() {
    var page = HTML.parse(
        "<main><article class='card featured'><h2>First</h2></article>" +
        "<article class=card><h2>Second</h2></article></main>"
    )

    var cards = page.select("main > article.card")
    cards.add_class("ready")
    cards.find("h2").set_attribute("data-level", "2")
    cards.filter(".featured").prepend(HTML.element("hr"))

    var extra = HTML.parse_fragment("<article class='card ready'><h2>Third</h2></article>")
    page.select("main").append_all(extra)

    assert(page.select("article.ready").count() == 3)
    assert(page.select("article.featured").siblings().count() == 2)
    print(HTML.serialize(page, HTML.Format.pretty))
}
```

`cards` keeps both articles in document order. Changes affect every element in
the selection, while `filter(".featured")` limits separator insertion to the
first article. The fragment adds an independent third article before the two
final checks.

[Return to the HTML documentation](../README.md)
