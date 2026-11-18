[Français](MANIFESTE.md) · **English**

# EPrints du futur: Manifesto

*Draft 0.1, November 2026. Working document, open to contributions.*

> For twenty-five years, EPrints has shown that an institutional repository can be simple, configurable and
> extensible without being a software monster. This manifesto proposes a natural successor for it:
> a platform that keeps what EPrints does best, fixes what time has revealed, and prepares
> for a world where machines read more than humans do.

---

## 1. Why now

**The audience has changed.** In institutional repositories, robots and agents already account for the great majority
of traffic, according to measurements that are still partial. Repositories were designed for human readers who
browse pages. Today they are harvested, re-crawled, summarized and cited by systems that need neither our menus
nor our facets. What these systems do need, vitally, is data that is accurate, versioned and provable. We can
put up with this traffic, or we can make it our first distribution channel.

**Expectations have changed.** Persistent identifiers for every entity, linked data, FAIR Signposting,
documented APIs, verifiable integrity, accessibility, visual editing: what used to be optional is now
the baseline.

**The technical foundation has aged.** Perl and mod_perl have carried EPrints admirably, but the next generation
of developers is getting scarce, and the gap keeps widening between what the platform does natively and what
institutions have to cobble together around it. The OAIHP project, which accompanies this manifesto, demonstrates
this: almost every requirement of a "machine-readable" repository has to be added to EPrints through a plugin,
a patch or a rewrite rule.

The time is right because not much is missing. The fundamental ideas of EPrints are sound. They need a language,
standards and an architecture of their time.

---

## 2. What we keep: the strengths of EPrints

A successor that forgot what gives EPrints its value would be just one more platform. Here is what must
survive, and even grow stronger.

### 2.1 EPScript: one language, everywhere
In EPrints, the same small expression syntax is used for citations, record pages, translated phrases,
deposit workflows, validation rules and exports. A librarian who can write
`type = 'thesis' and date.is_set()` can change almost the whole site. **This unity is the greatest strength
of EPrints.** It is rare: most platforms separate templates, configuration and code, and reserve each one for
a different audience.

### 2.2 A simple data model
Datasets, objects, typed fields. A field can be multiple, compound, or contain other objects. This model fits
on one page, and yet it describes theses, datasets, patents, works and people.

### 2.3 Recursion
Documents live inside records, compound fields contain fields, objects contain other objects. This recursion
is the source of many headaches, but it opens possibilities that few platforms offer: nesting content, and in
time nesting **entire repositories inside other repositories**.

### 2.4 Plugins of plugins
Import, export, screens, conversions, events, search: almost everything is a plugin, and a plugin can
specialize another one. With a little guidance, you can do just about anything.

### 2.5 Configuration rather than code
A large part of a repository's behaviour is adjusted through declarative files, without touching the core.

### 2.6 A wide range of formats
EPrints already speaks many standards: Dublin Core, METS, MODS, DataCite, RDF, BibTeX, EndNote, OAI-PMH,
SWORD, and many others depending on the installed extensions.

### 2.7 A community that configures
The best improvements to EPrints do not always come from programmers. They often come from repository
managers who learned how to configure it. This culture is an asset.

---

## 3. What we fix: lessons from experience

The findings below come from the daily operation of EPrints repositories in production and from the work of the OAIHP project. They are not criticisms. They are
limits that an architecture from 2000 could not anticipate.

