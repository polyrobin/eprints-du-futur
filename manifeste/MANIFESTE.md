**Français** · [English](MANIFESTO.en.md)

# EPrints du futur — Manifeste

*Ébauche 0.1 — novembre 2026. Document de travail, ouvert aux contributions.*

> Pendant vingt-cinq ans, EPrints a montré qu'un dépôt institutionnel pouvait être simple, configurable et
> extensible sans être un monstre logiciel. Ce manifeste propose de lui donner un successeur naturel :
> une plateforme qui garde ce qu'EPrints fait de mieux, corrige ce que le temps a révélé, et se prépare
> à un monde où les machines lisent plus que les humains.

---

## 1. Pourquoi maintenant

**Le public a changé.** Dans les dépôts institutionnels, les robots et les agents représentent déjà, selon des mesures
encore partielles, la grande majorité du trafic. Les dépôts ont été conçus pour des lecteurs humains qui
parcourent des pages ; ils sont aujourd'hui moissonnés, recrawlés, résumés et cités par des systèmes qui
n'ont besoin ni de nos menus ni de nos facettes, mais qui ont un besoin vital de données exactes, versionnées
et prouvables. Nous pouvons subir ce trafic ou en faire notre premier canal de diffusion.

**Les attentes ont changé.** Identifiants pérennes pour chaque entité, données liées, signalement FAIR,
API documentées, intégrité vérifiable, accessibilité, édition visuelle : ce qui était optionnel est devenu
la base.

**Le socle technique a vieilli.** Perl et mod_perl ont porté EPrints admirablement, mais la relève de
développeurs se raréfie, et l'écart se creuse entre ce que la plateforme permet nativement et ce que les
établissements doivent bricoler autour d'elle. Le projet OAIHP, qui accompagne ce manifeste, en est la
démonstration : presque chaque exigence d'un dépôt « lisible par les machines » doit être ajoutée à EPrints
par un plugin, un correctif ou une règle de réécriture.

Le moment est bon parce qu'il ne manque pas grand-chose. Les idées fondamentales d'EPrints sont justes. Il
faut leur donner un langage, des standards et une architecture de leur époque.

---

## 2. Ce que nous gardons — les forces d'EPrints

Un successeur qui oublierait ce qui fait la valeur d'EPrints ne serait qu'une plateforme de plus. Voici
ce qui doit survivre, et même s'affirmer.

### 2.1 EPScript : un seul langage, partout
Dans EPrints, la même petite syntaxe d'expressions sert aux citations, aux pages de notices, aux phrases
traduites, aux workflows de dépôt, aux règles de validation et aux exportations. Un bibliothécaire qui sait
écrire `type = 'thesis' and date.is_set()` peut modifier presque tout le site. **Cette unité est la plus
grande force d'EPrints.** Elle est rare : la plupart des plateformes séparent gabarits, configuration et code,
et réservent chacun à un public différent.

### 2.2 Un modèle de données simple
Des jeux de données, des objets, des champs typés. Un champ peut être multiple, composé, ou contenir
d'autres objets. Ce modèle tient sur une page et pourtant décrit des thèses, des jeux de données, des
brevets, des œuvres et des personnes.

### 2.3 La récursivité
Les documents vivent dans des notices, les champs composés contiennent des champs, des objets en contiennent
d'autres. Source de bien des maux de tête, cette récursivité ouvre des possibilités que peu de plateformes
offrent : l'imbrication de contenus, et à terme de **dépôts entiers dans d'autres dépôts**.

### 2.4 Des plugins de plugins
Importation, exportation, écrans, conversions, événements, recherche : presque tout est un plugin, et un
plugin peut en spécialiser un autre. Avec un peu d'accompagnement, on peut à peu près tout faire.

### 2.5 La configuration plutôt que le code
Une grande part du comportement d'un dépôt s'ajuste par des fichiers déclaratifs, sans toucher au noyau.

### 2.6 La variété des formats
EPrints parle déjà de nombreux standards : Dublin Core, METS, MODS, DataCite, RDF, BibTeX, EndNote, OAI-PMH,
SWORD, et bien d'autres selon les extensions installées.

### 2.7 Une communauté qui configure
Les meilleures améliorations d'EPrints ne viennent pas toujours de programmeurs, mais de responsables de
dépôts qui ont appris à le configurer. Cette culture est un actif.

---

## 3. Ce que nous corrigeons — les leçons de l'expérience

Les constats ci-dessous viennent de la pratique quotidienne de dépôts EPrints en production et des travaux du projet OAIHP. Ils ne sont pas des reproches : ce sont des
limites qu'une architecture de 2000 ne pouvait pas anticiper.

