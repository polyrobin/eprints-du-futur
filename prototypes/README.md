**Français** ([ci-dessous](#prototypes)) · **English** ([below](#english))

# Prototypes

| Prototype | Idée démontrée | Lien avec le manifeste |
|---|---|---|
| [`atelier-epscript2/`](atelier-epscript2/) · [**en ligne**](https://polyrobin.github.io/eprints-du-futur/prototypes/atelier-epscript2/) | EPScript 2.0 : syntaxe lisible hors du XML, **écrite en français ou en anglais sur une même structure neutre** (comme les dialectes d'AppleScript : le changement de langue réécrit le script, l'empreinte de structure reste identique), fonctions réutilisables, vérification de types contre le schéma (avec suggestion du bon nom de champ), exécution en bac à sable (arrêt d'une récursion sans fin), vue en blocs synchronisée avec le texte, essais en direct sur plusieurs notices, et une même notice servie en trois couches (données JSON-LD, cadre HTML sémantique, forme stylée). | §4.1 Trois couches, §4.4 EPScript 2.0, §6.1 Langage d'un genre nouveau |
| [`strates-donnees-liees/`](strates-donnees-liees/) · [**en ligne**](https://polyrobin.github.io/eprints-du-futur/prototypes/strates-donnees-liees/) | Une notice décrite par six vocabulaires (schema.org, Dublin Core, BIBO, FOAF, PROV, OAIHP), chacun sur son plan en 3D ; l'axe vertical relie les descriptions d'une même ressource (son identité) ; anneau des sources externes (ORCID, ROR, Crossref, Wikidata) ; recouvrements concordants ou divergents entre schémas ; Turtle et RDF/XML avec préfixes en couleur et déclarations repliées. Canevas sans bibliothèque. | §4.2 Tout est entité, §4.15 Structure, espaces de noms, données liées |

L'atelier est une page HTML autonome : essayez-le [en ligne](https://polyrobin.github.io/eprints-du-futur/prototypes/atelier-epscript2/), ou ouvrez `atelier-epscript2/index.html` dans un navigateur. Le langage, le
compilateur et les rendus sont volontairement simplifiés ; les notices sont fictives.

---

<a id="english"></a>

# Prototypes (English)

| Prototype | Idea demonstrated | Link to the [manifesto](../manifeste/MANIFESTO.en.md) |
|---|---|---|
| [`atelier-epscript2/`](atelier-epscript2/) · [**live**](https://polyrobin.github.io/eprints-du-futur/prototypes/atelier-epscript2/#en) | EPScript 2.0: a syntax readable outside XML, **written in French or in English on one shared neutral structure** (like AppleScript's dialects: switching language rewrites the script, and the structure hash stays the same), reusable functions, type checking against the schema (with a suggestion for the right field name), sandboxed execution (an endless recursion is stopped), a block view synchronized with the text, live tests on several records, and one record served in three layers (JSON-LD data, semantic HTML frame, styled form). | §4.1 Three layers, §4.4 EPScript 2.0, §6.1 A new kind of language |
| [`strates-donnees-liees/`](strates-donnees-liees/) · [**live**](https://polyrobin.github.io/eprints-du-futur/prototypes/strates-donnees-liees/#en) | A record described by six vocabularies (schema.org, Dublin Core, BIBO, FOAF, PROV, OAIHP), each on its own plane in 3D; the vertical axis connects the descriptions of the same resource (its identity); a ring of external sources (ORCID, ROR, Crossref, Wikidata); matching or diverging overlaps between schemas; Turtle and RDF/XML with coloured prefixes and folded declarations. Canvas, no library. | §4.2 Everything is an entity, §4.15 Structure, namespaces, linked data |

The workshop (`atelier`) is a standalone HTML page: try it [online](https://polyrobin.github.io/eprints-du-futur/prototypes/atelier-epscript2/#en), or open `atelier-epscript2/index.html` in a browser. The
language, the compiler and the renderings are deliberately simplified; the records are fictitious.