| Finding | Concrete symptom | Intended fix |
|---|---|---|
| **EPScript is trapped in XML.** | A single APA citation takes a block of XML several kilobytes long, unreadable and impossible to test piece by piece. | EPScript 2.0: a clean syntax, typed, composable into functions and blocks, unit-testable. |
| **The nested structure hides what is actually active.** | To serve `robots.txt`, EPrints looks in turn in the archive's language folder, its `static` folder, the themes, the *flavours* and *ingredients*, then the distribution. It takes the first file found, then appends content to it through configuration. The result can even vary with the session language. A repository can thus serve the distribution default without knowing it, which forbids `/cgi/` (and therefore OAI-PMH) to polite robots, because you cannot see at a glance which file wins. | Keep the nesting, but make it visible: for each element (file, phrase, citation, setting), show the effective value, the layer it comes from and the layers it masks (see §4.6). |
| **Periodic tasks live outside the platform.** | Lifting embargoes, alerts, and generating views, the sitemap and abstracts are scripts that must be wired by hand into the system crontab. An event queue exists, but it does not cover these tasks. If a line is missing or fails, nothing reports it: an embargo can stay closed past its date without anyone knowing. | A built-in scheduler, preconfigured, visible and monitored (see §4.11). |
| **Configuration order depends on file names.** | Prefixes `z_`, `zz_`, `zzz_`, `zzzz_` to make sure one setting is applied after another. | Explicitly declared dependencies; order computed and displayed. |
| **Configuration has no history.** | Without history, people keep `.disabled`, `.old` or `.save` copies next to the active files. | Configuration is itself versioned content: reviewable, comparable, reversible. |
| **HTML is the primary representation.** | No native JSON; partial content negotiation; search falls back to Atom without warning. | Data first; HTML is only one of the representations (see §4.1). |
| **Machines are unexpected guests.** | FAIR Signposting, JSON-LD, robot management and bulk exports are added through extensions. | Two first-class audiences: humans and machines. Native OAIHP. |
| **Integrity is not structural.** | Files can be replaced; proof of integrity depends on external procedures. | Content-addressed storage, immutable versions, signed manifests, chained log. |
| **Entities are secondary.** | Authors are often character strings inside a record rather than linked, identified entities. | Everything is an entity, everything is a link (see §4.2). |
| **The technical succession is thinning.** | Perl and mod_perl: few new developers, ageing tooling. | Modern language, current tooling, easier contributions (see §6). |
| **Extending requires knowing the internals.** | The power of plugins is real, but the path to it is poorly documented. | Documented, typed plugin interfaces, with templates, examples and an assistant. |

---

## 4. The principles: the core

The core is what makes the project credible. It must be possible to deliver it, test it and have it adopted by
a first institution.

### 4.1 Three layers for one page
Each page is the superposition of three layers, and each layer can be served on its own:

1. **The data**: structured, linked, in standard formats (JSON-LD, RDF, CSL-JSON, DataCite…). This is
   what a robot or an agent receives.
2. **The frame**: the ordering of that data. Semantic, accessible HTML that does not depend on a style.
   This is what a screen reader, a minimal device or a text browser receives.
3. **The form**: style, layout, interaction. This is what a full browser receives.

The three layers are three levels of complexity of the *same* page. They are not three sites. Content
negotiation picks the right one. The robot no longer needs to "scrape" the HTML: it gets the layer meant
for it directly.

### 4.2 Everything is an entity, everything is a link
Records, documents, people, organizations, projects, funding, events, places, concepts: each one is
a first-rank entity, with a **unique, persistent, standardized and customizable identifier**
(local identifier templates, and alignment with DOI, Handle, ARK, ORCID, ROR, ISNI, Wikidata).
Each piece of content is linked to the people responsible for it, to the entities it mentions, to its sources,
to its versions and to its descendants. The repository becomes a **knowledge graph** that can be browsed in many
ways, much like Wikidata, but with the editorial authority of an institution.

### 4.3 Integrity by construction
- **Nothing is lost.** Storage is append-only: a change creates a version, it does not overwrite.
  The storage format follows an open preservation standard (OCFL), readable without the platform.
- **Nothing is corrupted without anyone knowing.** Each file and each version carries its hash; manifests
  are signed; the change log is chained by hashes.
- **Everything can be proven.** For every statement, you can easily trace back to the source, the version,
  the person or the body that validated it.
- **Withdrawal remains possible.** The chain covers hashes, not content: you can withdraw a piece of content
  (embargo, rights, error) without breaking the proof of integrity for everything else.