| Constat | Symptôme concret | Correction visée |
|---|---|---|
| **EPScript est prisonnier du XML.** | Une seule citation APA tient en un bloc XML de plusieurs kilo-octets, illisible et non testable par morceaux. | EPScript 2.0 : syntaxe propre, typée, composable en fonctions et en blocs, testable unitairement. |
| **La structure imbriquée cache ce qui est réellement actif.** | Pour servir `robots.txt`, EPrints cherche successivement dans le dossier de langue de l'archive, son dossier `static`, les thèmes, les *flavours* et *ingredients*, puis la distribution ; il prend le premier fichier trouvé, puis y ajoute du contenu par configuration. Le résultat peut même varier selon la langue de session. Un dépôt peut ainsi servir, sans le savoir, le fichier par défaut de la distribution, qui interdit `/cgi/` — donc OAI-PMH — aux robots polis, parce qu'on ne voit pas d'un coup d'œil quel fichier gagne. | Garder l'imbrication, mais la rendre visible : pour chaque élément (fichier, phrase, citation, réglage), afficher la valeur effective, la couche d'où elle vient et celles qu'elle masque (voir §4.6). |
| **Les tâches périodiques vivent hors de la plateforme.** | La levée des embargos, les alertes, la génération des vues, du sitemap et des résumés sont des scripts qu'il faut brancher à la main dans le crontab du système. Une file d'événements existe, mais elle ne couvre pas ces tâches. Si une ligne manque ou échoue, rien ne le signale : un embargo peut rester fermé au-delà de sa date sans que personne le sache. | Un planificateur intégré, préconfiguré, visible et surveillé (voir §4.11). |
| **L'ordre de configuration dépend des noms de fichiers.** | Préfixes `z_`, `zz_`, `zzz_`, `zzzz_` pour garantir qu'un réglage passe après un autre. | Dépendances déclarées explicitement ; ordre calculé et affiché. |
| **La configuration n'a pas d'historique.** | Faute d'historique, on garde des copies `.disabled`, `.old` ou `.save` à côté des fichiers actifs. | La configuration est elle-même du contenu versionné, révisable, comparable, réversible. |
| **Le HTML est la représentation première.** | Pas de JSON natif ; négociation de contenu partielle ; la recherche retombe en Atom sans prévenir. | Les données d'abord ; HTML n'est qu'une des représentations (voir §4.1). |
| **Les machines sont des invités imprévus.** | Le signalement FAIR, le JSON-LD, la gestion des robots et les exportations en vrac s'ajoutent par extensions. | Deux audiences de première classe : humains et machines. OAIHP natif. |
| **L'intégrité n'est pas structurelle.** | Les fichiers peuvent être remplacés ; la preuve d'intégrité dépend de procédures externes. | Stockage adressé par contenu, versions immuables, manifestes signés, journal chaîné. |
| **Les entités sont secondaires.** | Les auteurs sont souvent des chaînes de caractères dans une notice plutôt que des entités liées et identifiées. | Tout est entité, tout est lien (voir §4.2). |
| **La relève technique s'amenuise.** | Perl et mod_perl : peu de nouveaux développeurs, outillage vieillissant. | Langage moderne, outillage actuel, contributions facilitées (voir §6). |
| **Étendre demande de connaître les entrailles.** | La puissance des plugins est réelle, mais le chemin pour y arriver est peu documenté. | Interfaces de plugins documentées, typées, avec gabarits, exemples et assistant. |

---

## 4. Les principes — le noyau

Le noyau est ce qui rend le projet crédible. Il doit pouvoir être livré, testé et adopté par un premier
établissement.

### 4.1 Trois couches pour une même page
Chaque page est la superposition de trois couches, chacune pouvant être servie seule :

1. **Les données** — structurées, liées, en formats standards (JSON-LD, RDF, CSL-JSON, DataCite…). C'est ce
   que reçoit un robot ou un agent.
2. **Le cadre** — la mise en ordre de ces données : HTML sémantique, accessible, sans dépendre d'un style.
   C'est ce que reçoivent un lecteur d'écran, un appareil minimal, un navigateur textuel.
3. **La forme** — le style, la mise en page, l'interaction. C'est ce que reçoit un navigateur complet.

Les trois couches sont trois niveaux de complexité de la *même* page, pas trois sites. La négociation de
contenu choisit la bonne. Le robot n'a plus besoin de « gratter » le HTML : on lui donne directement la
couche qui lui est destinée.

