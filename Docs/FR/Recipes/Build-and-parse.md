# Construire puis analyser une page

Cette recette crée un document HTML sans texte source, le sérialise, puis
analyse le résultat pour vérifier que les attributs ont conservé leur valeur.

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

`append_text` échappe les caractères HTML du paragraphe. La sérialisation fait
de même pour l’esperluette de l’attribut `href`; le parseur restitue ensuite la
valeur applicative originale. La dernière assertion montre qu’une recherche
d’identifiant absent retourne `null`.

[Revenir à la documentation HTML](../README.md)
