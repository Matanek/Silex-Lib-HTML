# Sélectionner et transformer un document

Cette recette combine des sélecteurs, une modification de groupe, une
navigation entre frères et l’insertion d’un fragment.

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

`cards` conserve les deux articles dans l’ordre du document. Les modifications
touchent chaque élément de la sélection, tandis que `filter(".featured")`
restreint l’insertion du séparateur au premier article. Le fragment ajoute un
troisième article indépendant avant les deux vérifications finales.

[Revenir à la documentation HTML](../README.md)