### 4.2 Tout est entité, tout est lien
Notices, documents, personnes, organisations, projets, financements, événements, lieux, concepts : chacun est
une entité de premier rang, avec un **identifiant unique, pérenne, standardisé et personnalisable**
(gabarits d'identifiants locaux, et alignement sur DOI, Handle, ARK, ORCID, ROR, ISNI, Wikidata).
Chaque contenu est relié à ses responsables, aux entités qu'il mentionne, à ses sources, à ses versions et à
ses descendants. Le dépôt devient un **graphe de connaissances** qu'on peut parcourir de multiples façons —
à la manière de Wikidata, mais avec l'autorité éditoriale d'une institution.

### 4.3 L'intégrité par construction
- **Rien ne se perd.** Le stockage est en ajout seul : une modification crée une version, elle n'écrase pas.
  Le format de stockage suit une norme de préservation ouverte (OCFL), lisible sans la plateforme.
- **Rien ne se corrompt sans qu'on le sache.** Chaque fichier et chaque version porte son empreinte ; les
  manifestes sont signés ; le journal des changements est chaîné par empreintes.
- **Tout se prouve.** Pour chaque affirmation, on remonte facilement à la source, à la version, à la personne
  ou à l'instance qui l'a validée.
- **Le retrait reste possible.** Le chaînage porte sur les empreintes et non sur les contenus : on peut
  retirer un contenu (embargo, droit, erreur) sans briser la preuve d'intégrité du reste.
- **Chaque état du cycle de vie a un nom et se dit.** Une notice n'est pas seulement « présente » ou
  « supprimée ». Elle peut être en ligne, **retirée provisoirement** (renvoyée en révision, en correction, en
  examen de droits, et appelée à revenir), retirée définitivement, ou supprimée. Chaque transition est un
  événement daté, motivé et publié : les humains voient une page qui explique la situation, les machines
  reçoivent un signal clair (cesser de diffuser, attendre le retour éventuel), et la remise en ligne est
  annoncée à son tour. Aujourd'hui, une notice renvoyée en révision disparaît sans explication, ou apparaît
  comme supprimée d'un côté et inconnue de l'autre.
- **Source et dérivé ne se confondent pas.** Un texte extrait d'un PDF est une transformation déclarée
  (outil, version, date), jamais présentée comme l'original.

### 4.4 EPScript 2.0
Le même principe qu'aujourd'hui — un seul langage partout — avec ce qui lui manque :
- une **syntaxe propre**, lisible hors du XML, et un **typage** qui détecte les erreurs avant la mise en ligne ;
- des **fonctions et modules** réutilisables (une citation APA devient une composition de petits blocs
  testés, partagés entre établissements) ;
- une **exécution isolée** (bac à sable), sans accès arbitraire au système ;
- une **compilation vers une cible portable** (par exemple WebAssembly), pour que le même script s'exécute
  sur le serveur et dans le navigateur — ce qui permet l'aperçu en direct ;
- un **éditeur visuel par blocs** : la structure se voit, se déplace, se teste ; chaque modification
  compile en direct et montre le rendu sur des notices réelles ; le texte et les blocs restent deux vues
  synchronisées du même script ;
- une **ponctuation magique généralisée**, héritière du `magicstop` d'EPrints : dans une citation, les espaces
  superflus disparaissent, un point n'est jamais doublé (ni ajouté après « ? » ou « ! »), une ponctuation est
  suivie d'une espace sauf si la donnée en contient déjà une, les crochets et parenthèses vides et les séparateurs
  orphelins s'effacent, et les URL et DOI restent intacts. On écrit la règle bibliographique, pas chaque cas
  particulier ; la programmation fine (espaces visibles, contrôle de chaque caractère) reste possible quand on en
  a besoin ;
- une **traduction automatique** depuis EPScript 1, pour que les citations et workflows existants migrent.

### 4.5 Des plugins de plugins, mieux encadrés
La philosophie est conservée ; le cadre change :
- chaque plugin déclare ce qu'il fournit, ce qu'il requiert, ses **permissions**, sa version et ses
  dépendances — l'ordre de chargement est calculé, jamais deviné ;
- les plugins peuvent s'exécuter en **bac à sable** et, idéalement, être écrits dans plusieurs langages ;
- la mise à jour est **incrémentale et automatique**, avec retour arrière possible, sans réinstaller la
  plateforme.

### 4.6 La configuration est du contenu
La configuration vit dans la plateforme comme le reste : versionnée, commentable, comparable, testable dans
un espace d'essai, promue en production par une action explicite, réversible. Plus de fichiers `.old`.
Les fichiers restent exportables et importables, pour ceux qui préfèrent travailler avec git.

**Voir d'un coup d'œil.** L'imbrication des couches (distribution, extensions, thème, archive, langue) est une
force : elle permet de surcharger sans copier. Mais elle doit être lisible. La plateforme offre un **explorateur de
configuration** qui, pour chaque élément, montre :
- la **valeur effective** réellement servie ;
- la **couche** d'où elle vient, et les couches qu'elle masque ;
- les **variantes** (par langue, par type de contenu, par public) ;
- les **éléments sensibles** mis en évidence d'office : politique des robots, droits, accès, sécurité.

C'est l'équivalent, pour la configuration d'un dépôt, de l'inspecteur de styles d'un navigateur. Un
administrateur doit pouvoir répondre en quelques secondes à « quel `robots.txt` est servi, et pourquoi ? ».
L'installation de base, elle, reste **plate et minimale** : un dépôt neuf ne contient que ce qu'il utilise, et
chaque couche ajoutée est déclarée, nommée et réversible.

### 4.7 Deux audiences, un même contenu
Humains et machines sont des publics de première classe. La plateforme implémente nativement le protocole
OAIHP : signalement FAIR, JSON-LD, ResourceSync, flux d'événements, exportations en vrac, texte intégral
structuré, recherche pour agents, serveur MCP, politique de débit lisible par les machines. Elle accueille les
robots polis, oriente les autres vers les accès officiels et contient les abusifs — sans jamais fermer l'accès
ouvert.

### 4.8 Des permissions évoluées
Rôles composables, permissions par collection, par type, par champ et par étape de workflow ; délégation ;
journal d'audit de chaque action sensible. Une interface de configuration agréable n'a de sens que si elle
est sûre.

### 4.9 L'assistant IA : au service, jamais aux commandes
- **Pour les administrateurs** : il repère les problèmes (configuration incohérente, métadonnées manquantes,
  identifiants invalides, non-conformité OAIHP), explique, propose des modifications sous forme de
  changements révisables. **Rien n'est appliqué sans validation humaine.**
- **Pour les déposants** : il aide à remplir, signale les incohérences, suggère des liens vers des entités.
- **Pour les lecteurs** : il aide à chercher, propose des pistes, et **apprend à chercher** plutôt que de
  chercher à la place.
- Il cite toujours ses sources dans le dépôt, et peut être désactivé ou remplacé (aucune dépendance à un
  fournisseur unique).

### 4.10 Les standards, toujours
Formats ouverts, bien documentés, libres. Le logiciel lui-même est documenté comme ses données. Aucun format
propriétaire au cœur ; chaque donnée est exportable intégralement, à tout moment.

### 4.11 Un planificateur intégré, prêt dès l'installation
Les tâches périodiques font partie de la plateforme, pas du système d'exploitation :
- **Activé par défaut, avec tout le nécessaire préconfiguré** : levée des embargos, alertes, régénération des
  vues et du sitemap, indexation, vérification d'intégrité des fichiers (empreintes), production des exportations
  et des snapshots OAIHP, purge du journal selon la rétention déclarée, vérification des liens et des
  identifiants, autotest de conformité OAIHP. Un dépôt neuf fonctionne correctement sans toucher au crontab.
- **Visible** : un tableau de bord montre pour chaque tâche la dernière et la prochaine exécution, la durée,
  le résultat et l'historique ; on peut la lancer à la main, la suspendre ou changer sa fréquence.
- **Surveillé** : un échec ou un retard alerte les administrateurs ; une tâche manquée pendant un arrêt est
  rattrapée au redémarrage.
- **Sûr** : une tâche ne s'exécute qu'une fois, même sur plusieurs serveurs (verrou) ; les tâches lourdes
  respectent des fenêtres et des priorités pour ne pas ralentir le service aux humains.
- **Extensible** : un plugin déclare ses tâches et leur fréquence par défaut ; une tâche simple peut s'écrire en
  EPScript 2.0, avec le même éditeur et le même historique que le reste de la configuration.

**Les tâches sont des plugins `Cron` à part entière.** Comme il existe des plugins d'importation, d'exportation,
d'écran ou de conversion, il existe une famille de plugins `Cron`. Chaque fonction essentielle aujourd'hui
confiée à un script du crontab (lever les embargos, envoyer les alertes, générer les vues, le sitemap et les
résumés, vérifier les empreintes, produire les snapshots OAIHP…) devient un plugin `Cron` livré avec la
plateforme. Un plugin `Cron` déclare :
- sa **fréquence par défaut** et sa fenêtre d'exécution, que l'administrateur peut surcharger ;
- ses **dépendances** (« après la génération des vues ») et ses **conflits** (« jamais en même temps que la
  réindexation complète ») ;
- les **permissions** dont il a besoin et les **données** qu'il touche ;
- s'il est **idempotent** (sûr à relancer) et comment il **rattrape** une exécution manquée ;
- ce qu'il **produit** : un rapport, des événements, des fichiers, des alertes.

Le même plugin se lance par le planificateur, à la main depuis l'interface ou en ligne de commande, avec le même
résultat et le même journal. Et comme pour les autres familles, un plugin `Cron` peut en **spécialiser** un
autre : un établissement ajoute une notification aux auteurs à la levée des embargos sans réécrire la tâche
d'origine. Les scripts qu'on duplique et renomme (`lift_embargos_orig`, `generate_views` copié pour une tentative)
disparaissent : on surcharge, on versionne, on revient en arrière.

**Chaque tâche et chaque événement a son écran et sa configuration.** Un plugin `Cron` ou `Event` déclare un
**schéma de configuration** (paramètres, types, valeurs par défaut, bornes, aide). La plateforme en tire
automatiquement :
- un **écran de configuration** dans l'administration, validé, avec l'aide en ligne, la valeur effective et la
  couche d'où elle vient (§4.6) ;
- un **écran de suivi** : file d'attente, exécutions en cours et passées, durée, erreurs, données touchées,
  bouton pour relancer ou suspendre ;
- des **permissions** propres : qui peut voir, configurer, lancer.

Le plugin peut fournir son propre écran quand l'écran généré ne suffit pas, mais il n'en a pas besoin pour
exister. Aucune tâche ni aucun événement n'est invisible ou configurable seulement en éditant un fichier.

### 4.12 Rien de caché
Le principe porte sur **le fonctionnement**, pas sur les contenus. Tout ce qui détermine le comportement du dépôt
est visible et consultable, au moins par les administrateurs. Rien ne fonctionne « dans le dos » de ceux qui en
sont responsables :
- **la configuration effective** : chaque réglage, sa valeur, sa provenance, y compris les valeurs par défaut
  codées dans la plateforme (qui ne doivent pas exister sans être affichées) ;
- **les plugins** chargés, leur version, leur ordre, ce qu'ils surchargent et ce qu'ils désactivent ;
- **les tâches et les événements** : planifiés, en cours, en échec, avec leur historique ;
- **les règles d'accès** : qui peut voir quoi, et pourquoi telle personne ou tel robot obtient telle réponse ;
- **ce que voient les machines** : le `robots.txt` réellement servi, les politiques annoncées, les limites de débit
  appliquées, les réponses données à chaque famille de robots ;
- **les caches et les index** : leur état, leur fraîcheur, ce qu'ils couvrent et ce qu'ils excluent (par exemple : un index plein texte inclut-il des documents sous embargo ?) ;
- **les journaux** : erreurs, actions sensibles, changements de configuration, propositions et actions de
  l'assistant IA (voir ci-dessous) ;
- **le traitement des données** : pour chaque objet, son état (en ligne, sous embargo, restreint, confidentiel,
  retiré provisoirement, retiré, supprimé), les règles qui s'y appliquent et l'historique de ses changements
  d'état sont visibles. On sait toujours *pourquoi* un contenu est ou n'est pas accessible.

**Les journaux se consultent dans la plateforme.** Aujourd'hui, comprendre une panne d'EPrints demande un accès
au serveur : journal d'erreurs d'Apache, journal de l'indexeur, sorties du crontab envoyées par courriel, file
d'événements en base. Dans le successeur, les journaux de toutes les composantes sont réunis dans un écran
d'administration :
- **unifiés** : requêtes, erreurs, tâches, événements, indexation, plugins, décisions d'accès et de débit, au
  même endroit et dans un même format structuré ;
- **cherchables et filtrables** : par période, niveau, composante, plugin, notice, utilisateur ou famille de
  robots ;
- **reliés** : un identifiant de requête suit une action de bout en bout (la requête, le plugin appelé, la tâche
  déclenchée, l'erreur produite) ;
- **en direct** : on peut suivre le journal pendant qu'on reproduit un problème ;
- **exportables** vers les outils de l'établissement (formats ouverts, protocoles standards), sans en dépendre ;
- **sobres** : les données personnelles sont minimisées, la durée de conservation est affichée et respectée,
  et la consultation des journaux est elle-même journalisée.

Aucun accès SSH ne doit être nécessaire pour savoir ce qui se passe dans son dépôt.

**Les contenus, eux, suivent leurs propres règles d'accès.** Un document sous embargo, confidentiel ou réservé
n'est pas « ouvert » parce que la configuration l'est : il reste protégé, y compris, selon les cas, des
administrateurs eux-mêmes. La transparence porte sur les règles et leur application, jamais sur le contenu
protégé.

Les secrets (mots de passe, clés) ne sont jamais affichés en clair, mais leur **existence**, leur usage et leur
date de rotation le sont. La transparence a des niveaux (le public voit moins que les administrateurs) ; elle n'a
pas de zones d'ombre.

### 4.13 Alertes, messages et autodiagnostic
La plateforme communique dans les deux sens, et elle sait dire d'elle-même quand quelque chose ne va pas.

**Envoyer des messages aux utilisateurs.** Déposants, auteurs, réviseurs, abonnés, administrateurs reçoivent ce
qui les concerne : dépôt reçu, demande de correction, notice renvoyée en révision ou remise en ligne, levée d'un
embargo, nouvelle publication correspondant à une recherche sauvegardée, maintenance annoncée.
- **Canaux** : messagerie intégrée, courriel, bandeau sur le site, et des protocoles ouverts pour les machines
  (COAR Notify, ActivityStreams, webhooks).
- **Préférences** : chaque personne choisit ce qu'elle reçoit, par quel canal, à quelle fréquence (immédiat ou
  résumé).
- **Gabarits** : les messages s'écrivent en EPScript 2.0 et en phrases traduites, avec le même éditeur et le même
  historique que le reste de la configuration.
- **Traçabilité** : chaque envoi est journalisé (§4.12) ; on sait qui a été prévenu de quoi, et quand.

**Recevoir les alertes du système.** Une tâche en échec, une file d'événements qui s'allonge, un disque qui se
remplit, un certificat qui expire, une empreinte de fichier qui ne correspond plus : l'alerte arrive aux bonnes
personnes, avec sa gravité, au lieu d'attendre qu'un utilisateur signale la panne.

**S'autodiagnostiquer.** Des vérifications de santé tournent en continu, comme plugins `Cron` (§4.11) :
- **fonctionnement** : base de données, indexeur, file d'événements, espace disque, tâches en retard ;
- **intégrité** : empreintes des fichiers, cohérence des droits d'accès, embargos échus mais non levés ;
- **cohérence** : configuration contradictoire, plugins en conflit, phrases manquantes, identifiants invalides ;
- **accès des machines** : le `robots.txt` servi correspond-il à la politique voulue ? OAI-PMH répond-il de façon
  cohérente ? Les chemins machine sont-ils servis sans défi du pare-feu ? Le niveau OAIHP revendiqué est-il
  toujours atteint ?

Chaque diagnostic dit **ce qui ne va pas, pourquoi c'est important, et comment le corriger**, avec un lien vers
le réglage concerné ; l'assistant IA peut l'expliquer et proposer un correctif (§4.9). Une page d'état résume la
santé du dépôt : détaillée pour les administrateurs, sommaire pour le public.

EPrints en contient déjà les germes (alertes sur recherches sauvegardées, signalement de problèmes dans les
notices) : il s'agit de les généraliser et de les réunir.

### 4.14 Une passerelle d'administration à part
L'administration n'est pas une page de plus sur le site public.
- **Une adresse distincte** (nom d'hôte, voire port ou réseau dédiés), servie par un processus séparé avec ses
  propres privilèges. Le site public ne contient aucune fonction d'administration : un défaut sur le site public
  ne donne pas accès à l'administration.
- **Un accès contrôlé finement côté réseau** : réseau de l'établissement ou VPN, listes d'adresses, certificats
  clients (TLS mutuel), selon la politique de chaque établissement. Le pare-feu applicatif du site public et
  celui de l'administration ont des règles différentes, adaptées à leurs publics.
- **Une authentification forte** : authentification unique de l'établissement, double facteur obligatoire, clés
  matérielles (WebAuthn) recommandées, sessions courtes, ré-authentification pour les actions sensibles.
- **Un accès d'urgence documenté** (« bris de glace »), journalisé et signalé à tous les administrateurs.
- **Tout y est journalisé** : chaque consultation et chaque action (§4.12).

Les déposants et réviseurs continuent de travailler sur le site public, avec leurs propres rôles (§4.8) ; la
passerelle est réservée à ce qui touche le fonctionnement de la plateforme.

**Un tableau de bord de toutes les fonctions critiques.** La passerelle affiche l'état de chaque composante :
serveur web, base de données, moteur de recherche et index, stockage des fichiers et sauvegardes, file
d'événements et planificateur, caches, courriel, certificats TLS, pare-feu applicatif, ressources du système
(processeur, mémoire, disque). Pour chacune : disponible ou non, charge, temps de réponse, version, mises à jour
de sécurité en attente, dernières erreurs, et le lien vers le diagnostic (§4.13). Les mesures s'exportent dans
des formats ouverts (OpenTelemetry, Prometheus) pour les outils de supervision de l'établissement, sans en
dépendre.

**Le choix de la base de données.** EPrints s'appuie surtout sur MySQL ou MariaDB (des pilotes PostgreSQL et
Oracle existent, moins utilisés). Pour le successeur, **PostgreSQL** est le candidat naturel : transactions et
intégrité solides, types JSON indexables, recherche plein texte intégrée, requêtes récursives pour parcourir le
graphe d'entités, sécurité au niveau des lignes, réplication éprouvée, licence libre et communauté large. Le
principe compte davantage que le produit :
- **une seule source de vérité** : la base (métadonnées, entités, événements) et le stockage de préservation
  (fichiers, OCFL) ;
- **tout le reste est dérivé et reconstructible** : index de recherche, point d'accès SPARQL, caches,
  exportations, snapshots OAIHP. On peut les détruire et les reconstruire sans rien perdre.

---

### 4.15 La structure en XML, les espaces de noms rendus lisibles, les données liées en relief

EPScript 2.0 ne vit pas seul : il s'inscrit dans une **structure**. Pour les pages, les citations et les gabarits,
XML reste le bon squelette. Il est hiérarchique, validable par schéma, transformable, et c'est déjà le modèle
d'EPrints : la structure est en XML et les expressions en EPScript. Nous le gardons.

Les **espaces de noms** sont indispensables, parce que plusieurs jeux de schémas décrivent la même notice et se
chevauchent : schema.org, Dublin Core, BIBO, FOAF, PROV, DataCite, et les vocabulaires propres au dépôt. Leur
coût tient surtout à la présentation : déclarations en tête de fichier, préfixes à retenir, IRI illisibles.
L'éditeur doit donc **les montrer sans les imposer** :
- les déclarations sont repliées dans une légende ;
- chaque préfixe a une couleur stable, partagée par le code, les blocs et les vues de données ;
- l'IRI complète s'affiche au survol ;
- la complétion propose les termes du bon vocabulaire selon le contexte ;
- la vérification signale un terme inconnu dans son espace de noms, comme elle signale un champ inconnu.

Avec les **données liées**, la troisième dimension prend un sens. Chaque vocabulaire devient un **plan**. Une même
ressource (la notice, une personne, un établissement) occupe la même place sur chaque plan où elle est décrite,
et un axe vertical relie ses descriptions : c'est son **identité**. Les **sources externes** (ORCID, ROR,
Crossref, Wikidata, les autres dépôts) se placent autour, reliées par `owl:sameAs` ou des identifiants partagés.
On voit d'un coup d'œil :
- quel schéma décrit quoi ;
- où les schémas se recouvrent et où ils se contredisent ;
- ce qui ne vient que de l'extérieur.

