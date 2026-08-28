# Build then parse a page

This recipe creates an HTML document without source text, serializes it, then
parses the result to verify that attributes retained their value.

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

`append_text` escapes the paragraph’s HTML characters. Serialization does the
same for the ampersand in the `href` attribute; the parser then restores the
original application value. The final assertion shows that looking up an
absent identifier returns `null`.

[Return to the HTML documentation](../README.md)
