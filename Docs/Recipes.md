# Recettes HTML

Ces recettes complètent la présentation de l’API dans le README. Elles sont
conservées comme documentation : les programmes de démonstration visuelle ou
applicative appartiennent à `Silex-Examples`.

## BuildAndParse

```sx
use HTML

func main() {
    var page = HTML.html_document("Silex packages", "fr")
    var body = page.elements("body")[0]
    var main = body.append_element("main")
    main.set_attribute("class", "documentation package")
    main.append_element("h1").append_text("HTML depuis Silex")
    main.append_element("p").append_text("Le texte <et> les attributs sont échappés.")
    var link = main.append_element("a")
    link.set_attribute("href", "/docs?format=html&lang=fr")
    link.append_text("Lire la documentation")

    let encoded = HTML.serialize(page, HTML.Format.pretty)
    print(encoded)

    var parsed = HTML.parse(encoded)
    if let destination = parsed.elements("a")[0].attribute("href") {
        assert(destination == "/docs?format=html&lang=fr")
    } else { panic("parsed link should retain its destination") }
    assert(parsed.find_by_id("missing") == null)
}
```

## SelectAndTransform

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