La même vue sert à l'administrateur qui vérifie un mappage, au catalogueur qui complète une notice et au robot
qui doit savoir quoi moissonner (§4.7). Elle reste une **vue** : les données sont stockées une seule fois (§4.3),
et chaque plan est une projection reconstructible.

## 5. L'horizon — ce qui rend le projet désirable

L'horizon n'est pas requis pour une première version, mais le noyau doit le rendre possible.

- **Le méta-dépôt.** Grâce à la récursivité, un dépôt peut en contenir d'autres — du macro (consortium,
  réseau national) au micro (laboratoire, projet, cours) — et des dépôts peuvent aussi coexister en parallèle
  et se fédérer. Chacun garde son autorité ; le graphe les relie.
- **Des producteurs vérifiés.** Les créateurs — scientifiques, artistes ou autres — sont authentifiés
  (ORCID, identités institutionnelles, attestations vérifiables), et leurs contributions le sont aussi.
- **Des communautés de pratique.** Les personnes liées aux contenus peuvent communiquer entre elles de façon
  sécurisée, autour des œuvres et des données, en s'appuyant sur des protocoles ouverts et fédérés
  (COAR Notify, ActivityStreams) plutôt que sur une plateforme fermée.
- **Une diffusion polyvalente.** Texte, données, images, son, vidéo, objets 3D : une bibliothèque numérique
  qui diffuse tous les formats, chacun avec ses dérivés accessibles (transcriptions, descriptions).