- **Every lifecycle state has a name and is stated.** A record is not only "present" or "deleted". It can be
  live, under **provisional withdrawal** (sent back for revision, correction or rights review, and expected to
  return), permanently withdrawn, or deleted. Each transition is a dated, justified and published event: humans
  see a page that explains the situation, machines receive a clear signal (stop distributing, wait for a possible
  return), and the return online is announced in turn. Today, a record sent back for revision disappears without
  explanation, or appears as deleted on one side and unknown on the other.
- **Source and derivative are never confused.** Text extracted from a PDF is a declared transformation
  (tool, version, date), never presented as the original.

### 4.4 EPScript 2.0
The same principle as today (one language everywhere), with what it lacks:
- a **clean syntax**, readable outside XML, and **typing** that catches errors before going live;
- reusable **functions and modules** (an APA citation becomes a composition of small tested blocks,
  shared between institutions);
- **isolated execution** (a sandbox), with no arbitrary access to the system;
- **compilation to a portable target** (for example WebAssembly), so that the same script runs on the server
  and in the browser. This makes live preview possible;
- a **visual block editor**: the structure can be seen, moved and tested; each change compiles live and shows
  the output on real records; text and blocks remain two synchronized views of the same script;
- **generalized magic punctuation**, inheriting from EPrints' `magicstop`: in a citation, extra spaces
  disappear, a full stop is never doubled (nor added after "?" or "!"), punctuation is followed by a space
  unless the data already contains one, empty brackets and parentheses and orphan separators are removed, and
  URLs and DOIs stay intact. You write the bibliographic rule, not every special case; fine-grained programming
  (visible spaces, control over each character) remains possible when you need it;
- **automatic translation** from EPScript 1, so that existing citations and workflows can migrate.

### 4.5 Plugins of plugins, with a better framework
The philosophy is kept; the framework changes:
- each plugin declares what it provides, what it requires, its **permissions**, its version and its
  dependencies. The load order is computed, never guessed;
- plugins can run in a **sandbox** and, ideally, be written in several languages;
- updates are **incremental and automatic**, with rollback, without reinstalling the platform.

### 4.6 Configuration is content
Configuration lives in the platform like everything else: versioned, open to comments, comparable, testable in
a staging space, promoted to production by an explicit action, reversible. No more `.old` files.
Files remain exportable and importable, for those who prefer to work with git.

**See at a glance.** The nesting of layers (distribution, extensions, theme, archive, language) is a strength:
it lets you override without copying. But it must be readable. The platform offers a **configuration
explorer** that shows, for each element:
- the **effective value** actually served;
- the **layer** it comes from, and the layers it masks;
- the **variants** (by language, by content type, by audience);
- **sensitive elements** highlighted by default: robot policy, rights, access, security.

This is the equivalent, for a repository's configuration, of a browser's style inspector. An administrator must
be able to answer "which `robots.txt` is served, and why?" in a few seconds. The base installation, for its part,
remains **flat and minimal**: a new repository contains only what it uses, and every added layer is declared,
named and reversible.

### 4.7 Two audiences, one content
Humans and machines are first-class audiences. The platform natively implements the OAIHP protocol:
FAIR Signposting, JSON-LD, ResourceSync, event streams, bulk exports, structured full text, search for agents,
an MCP server, a machine-readable rate policy. It welcomes polite robots, directs the others to the official
access points and contains the abusive ones, without ever closing open access.

### 4.8 Advanced permissions
Composable roles, permissions by collection, by type, by field and by workflow stage; delegation; an audit log
of every sensitive action. A pleasant configuration interface only makes sense if it is safe.

### 4.9 The AI assistant: in service, never in command
- **For administrators**: it spots problems (inconsistent configuration, missing metadata, invalid identifiers,
  OAIHP non-compliance), explains them, and proposes modifications as reviewable changes. **Nothing is applied
  without human validation.**
- **For depositors**: it helps fill in forms, flags inconsistencies, suggests links to entities.
- **For readers**: it helps them search, suggests leads, and **teaches how to search** rather than searching
  for them.
- It always cites its sources in the repository, and it can be disabled or replaced (no dependency on a single
  provider).

### 4.10 Standards, always
Open, well-documented, free formats. The software itself is documented like its data. No proprietary format at
the core; all data can be exported in full, at any time.

