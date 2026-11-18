# EPrints face aux autres plateformes

*Ébauche 0.1 — novembre 2026. Synthèse de connaissances générales, à vérifier et compléter par la communauté
(versions et fonctions évoluent vite). L'objectif n'est pas de désigner un gagnant, mais d'identifier ce que
le successeur d'EPrints devrait emprunter à chacun, et ce qu'il devrait éviter.*

## Vue d'ensemble

| Plateforme | Pile technique | Modèle de données | Point fort à emprunter | Limite à éviter |
|---|---|---|---|---|
| **EPrints 3.4** | Perl, Apache/mod_perl, MySQL/MariaDB, Xapian (optionnel) | Jeux de données, objets, champs composés et récursifs | EPScript partout ; configuration déclarative ; plugins de plugins ; simplicité | EPScript enfermé dans le XML ; HTML d'abord ; relève Perl ; configuration non versionnée |
| **DSpace 7 et suivantes** | Java/Spring (API REST), Angular (interface) | Communautés → collections → items ; « entités configurables » | API REST complète ; séparation nette interface/serveur ; entités (personnes, projets…) | Lourdeur de déploiement et de personnalisation ; personnaliser l'interface demande du développement Angular |
| **InvenioRDM** | Python/Flask, OpenSearch, PostgreSQL | Notices au schéma DataCite, communautés, versions | JSON d'abord ; versionnement des notices ; schéma de métadonnées normé ; base de Zenodo | Personnalisation surtout réservée aux développeurs Python ; pile exigeante |
| **Samvera / Hyrax** | Ruby on Rails, Solr, Fedora ou Valkyrie | Œuvres, ensembles de fichiers, collections (modèle PCDM) | Modèle de contenu générique (PCDM) ; communauté de bibliothèques | Pile complexe ; nécessite des développeurs Rails |
| **Fedora 6** | Java ; stockage OCFL ; API LDP | Ressources liées (RDF), conteneurs | **Stockage OCFL lisible sans le logiciel** ; versions ; préservation | Couche de stockage seulement : il faut une application au-dessus |
| **Islandora** | Drupal (PHP) + Fedora | Nœuds Drupal + ressources liées | Écosystème Drupal ; collections patrimoniales multimédias | Double pile Drupal + Fedora |
| **Omeka S** | PHP | Ressources décrites en données liées (JSON-LD natif), gabarits de ressources | **JSON-LD natif** ; gabarits de ressources configurables sans code ; modules simples | Pensé pour les expositions et collections plus que pour l'archivage institutionnel à grande échelle |
| **Wikibase** | PHP (MediaWiki), service de requêtes SPARQL | Entités, propriétés, déclarations avec qualificatifs et **références** | **Tout est entité ; chaque affirmation porte sa source** ; historique complet | Pas un dépôt de fichiers ; modèle très ouvert, gouvernance éditoriale à construire |

## Ce que le successeur d'EPrints devrait emprunter

| Idée | Source d'inspiration | Lien avec le manifeste |
|---|---|---|
| Stockage de préservation ouvert, versionné, lisible sans la plateforme | Fedora 6 / OCFL | §4.3 Intégrité par construction |
| Chaque affirmation porte sa source, ses qualificatifs, son historique | Wikibase | §4.2 Tout est entité ; « remonter aux sources » |
| JSON d'abord, schéma de métadonnées normé, versions de notices | InvenioRDM | §4.1 Trois couches ; §4.7 Deux audiences |
| Entités configurables (personnes, projets, unités) | DSpace 7+ | §4.2 Tout est entité |
| Gabarits de ressources configurables sans code, JSON-LD natif | Omeka S | §4.4 EPScript 2.0 ; §4.10 Standards |
| Modèle de contenu générique et partagé (œuvre / fichiers / collections) | Samvera / PCDM | §2.3 Récursivité, méta-dépôt |
| Un seul langage de configuration partout, accessible aux non-programmeurs | **EPrints** | §2.1 et §4.4 — l'atout distinctif à préserver |

## Constat

Aucune plateforme ne réunit aujourd'hui :
1. la **configurabilité par des non-programmeurs** d'EPrints (EPScript),
2. le **graphe d'entités sourcées** de Wikibase,
3. l'**intégrité de préservation** d'OCFL,
4. la **lisibilité machine native** (OAIHP, signalement FAIR, JSON-LD, flux de changements).

C'est cet espace que vise le successeur d'EPrints.

## À vérifier

- Les fonctions exactes de chaque plateforme dans leurs versions 2026 (notamment DSpace 9, InvenioRDM 13+).
- Le support natif de FAIR Signposting, ResourceSync et COAR Notify par chaque plateforme ; le recensement de
  l'agent Normes (projet OAIHP) complétera ce point.
- Les retours d'expérience de migration depuis EPrints vers d'autres plateformes.