- **La puissance des données liées.** Parcourir, questionner, visualiser, mesurer : le graphe permet de
  montrer ce qu'un établissement produit, avec qui, et à partir de quoi.

En une phrase : **un croisement entre EPrints, Wikidata, une plateforme de diffusion et un réseau
professionnel, dont le contenu est vérifiable par sa structure même.**

---

## 6. Le choix technologique — des critères avant un nom

Le langage sera choisi par un groupe de travail sur des critères explicites, pas par préférence. Les critères :

1. **Longévité** : un écosystème qui existera encore dans vingt ans, et des normes de compatibilité.
2. **Relève** : un bassin de développeurs, y compris dans les bibliothèques et les universités.
3. **Sûreté** : typage, gestion de la mémoire, isolement des extensions.
4. **Performance** sur le service de fichiers et l'indexation.
5. **Accessibilité** pour les non-programmeurs, par EPScript 2.0 plutôt que par le langage hôte.

Pistes à évaluer (non exhaustives) :

| Piste | Atouts | Points d'attention |
|---|---|---|
| Rust pour le noyau, WebAssembly pour EPScript et les plugins | Sûreté, performance, isolement natif des extensions, même exécution serveur et navigateur | Courbe d'apprentissage ; bassin de contributeurs plus restreint |
| TypeScript de bout en bout | Un seul langage du navigateur au serveur ; immense écosystème | Renouvellement rapide de l'écosystème ; dette de dépendances |
| Python | Très présent en recherche et en bibliothèques (InvenioRDM) | Performance ; isolement des extensions plus difficile |
| Go | Simplicité, déploiement, longévité | Moins expressif pour un langage de configuration embarqué |