### 4.11 A built-in scheduler, ready from installation
Periodic tasks are part of the platform, not of the operating system:
- **Enabled by default, with everything needed preconfigured**: lifting embargoes, alerts, regenerating views
  and the sitemap, indexing, checking file integrity (hashes), producing exports and OAIHP snapshots, purging
  the log according to the declared retention, checking links and identifiers, OAIHP compliance self-test.
  A new repository works correctly without touching the crontab.
- **Visible**: a dashboard shows, for each task, the last and next run, the duration, the result and the
  history; you can run it by hand, suspend it or change its frequency.
- **Monitored**: a failure or a delay alerts the administrators; a task missed during downtime is caught up on
  restart.
- **Safe**: a task runs only once, even across several servers (lock); heavy tasks respect windows and
  priorities so they do not slow down service to humans.
- **Extensible**: a plugin declares its tasks and their default frequency; a simple task can be written in
  EPScript 2.0, with the same editor and the same history as the rest of the configuration.

**Tasks are full-fledged `Cron` plugins.** Just as there are import, export, screen and conversion plugins,
there is a family of `Cron` plugins. Each essential function that is today entrusted to a crontab script (lifting
embargoes, sending alerts, generating views, the sitemap and abstracts, checking hashes, producing OAIHP
snapshots…) becomes a `Cron` plugin shipped with the platform. A `Cron` plugin declares:
- its **default frequency** and its execution window, which the administrator can override;
- its **dependencies** ("after view generation") and its **conflicts** ("never at the same time as the full
  reindex");
- the **permissions** it needs and the **data** it touches;
- whether it is **idempotent** (safe to rerun) and how it **catches up** on a missed run;
- what it **produces**: a report, events, files, alerts.

The same plugin can be run by the scheduler, by hand from the interface or from the command line, with the same
result and the same log. And as with the other families, a `Cron` plugin can **specialize** another one: an
institution adds a notification to authors when embargoes are lifted without rewriting the original task.
The scripts that get duplicated and renamed (`lift_embargos_orig`, `generate_views` copied for an experiment)
disappear: you override, you version, you roll back.

**Every task and every event has its own screen and configuration.** A `Cron` or `Event` plugin declares a
**configuration schema** (parameters, types, default values, bounds, help). From it, the platform automatically
derives:
- a **configuration screen** in the administration, validated, with online help, the effective value and the
  layer it comes from (§4.6);
- a **monitoring screen**: queue, current and past runs, duration, errors, data touched, a button to rerun or
  suspend;
- its own **permissions**: who can view, configure, run.

The plugin can provide its own screen when the generated one is not enough, but it does not need one to
exist. No task and no event is invisible or configurable only by editing a file.

### 4.12 Nothing hidden
This principle applies to **how the platform works**. It does not apply to content. Everything that determines
the repository's behaviour is visible and inspectable, at least by administrators. Nothing runs "behind the backs"
of those who are responsible for it:
- **the effective configuration**: each setting, its value, its origin, including the default values coded into
  the platform (which must not exist without being displayed);
- **the plugins** loaded, their version, their order, what they override and what they disable;
- **tasks and events**: scheduled, running, failed, with their history;
- **access rules**: who can see what, and why a given person or robot gets a given response;
- **what machines see**: the `robots.txt` actually served, the announced policies, the rate limits applied,
  the responses given to each family of robots;
- **caches and indexes**: their state, their freshness, what they cover and what they exclude (for example: does a full-text index include embargoed documents?);
- **logs**: errors, sensitive actions, configuration changes, proposals and actions of the AI assistant (see
  below);
- **data handling**: for each object, its state (live, embargoed, restricted, confidential, under provisional
  withdrawal, withdrawn, deleted), the rules that apply to it and the history of its state changes are visible.
  You always know *why* a piece of content is or is not accessible.

**Logs can be consulted inside the platform.** Today, understanding an EPrints failure requires access to the
server: the Apache error log, the indexer log, crontab output sent by email, the event queue in the database.
In the successor, the logs of all components are brought together in one administration screen:
- **unified**: requests, errors, tasks, events, indexing, plugins, access and rate decisions, in the same place
  and in the same structured format;
- **searchable and filterable**: by period, level, component, plugin, record, user or robot family;
- **linked**: a request identifier follows an action from end to end (the request, the plugin called, the task
  triggered, the error produced);
- **live**: you can follow the log while you reproduce a problem;
- **exportable** to the institution's tools (open formats, standard protocols), without depending on them;
- **lean**: personal data is minimized, the retention period is displayed and respected, and access to the
  logs is itself logged.

No SSH access should be needed to know what is happening in your repository.

**Content, for its part, follows its own access rules.** A document that is embargoed, confidential or
restricted is not "open" because the configuration is: it stays protected, including, depending on the case,
from the administrators themselves. Transparency covers the rules and how they are applied, never protected
content.

Secrets (passwords, keys) are never displayed in clear, but their **existence**, their use and their rotation
date are. Transparency has levels (the public sees less than administrators); it has no grey areas.

### 4.13 Alerts, messages and self-diagnosis
The platform communicates in both directions, and it can tell on its own when something is wrong.

**Sending messages to users.** Depositors, authors, reviewers, subscribers and administrators receive what
concerns them: deposit received, correction request, record sent back for revision or put back online, embargo
lifted, new publication matching a saved search, announced maintenance.
- **Channels**: built-in messaging, email, a banner on the site, and open protocols for machines
  (COAR Notify, ActivityStreams, webhooks).
- **Preferences**: each person chooses what they receive, through which channel, and how often (immediately or
  as a digest).
- **Templates**: messages are written in EPScript 2.0 and in translated phrases, with the same editor and the
  same history as the rest of the configuration.
- **Traceability**: every message sent is logged (§4.12); you know who was notified of what, and when.

**Receiving system alerts.** A failed task, a growing event queue, a disk filling up, an expiring certificate,
a file hash that no longer matches: the alert reaches the right people, with its severity, instead of waiting
for a user to report the failure.

**Self-diagnosis.** Health checks run continuously, as `Cron` plugins (§4.11):
- **operation**: database, indexer, event queue, disk space, overdue tasks;
- **integrity**: file hashes, consistency of access rights, embargoes past their date but not
  lifted;
- **consistency**: contradictory configuration, conflicting plugins, missing phrases, invalid identifiers;
- **machine access**: does the `robots.txt` served match the intended policy? Does OAI-PMH respond
  consistently? Are machine paths served without a firewall challenge? Is the claimed OAIHP level still
  reached?

Each diagnostic says **what is wrong, why it matters, and how to fix it**, with a link to the relevant setting;
the AI assistant can explain it and propose a fix (§4.9). A status page summarizes the health of the repository:
detailed for administrators, brief for the public.

EPrints already contains the seeds of this (alerts on saved searches, problem reports on records): the task is
to generalize them and bring them together.

### 4.14 A separate administration gateway
Administration is not one more page on the public site.
- **A separate address** (host name, or even a dedicated port or network), served by a separate process with its
  own privileges. The public site contains no administration functions: a flaw on the public site does not give
  access to the administration.
- **Fine-grained network-level access control**: institutional network or VPN, address lists, client
  certificates (mutual TLS), according to each institution's policy. The application firewall of the public site
  and that of the administration have different rules, suited to their audiences.
- **Strong authentication**: the institution's single sign-on, mandatory two-factor authentication, hardware
  keys (WebAuthn) recommended, short sessions, re-authentication for sensitive actions.
- **A documented emergency access** ("break-glass"), logged and reported to all administrators.
- **Everything is logged there**: every view and every action (§4.12).

Depositors and reviewers keep working on the public site, with their own roles (§4.8); the gateway is reserved
for what affects how the platform runs.

**A dashboard of all critical functions.** The gateway shows the state of each component: web server, database,
search engine and indexes, file storage and backups, event queue and scheduler, caches, email, TLS certificates,
application firewall, system resources (CPU, memory, disk). For each one: available or not, load, response time,
version, pending security updates, latest errors, and the link to the diagnostic (§4.13). Metrics can be exported
in open formats (OpenTelemetry, Prometheus) for the institution's monitoring tools, without depending on them.

**The choice of database.** EPrints relies mostly on MySQL or MariaDB (PostgreSQL and Oracle drivers exist, but
are less used). For the successor, **PostgreSQL** is the natural candidate: solid transactions and integrity,
indexable JSON types, built-in full-text search, recursive queries to traverse the entity graph, row-level
security, proven replication, a free licence and a large community. The principle matters more than the
product:
- **a single source of truth**: the database (metadata, entities, events) and the preservation storage
  (files, OCFL);
- **everything else is derived and can be rebuilt**: search indexes, SPARQL endpoint, caches, exports, OAIHP
  snapshots. They can be destroyed and rebuilt without losing anything.

---

### 4.15 Structure in XML, namespaces made readable, linked data in relief

EPScript 2.0 does not live alone: it fits into a **structure**. For pages, citations and templates, XML remains
the right skeleton. It is hierarchical, schema-validatable, transformable, and it is already the EPrints model:
the structure is in XML and the expressions are in EPScript. We keep it.

**Namespaces** are essential, because several sets of schemas describe the same record and overlap:
schema.org, Dublin Core, BIBO, FOAF, PROV, DataCite, and the repository's own vocabularies. Their cost lies
mostly in presentation: declarations at the top of the file, prefixes to remember, unreadable IRIs.
The editor must therefore **show them without imposing them**:
- declarations are folded into a legend;
- each prefix has a stable colour, shared by the code, the blocks and the data views;
- the full IRI is shown on hover;
- completion suggests terms from the right vocabulary according to context;
- checking flags an unknown term in its namespace, just as it flags an unknown field.

With **linked data**, the third dimension becomes meaningful. Each vocabulary becomes a **plane**. A given
resource (the record, a person, an institution) occupies the same position on every plane where it is
described, and a vertical axis connects its descriptions: this is its **identity**. **External sources** (ORCID,
ROR, Crossref, Wikidata, other repositories) are placed around it, linked by `owl:sameAs` or shared identifiers.
You can see at a glance:
- which schema describes what;
- where the schemas overlap and where they contradict each other;
- what comes only from outside.

The same view serves the administrator checking a mapping, the cataloguer completing a record, and the robot
that needs to know what to harvest (§4.7). It remains a **view**: the data is stored only once (§4.3), and each
plane is a projection that can be rebuilt.

## 5. The horizon: what makes the project desirable

The horizon is not required for a first version, but the core must make it possible.

- **The meta-repository.** Thanks to recursion, a repository can contain others, from the macro (consortium,
  national network) to the micro (laboratory, project, course). Repositories can also coexist side by side
  and federate. Each one keeps its authority; the graph connects them.
- **Verified producers.** Creators (scientists, artists or others) are authenticated (ORCID, institutional
  identities, verifiable credentials), and so are their contributions.
- **Communities of practice.** People linked to the content can communicate with each other securely, around
  works and data, relying on open, federated protocols (COAR Notify, ActivityStreams) rather than on a closed
  platform.
- **Versatile distribution.** Text, data, images, sound, video, 3D objects: a digital library that distributes
  every format, each with its accessible derivatives (transcripts, descriptions).
- **The power of linked data.** Browse, query, visualize, measure: the graph makes it possible to show what an
  institution produces, with whom, and from what.

In one sentence: **a cross between EPrints, Wikidata, a distribution platform and a professional network,
whose content is verifiable by its very structure.**

---

## 6. The technology choice: criteria before a name

The language will be chosen by a working group on explicit criteria, not by preference. The criteria:

1. **Longevity**: an ecosystem that will still exist in twenty years, and compatibility standards.
2. **Succession**: a pool of developers, including in libraries and universities.
3. **Safety**: typing, memory management, isolation of extensions.
4. **Performance** for file serving and indexing.
5. **Accessibility** for non-programmers, through EPScript 2.0 rather than through the host language.

Options to evaluate (not exhaustive):

| Option | Strengths | Points of attention |
|---|---|---|
| Rust for the core, WebAssembly for EPScript and plugins | Safety, performance, native isolation of extensions, same execution on server and browser | Learning curve; smaller pool of contributors |
| TypeScript end to end | A single language from browser to server; huge ecosystem | Fast ecosystem churn; dependency debt |
| Python | Very present in research and libraries (InvenioRDM) | Performance; isolating extensions is harder |
| Go | Simplicity, deployment, longevity | Less expressive for an embedded configuration language |

One principle settles part of the debate: **if EPScript 2.0 and the plugins run in a portable sandbox, the core
language matters less to the community.** Institutions write EPScript and plugins; only the core maintainers
write the host language.

### 6.1 A new kind of language

So the language that really matters to the community is **EPScript 2.0**, not the host language, and it must be
a new kind of language. The goal is not to invent one more syntax. The goal is to combine properties that no
common language combines:

- **Aware of the data model**: its types come from the repository schema. Writing `item.titel` is an error
  flagged before going live, with the suggestion `title` (demonstrated in the workshop prototype).
- **Declared effects**: each script, plugin or task declares what it reads, what it writes, and whether it sends
  a message or contacts the network. This is not a convention: the engine enforces it. Permissions (§4.8),
  transparency (§4.12) and security then follow from the code itself, instead of being documented alongside it.
- **Isolated by construction**: sandboxed execution, time and memory limits, no implicit access.
- **Two views, one program**: text and visual blocks are two synchronized representations of the same script;
  a non-programmer and a developer work on the same object.
- **Live**: compiled and checked at every keystroke, run on real records, with the output in front of you.
  Each error is shown on the faulty line, with an arrow under the column where it is detected. The scope being
  written (the "if", the "match" or the function where the cursor is) stays sharp, and the rest fades, in the
  text as in the blocks. The blocks follow the layout of the text instead of being another representation of it.
- **Self-documenting**: documentation is part of the language (description, examples, edge cases), and the
  examples are run as tests. A function with no documentation or example is flagged.
- **Readable by humans and by machines**: a small, explicit semantics, with no magic, that AI assistants can
  read, explain, check and propose to modify, under human control (§4.9).
- **Versioned and traceable**: each script has its history, its provenance and its dependencies.
- **Portable**: compiled to an open target (for example WebAssembly), it runs on the server, in the browser for
  preview, and in other implementations of the platform.

The host language of the core will be chosen according to the criteria above. But it will have to serve this
language: EPScript 2.0 is what will carry the legacy of EPrints and the promise made to non-programmers.

**Towards a structured, multilingual natural language.** The horizon of EPScript 2.0 goes further: a
**structured, multilingual natural language**, where a rule is written and read like a sentence (in French, in
English or in another language) while remaining an exact program that can be checked and executed. One abstract
structure, several surface languages: a rule written in French by a librarian is read in English by a colleague,
is displayed as blocks for those who prefer it, and runs the same way everywhere.

EPrints already contains the seed of this: its multilingual phrase files and its EPScript expressions live side
by side in the same templates. This language is the subject of a **preliminary project**, PolyGlotML, whose
foundations exist; EPScript 2.0 would be its first application domain. Related work deserves study as support.
The most direct is **AppleScript**, which existed in the 1990s in several "dialects" (English, French, Japanese):
the script was stored in a neutral compiled form and displayed in each user's language. So the principle has
already worked; what remains is to generalize it. Add to this multilingual grammars with a shared abstract syntax (Grammatical Framework), controlled natural
languages (Attempto Controlled English), abstract content rendered in several languages
(Wikifunctions / Abstract Wikipedia) and programming languages with translated keywords (Hedy).

**Where natural language stops.** Not all software has to be written in natural language. The **core**
(storage, network, security, sandboxed execution) stays written in a conventional programming language, by a
small team of maintainers. Structured natural language targets **what institutions customize**: their rules.
We start with EPScript, that is, citations and renderings, where the need is clearest and the vocabulary most
closed. The same language then expands, domain by domain:

1. citations and renderings;
2. deposit validations ("a master's thesis must have a supervisor");
3. access, embargo and distribution rules;
4. workflows and review;
5. scheduled tasks and alert conditions (§4.11, §4.13);
6. policies towards robots (OAIHP).

Each step adds a vocabulary drawn from the schema and the declared effects. It does not add a new language.

**Three ways to write a rule**, all reduced to the same neutral structure:
- **by example**: you write the desired reference for a few records, and the platform infers the rule, which
  you then adjust (demonstrated in the workshop);
- **with code prediction**: first exact, drawn from the grammar and the schema (fields, methods according to
  type, uncovered cases). Next, a prediction of what comes next, drawn from the **index of the code itself** and
  from a **small base of common patterns** ("(year).", volume(issue), DOI prefix…). It is computed on the
  neutral form, so a habit formed in one language serves in the others (demonstrated in the workshop). Finally,
  a small local model, constrained by the grammar and the type checker, so that it
  only proposes code that compiles. This model is trained on the neutral form, so it is valid in every
  language;
- **directly**, in sentences, in blocks or in text.

### 6.2 Documentation that measures up

The documentation platform is chosen together with the language, because each ecosystem has its natural tools
(rustdoc and mdBook for Rust, TypeDoc and Starlight or Docusaurus for TypeScript, Sphinx or MkDocs for Python,
pkg.go.dev for Go). Whatever that choice, it must respect a few principles:

- **Generated from the source**: the reference for plugins, configuration schemas, EPScript functions, tasks
  and endpoints is produced from the code and the schemas, never copied by hand.
- **Executed examples**: every example is tested in every release; a wrong example makes the publication
  fail.
- **Integrated into the platform**: help for each screen, each setting and each function opens where it is
  needed, and the examples can run on the repository's data (as in the workshop).
- **Three audiences**: people who configure (guides, recipes), developers (reference, architecture, ADRs),
  and machines (OpenAPI, JSON Schema, MCP descriptions, `llms.txt`).
- **Versioned with the software**, with one page per release and what changed.
- **Bilingual at minimum** (French and English), translatable, open to contributions.
- **Served by the platform itself**: the documentation is a set of versioned, linked and citable content in an
  EPrints du futur repository. The project uses what it builds.

---

## 7. What we will not do

- **No frills.** Each feature must justify its complexity. The relative simplicity of EPrints is a quality
  to preserve.
- **No lock-in.** Not to a cloud provider, not to an AI model, not to a format.
- **No blockchain.** Integrity comes from hashes, signatures and a chained log, which are proven and frugal
  techniques.
- **No AI that decides.** The assistant proposes; humans decide.
- **No abrupt break.** No institution should have to rebuild everything to migrate.

---

## 8. The path from EPrints 3

1. **Today**: the OAIHP plugin for EPrints 3.3/3.4 makes existing repositories machine-readable
   and measures the effect. It also shows precisely what EPrints cannot do.
2. **Tomorrow**: a prototype of the new platform reads the data of an EPrints repository (EPrints XML
   export, OAI-PMH, OAIHP) and serves it according to the three layers. EPScript 1 is automatically translated
   into EPScript 2.0.
3. **Next**: a pilot institution runs both platforms in parallel, then switches over.
   Existing URLs and identifiers are preserved.

---

## 9. Call

We invite repository managers, librarians, archivists, researchers, developers, language communities and
standards bodies to:

- **criticize** this manifesto: its blind spots matter as much as its ideas;
- **share** their own findings, with concrete examples;
- **contribute** to the OAIHP protocol, the EPrints plugin and the prototypes;
- **join** the working group that will choose the architecture.

EPrints taught us that a repository can be simple and powerful at the same time. It is up to us to prove that
this stays true for the next twenty-five years.

---

*See also: [`comparaison-plateformes.md`](comparaison-plateformes.md) (EPrints compared with other
platforms); the OAIHP protocol and the `oaihp-eprints` package for EPrints 3, published separately.*
