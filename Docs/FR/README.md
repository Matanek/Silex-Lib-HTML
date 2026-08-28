# HTML pour Silex

Le package `HTML` construit, analyse, sélectionne, transforme et sérialise des
documents HTML. Le texte et les attributs sont échappés par défaut, les
éléments vides suivent la syntaxe HTML et le contenu mixte conserve son ordre.

[Read this documentation in English.](../EN/README.md)

## Installer le package

```text
silex install HTML
```

HTML demande Silex 0.39.0 ou une version plus récente.

## Sélectionner et transformer

Le parcours ordinaire analyse un document, sélectionne ses éléments puis les
modifie comme un groupe :

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

Le programme affiche :

```html
<main><article class="card visible" data-state="ready"><a href="/docs" rel="noopener">Documentation</a></article></main>
```

`Node.select` et `Selection.find` acceptent un sous-ensemble CSS volontairement
délimité :

- noms d’éléments et sélecteur universel `*` ;
- `#id` et `.class` ;
- `[attribute]` et `[attribute=value]`, avec valeur entre guillemets ou non ;
- sélecteurs composés comme `article.card[data-kind=news]` ;
- combinateurs descendant et enfant direct (`>`) ;
- groupes de sélecteurs séparés par des virgules.

`Selection` conserve l’ordre du document et retire les doublons. Elle fournit
`filter`, `children`, `parent`, `parents`, `siblings`, `next`, `previous` et
`closest`, ainsi que `first`, `at`, `nodes`, `count` et `text_content`. Les
liens vers les parents sont dérivés de la racine de sélection et ne créent donc
pas de cycles de possession dans le DOM.

Les méthodes directes s’arrêtent sur un sélecteur invalide. `try_select`,
`try_find`, `try_filter`, `try_children` et `try_closest` retournent un
`SelectorError` lorsque le sélecteur vient d’un utilisateur ou d’un système
externe. Les pseudo-classes, pseudo-éléments, échappements CSS, combinateurs de
frères et opérateurs d’attribut autres que l’égalité exacte ne font pas partie
de la version 0.4.

## Modifier la structure

Une sélection peut appliquer `set_attribute`, `remove_attribute`, `add_class`,
`remove_class`, `toggle_class` et `set_text` à tous les éléments trouvés. Elle
peut aussi employer `append`, `append_all`, `prepend`, `remove` ou
`replace_with`.

Les nœuds insérés sont copiés profondément pour chaque cible. Un même nœud
mutable ne peut donc pas être partagé accidentellement entre plusieurs
emplacements du DOM.

Les fragments s’analysent sans inventer d’élément englobant :

```sx
var list = page.select("ul.results")
list.append_all(HTML.parse_fragment("<li>First</li><li>Second</li>"))
```

`try_parse_fragment` fournit la forme récupérable.

## Analyser et sérialiser

`try_parse` accepte des `ParseOptions` explicites lorsqu’une application doit
récupérer les dépassements de taille, de nombre de nœuds ou de profondeur.
L’analyse reste volontairement tolérante pour les fermetures ordinaires mal
appariées, les attributs répétés et les commentaires incomplets.

`append_text` convient au contenu ordinaire. `append_raw_text` est une sortie
explicite réservée au code `script` et `style` de confiance ; elle désactive
l’échappement HTML. Le parseur crée lui-même ces nœuds bruts lorsqu’il lit ces
éléments.

Le parseur couvre la structure du document, les attributs avec ou sans
guillemets, les commentaires, doctypes, entités numériques standard et les
entités nommées courantes `amp`, `lt`, `gt`, `quot`, `apos` et `nbsp`. Il
n’implémente pas encore tout l’algorithme de construction d’arbre du HTML
Living Standard ; le foster parenting propre aux navigateurs et le catalogue
complet des entités nommées restent hors de la version 0.4.

## Choisir une recette

- [Construire puis analyser une page](Recipes/Build-and-parse.md)
- [Sélectionner et transformer un document](Recipes/Select-and-transform.md)

L’application de démonstration HTML généré appartient au dépôt central
[Silex-Examples](https://github.com/Matanek/Silex-Examples).

## Développer le package

Depuis la racine du workspace Silex :

```text
silex link Packages/HTML
silex test Packages/HTML/Tests
```