Un principe tranche une partie du débat : **si EPScript 2.0 et les plugins s'exécutent dans un bac à sable
portable, le langage du noyau importe moins pour la communauté.** Les établissements écrivent de l'EPScript
et des plugins ; seuls les mainteneurs du noyau écrivent le langage hôte.

### 6.1 Un langage d'un genre nouveau

Le langage qui compte vraiment pour la communauté n'est donc pas le langage hôte, mais **EPScript 2.0**, et il
doit être d'un genre nouveau. Il ne s'agit pas d'inventer une syntaxe de plus, mais de réunir des propriétés
qu'aucun langage courant ne réunit :

- **Conscient du modèle de données** : ses types viennent du schéma du dépôt. Écrire `item.titel` est une erreur
  signalée avant la mise en ligne, avec la suggestion `title` (démontré dans le prototype de l'atelier).
- **Effets déclarés** : chaque script, plugin ou tâche déclare ce qu'il lit, ce qu'il écrit, s'il envoie un
  message ou contacte le réseau. Ce n'est pas une convention : le moteur l'impose. Les permissions (§4.8), la
  transparence (§4.12) et la sécurité découlent alors du code lui-même, au lieu d'être documentées à côté.
- **Isolé par construction** : exécution en bac à sable, limites de temps et de mémoire, aucun accès implicite.
- **Deux vues, un seul programme** : le texte et les blocs visuels sont deux représentations du même script,
  synchronisées ; un non-programmeur et un développeur travaillent sur le même objet.
- **Vivant** : compilé et vérifié à chaque frappe, exécuté sur de vraies notices, avec le rendu sous les yeux.
  Chaque erreur s'affiche à la ligne fautive, une flèche sous la colonne où elle est détectée. La portée en cours
  d'écriture (le « si », le « selon » ou la fonction où se trouve le curseur) reste nette, et le reste s'estompe,
  dans le texte comme dans les blocs. Les blocs suivent la mise en page du texte au lieu d'en être une autre
  représentation.
- **Auto-documenté** : la documentation fait partie du langage (description, exemples, cas limites), et les
  exemples sont exécutés comme des tests. Une fonction sans documentation ni exemple est signalée.
- **Lisible par les humains et par les machines** : une sémantique petite et explicite, sans magie, que les
  assistants IA peuvent lire, expliquer, vérifier et proposer de modifier — sous contrôle humain (§4.9).
- **Versionné et traçable** : chaque script a son historique, sa provenance et ses dépendances.
- **Portable** : compilé vers une cible ouverte (par exemple WebAssembly), il s'exécute sur le serveur, dans le
  navigateur pour l'aperçu, et dans d'autres implémentations de la plateforme.

Le langage hôte du noyau sera choisi, lui, selon les critères ci-dessus. Mais il devra servir ce langage-là :
c'est EPScript 2.0 qui portera l'héritage d'EPrints et la promesse faite aux non-programmeurs.

**Vers un langage naturel structuré et multilingue.** L'horizon d'EPScript 2.0 va plus loin : un **langage
naturel structuré, multilingue**, où une règle s'écrit et se lit comme une phrase — en français, en anglais ou
dans une autre langue — tout en restant un programme exact, vérifiable et exécutable. Une même structure
abstraite, plusieurs langues de surface : la règle écrite en français par une bibliothécaire se lit en anglais
par un collègue, s'affiche en blocs pour qui préfère, et s'exécute de la même façon partout.

EPrints en contient déjà le germe : ses fichiers de phrases multilingues et ses expressions EPScript vivent
côte à côte dans les mêmes gabarits. Ce langage fait l'objet d'un **projet préalable**, PolyGlotML, dont les bases
existent ; EPScript 2.0 en serait le premier domaine d'application. Des travaux voisins méritent d'être étudiés
comme appuis. Le plus direct est **AppleScript**, qui a existé dans les années 1990 en plusieurs « dialectes »
(anglais, français, japonais) : le script était conservé sous une forme compilée neutre et affiché dans la langue
de chaque utilisateur. Le principe a donc déjà fonctionné ; il reste à le généraliser. S'y ajoutent les grammaires multilingues à syntaxe abstraite commune (Grammatical Framework), les langues
naturelles contrôlées (Attempto Controlled English), le contenu abstrait rendu en plusieurs langues
(Wikifunctions / Abstract Wikipedia) et les langages de programmation aux mots-clés traduits (Hedy).

**Où s'arrête la langue naturelle.** Tout le logiciel n'a pas à s'écrire en langue naturelle. Le **noyau**
(stockage, réseau, sécurité, exécution en bac à sable) reste écrit dans un langage de programmation classique,
par une petite équipe de mainteneurs. La langue naturelle structurée vise **ce que les établissements
personnalisent** : leurs règles. On commence par EPScript, c'est-à-dire les citations et les rendus, là où le
besoin est le plus net et le vocabulaire le plus fermé. Le même langage gagne ensuite, domaine par domaine :

1. citations et rendus ;
2. validations du dépôt (« un mémoire doit avoir un directeur ») ;
3. règles d'accès, d'embargo et de diffusion ;
4. flux de travail et révision ;
5. tâches planifiées et conditions d'alerte (§4.11, §4.13) ;
6. politiques envers les robots (OAIHP).

Chaque étape ajoute un vocabulaire tiré du schéma et des effets déclarés. Elle n'ajoute pas un nouveau langage.

**Trois manières d'écrire une règle**, toutes ramenées à la même structure neutre :
- **par l'exemple** : on écrit la référence voulue pour quelques notices, et la plateforme en déduit la règle, à
  ajuster ensuite (démontré dans l'atelier) ;
- **avec une prédiction de code** : d'abord exacte, tirée de la grammaire et du schéma (champs, méthodes selon le
  type, cas non couverts). Ensuite, une prédiction de la suite tirée de l'**index du code lui-même** et d'une
  **petite base de formes courantes** (« (année). », volume(numéro), préfixe du DOI…). Elle est calculée sur la forme
  neutre, si bien qu'une habitude prise dans une langue sert dans les autres (démontré dans l'atelier). Enfin, un
  petit modèle local, contraint par la grammaire et le vérificateur de types, pour
  ne proposer que du code qui compile. Ce modèle est entraîné sur la forme neutre, donc il est valable dans toutes
  les langues ;
- **directement**, en phrases, en blocs ou en texte.

### 6.2 Une documentation à la hauteur

La plateforme de documentation se choisit avec le langage, car chaque écosystème a ses outils naturels
(rustdoc et mdBook pour Rust, TypeDoc et Starlight ou Docusaurus pour TypeScript, Sphinx ou MkDocs pour Python,
pkg.go.dev pour Go). Quel que soit ce choix, elle doit respecter quelques principes :

- **Générée à la source** : la référence des plugins, des schémas de configuration, des fonctions EPScript, des
  tâches et des points d'accès est produite à partir du code et des schémas, jamais recopiée à la main.
- **Exemples exécutés** : chaque exemple est testé à chaque version ; un exemple faux fait échouer la
  publication.
- **Intégrée à la plateforme** : l'aide de chaque écran, de chaque réglage et de chaque fonction s'ouvre là où
  on en a besoin, et les exemples peuvent s'exécuter sur les données du dépôt (comme dans l'atelier).
- **Trois publics** : les personnes qui configurent (guides, recettes), les développeurs (référence,
  architecture, ADR), et les machines (OpenAPI, JSON Schema, descriptions MCP, `llms.txt`).
- **Versionnée avec le logiciel**, avec une page par version et ce qui a changé.
- **Bilingue au minimum** (français et anglais), traduisible, ouverte aux contributions.
- **Servie par la plateforme elle-même** : la documentation est un ensemble de contenus versionnés, liés et
  citables dans un dépôt EPrints du futur. Le projet utilise ce qu'il construit.

---

## 7. Ce que nous ne ferons pas

- **Pas de flafla.** Chaque fonction doit justifier sa complexité. La simplicité relative d'EPrints est une
  qualité à préserver.
- **Pas d'enfermement.** Ni dans un fournisseur d'infonuagique, ni dans un modèle d'IA, ni dans un format.
- **Pas de chaîne de blocs.** L'intégrité s'obtient avec des empreintes, des signatures et un journal chaîné,
  des techniques éprouvées et sobres.
- **Pas d'IA qui décide.** L'assistant propose ; les humains disposent.
- **Pas de rupture brutale.** Aucun établissement ne doit avoir à tout reconstruire pour migrer.

---

## 8. Le chemin depuis EPrints 3

1. **Aujourd'hui** — le plugin OAIHP pour EPrints 3.3/3.4 rend les dépôts existants lisibles par les machines
   et mesure l'effet. Il révèle aussi précisément ce qu'EPrints ne peut pas faire.
2. **Demain** — un prototype de la nouvelle plateforme lit les données d'un dépôt EPrints (export XML
   EPrints, OAI-PMH, OAIHP) et les sert selon les trois couches. EPScript 1 est traduit automatiquement en
   EPScript 2.0.
3. **Ensuite** — un établissement pilote fait tourner les deux plateformes en parallèle, puis bascule.
   Les URL et identifiants existants sont préservés.

---

## 9. Appel

Nous invitons les responsables de dépôts, bibliothécaires, archivistes, chercheuses et chercheurs,
développeuses et développeurs, communautés linguistiques et organismes de normalisation à :

- **critiquer** ce manifeste — ses angles morts comptent autant que ses idées ;
- **partager** leurs propres constats, avec des exemples concrets ;
- **contribuer** au protocole OAIHP, au plugin EPrints et aux prototypes ;
- **rejoindre** le groupe de travail qui choisira l'architecture.

EPrints nous a appris qu'un dépôt peut être simple et puissant à la fois. À nous de prouver que cela reste
vrai pour les vingt-cinq prochaines années.

---

*Voir aussi : [`comparaison-plateformes.md`](comparaison-plateformes.md) — EPrints face aux autres
plateformes ; le protocole OAIHP et le paquet `oaihp-eprints` pour EPrints 3, publiés séparément.*
