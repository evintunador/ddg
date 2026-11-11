# Offline Knowledge and Recovery System

Proto-requirements and implementation options. This repository currently contains the specification. Algorithms, default values, and implementation choices remain subject to evaluation.

The proposed product assembles and maintains a portable archive of knowledge, software, models, and culture under a user-selected storage budget. It should remain useful after internet access disappears and support ordinary reading, search, and capable local agents. Its distinguishing value is choosing a useful local collection while spreading additional knowledge across the participating population through one coordinated, importance-weighted allocation policy.

Every component should scale without requiring a person to curate each item, approve each routine contribution, or steer agents through individual implementation steps. A maintainer designs the architecture, schemas, trust policies, and general tests within which agents operate. The project can begin as a published requirements package for a developer, current agents, or future agents to implement. A small demonstration can establish parts of the maintenance design while keeping ingestion and compute costs low. This brief is intended to stand alone as a product and implementation handoff. Candidate algorithms remain replaceable until evaluated.

## Intended uses

Three uses drive the design. An individual downloads a small collection onto a phone or laptop. A household maintains a larger archive on an existing computer or external drive. A kit assembler provisions substantial storage and compute for a ready-to-use civilization recovery chest.

The long-term archive should span immediate survival, recovery, education, engineering, scientific reference, software reconstruction, and cultural continuity. Music, movies, games, literature, art, history, language, and recreation are legitimate user priorities. Users should be able to prioritize them as strongly as practical survival material.

A possible downstream deployment is a civilization recovery chest assembled from purchased batteries, folding solar panels, computers, drives, input devices, travel monitors, and portable paper-like readers. This scenario explains the desired depth, portability, and independence from online services. Commercial kit production is a later possibility and should not drive the initial scope. License evidence belongs in the catalog from the beginning. Hardware design and claims about enclosure shielding or battery endurance would need separate evidence.

| Area | Proto-requirement |
| --- | --- |
| Scalable maintenance | Agents and automation handle routine implementation, ingestion, structured extraction, validation, and policy-permitted changes through stable contracts and general tests. Maintainers govern architecture and exceptional decisions. Routine topic growth should not require item-by-item review. |
| Storage allocation | Accept arbitrary byte budgets and configurable category distributions. Treat capacities, weight functions, replication targets, and operating thresholds as parameters to evaluate and tune empirically. Support user pins and exclusions and show the planned collection before downloading. |
| Content breadth | Support PDFs, ebooks, reference websites, maps, scientific papers, source repositories, runnable software, build tools, AI models, datasets, courses, audiovisual material, and cultural archives through extensible adapters. |
| Ranking | Maintain a reproducible, versioned preference system within categories and use it to inform allocation. Preserve comparison evidence and model versions so new content and ranking changes can be incorporated without accumulating arbitrary ordering decisions. |
| Platforms | Start with macOS and retain Linux, Windows, and Android as longer-term targets. Publish a simple website with installable packages appropriate to each supported platform. Also support command-line and terminal interfaces. Record which operations each platform supports locally. |
| Language | Represent user language preferences, artifact language and script, translation relationships, and model language support independently from subject categories. Early implementations may support one language while preserving multilingual data and extension paths. |
| Access | Provide direct file browsing, conventional full-text search, optional similarity search, and an interface that agents can query. Search and AI output should link to the original source and page, section, timestamp, or commit. |
| Agent capabilities | Reuse an existing local agent harness that can read references, create and edit files, use Git, run commands, and use supported build or container tools. Support importing user material and preserving agent-created work separately from downloaded material. |
| Reuse | Reuse mature downloaders, readers, inference runtimes, OCR, search engines, agent harnesses, and package or environment managers through adapters. Select dependencies based on working offline demonstrations. |
| Data acquisition | Maintain manifests with download locations, checksums, sizes, licenses, metadata, and acquisition recipes. Download large content from upstream sources or permitted mirrors. Keep project-hosted data small by default. |
| Substitution | Distinguish identical-file mirrors, alternate editions or equivalent works, and broader fallback collections that provide partial coverage of the intended capability. Explain every substitution and its limitations. |
| Updates | Reconcile the selected collection when sources, rankings, models, or user settings change. Reuse verified local content, resume partial work, and recover from interruption without requiring a full wipe or redownload. |
| Models | Cover major model categories, including image generation, and apply category-specific selection policies. Reuse installed assets and structured external hardware evidence. Language models may use a size ladder. Other categories may select a single suitable model. Preserve complete dependencies and license evidence. |
| Population allocation | Make importance-weighted coverage, diversity among equivalent works, long-tail reach, and diminishing replication value part of ordinary selection. Preserve useful local coverage and complete artifacts. Provide a policy tradeoff between breadth and redundancy. Network seeding remains optional. |
| Kit provisioning | Preserve the longer-term ability to prepare multiple drives or machines, local sharing, offline transfer, and exports suitable for portable readers. Hardware kits and commercial export are later possibilities. |

## Language is an independent dimension

Support non-English speakers through explicit metadata and selection preferences. Represent artifact language and script, user comprehension and preferred languages, available translations, and relevant model capabilities separately from subject and media type. Permit multiple languages for a work, multilingual artifacts, and unknown language metadata.

Two editions in different languages may have similar subject coverage while providing different utility to a particular user. Translation relationships need provenance and quality evidence. A machine translation is a derived artifact with its own size, licensing considerations, source version, and validation state. A translation model does not make every source equally usable or automatically turn a generated translation into a validated replacement.

Language support applies to app labels, search and OCR, speech and translation tools, model recommendations, source-linked answers, and local coverage constraints. Embedding and ranking evaluations should eventually include relevant languages and scripts. An initial implementation may focus on one language to limit cost while retaining these fields and extension paths.

## Subject catalog and capability graph

Reuse a downloadable knowledge graph for stable subject identifiers, multilingual labels, aliases, and broader or narrower relationships. Wikidata is the leading initial candidate for this backbone. Its structured data is CC0 and downloadable, and its instance-of and subclass-of relationships support different kinds of catalog structure. Import a versioned, relevant subset and publish compact derived catalog data. A full Wikidata dump is unnecessary for a narrow pilot or for every end-user installation. Its subject coverage and relation quality should be checked in the chosen pilot domain. [Wikidata downloads](https://www.wikidata.org/wiki/Wikidata:Database_download), [Wikidata licensing](https://www.wikidata.org/wiki/Wikidata:Licensing), [Membership relationships](https://www.wikidata.org/wiki/Help:Basic_membership_properties)

| Resource | Potential role | Boundary |
| --- | --- | --- |
| Wikidata | Broad subject identifiers, labels, cross-domain links, classes, and instances | Its relations require filtering and validation for the application. |
| ConceptNet | Commonsense links and candidate prerequisite or use relationships | Its HasPrerequisite relation describes dependencies between events or actions and needs validation before becoming a learning or technical prerequisite. |
| OpenAlex | Research topic hierarchy and scholarly metadata | Its supported topic taxonomy is research-oriented. Its older Concepts system is deprecated. |
| SUMO | Formal upper-level and domain concepts where logical structure helps | Assess mapping effort and license compatibility before adopting its files. |

ConceptNet provides downloadable assertions and relation definitions. OpenAlex documents its topic hierarchy. SUMO publishes its ontology files in a source repository. These resources can be combined through mappings while preserving their source IDs and licenses. [ConceptNet downloads](https://github.com/commonsense/conceptnet5/wiki/Downloads), [ConceptNet relations](https://github.com/commonsense/conceptnet5/wiki/Relations), [OpenAlex aboutness](https://help.openalex.org/data/aboutness/), [SUMO](https://github.com/ontologyportal/sumo)

A document can belong to several subjects while its bytes are counted once. Learned clusters and embedding spaces should remain replaceable views over the stable catalog. When a source graph changes, maintain mappings and explicit migrations rather than silently reassigning existing item identities.

Represent capabilities such as repairing equipment, learning a subject, building a program, running a local agent, or communicating through images or speech. Connect them to documents, programs, models, datasets, hardware, and prerequisites. Keep subject relationships, recommended learning order, hard software dependencies, and physical process requirements as distinct edge types. A broad ontology can supply candidate concepts and relations, but a claim that one capability requires another still needs applicable evidence, provenance, and a confidence or validation state. Document overlap and subject similarity alone cannot establish prerequisites.

A catalog item needs stable identity, work and edition information, byte-level artifacts and hashes, acquisition locations, extracted text and provenance, rights evidence, dependencies, capability coverage, and relationships to replacements. PDF originals, OCR text, thumbnails, embeddings, runtime binaries, and model quantizations are distinct artifacts with separate sizes. Material may be available upstream, downloaded, verified, searchable, runnable, buildable, or tested. Those states should remain visible. Acquisition and source evidence should record who or what observed a fact, when, at which immutable version, and through which validation method.

For example, archived Linux source has value, but its priority depends on the profile. Boot media, compilers, firmware where redistributable, build dependencies, and practical documentation can make an operating-system collection usable. A kernel repository by itself does not establish that capability. Repository preservation must include the chosen commits, referenced submodules, Git LFS objects where used, dependency lockfiles, and the artifacts needed by the declared build. Distinguish preserving a selected working tree from full history, and count each requested form in the budget. Git bundles support offline repository transfer, and package systems such as Nix can transfer a program together with its dependency closure. Neither eliminates the need to test the complete offline workflow. [Git bundles](https://git-scm.com/docs/git-bundle), [Nix dependency closures](https://releases.nixos.org/nix/nix-2.28.3/manual/command-ref/nix-copy-closure.html)

## Ranking and comparison evidence

The proposed embedding, pairwise judge, and Elo pipeline is a reasonable experiment. Embeddings can help propose related items and duplicate candidates. Pairwise judgments can be easier to inspect than free-form importance scores. Neither establishes that similar documents are interchangeable or that a total order yields the best collection.

The comparison rubric should state the audience, scenario, assumed equipment, time horizon, and existing collection. Compare factual reliability, applicable coverage, accessibility, completeness, prerequisites, practical usability, freshness where relevant, and storage cost. Allow ties, incomparability, and abstention when the evidence is inadequate. Citations, repository stars, download counts, venue, and citation diversity can enter through category-specific metadata adapters. Their timestamps and limitations should be retained, and popularity should not override the actual content.

Preserve each comparison’s input evidence, selection method, rubric, model and checkpoint, outcome, and confidence. Include document order reversals, stable reference comparisons, and a held-out evaluation set with some independent human or expert judgments. Synthetic teacher labels can help train a smaller judge, but they do not independently validate the teacher’s preferences.

Elo does not remove ordering debt by itself. A sequential Elo implementation can depend on comparison order, opponent selection, starting conditions, and later judge changes. A candidate alternative is a regularized Bradley–Terry fit to a stored comparison graph, with uncertainty estimates and reference comparisons. Disconnected comparison groups require connecting evidence before their scores can be compared. The choix library implements relevant pairwise preference inference. This is an implementation candidate, subject to evaluation. [choix](https://github.com/lucasmaystre/choix)

Avoid comparing every pair. Compare new candidates to reference items, close alternatives, uncertain items, and documents near likely selection boundaries. Include some exploration outside the nearest embedding cluster so the system can detect missing topics. Version judge changes and recalibrate or recompute affected comparisons. Ranking releases should be reproducible from preserved evidence.

A production Laya judge would require domain-specific fine-tuning and evaluation. Training should use the project's explicit comparison rubric and preserve the teacher, evidence, labels, calibration, and held-out results. Laya's repository reports task-specific gains from specialization, which supports evaluating it as a compact decision component. It does not establish readiness for this catalog without that work. Evaluate input truncation, document-order sensitivity, uncertainty, and ranking stability alongside a simple baseline and a stronger general model. [Laya documentation and evaluation notes](https://github.com/NandhaKishorM/laya)

## Storage planning

A ranking is an input to selection. The planner also needs item sizes, dependency costs, duplicate content, coverage already selected, prerequisites, and user preferences. The fifth near-identical textbook may contribute less than a reference covering a missing practical capability. A large model may add less than a smaller model together with its retrieval tools and source corpus.

Treat selection as budgeted coverage with dependencies and diminishing returns, extended across the participating devices. Each proposed artifact placement has both personal utility on that device and preservation value for the collection as a whole. Replication value should diminish as suitably independent, useful copies accumulate. Importance and equivalence determine how quickly that value diminishes. Start with a deterministic, explainable algorithm and small cases where expected outcomes can be checked. Compare it with simple curated presets and independent per-device ranking.

Use one overall physical disk budget, with visible accounting for content, runnable tools and models, indexes, and required overhead. Population-wide preservation influences ordinary allocation within that same budget. It should not depend on a separate long-tail opt-in or fixed preservation partition. Model deployment can have a minimum viable bundle comprising weights, tokenizer, runtime, configuration, and supporting files. A model-only download intended for future hardware should be labeled as archival. Users may adjust category distributions and model allocations while all artifacts continue to count toward the physical limit.

Account for installed size, OCR and indexes, shared dependencies, decompression, filesystem allocation, download staging, and update headroom. Report both final size and peak temporary space. Hash-based deduplication can avoid repeated exact files, but near-duplicate editions need explicit representation choices.

A budget increase should usually preserve the useful existing collection. If a different combination becomes substantially better, show the tradeoff. Use a measurable replacement threshold or change cost to limit churn. User pins, exclusions, notes, and generated work survive replanning. If required capabilities cannot fit, show the missing capability and minimum additional bytes.

## Mirrors and replacements

| Relationship | Identification | Required behavior |
| --- | --- | --- |
| Identical artifact at another location | Cryptographic digest, expected size, and provenance | Try another mirror without changing the selected content. A responding URL alone does not establish that it serves the expected file. |
| Another artifact of the same work | Edition, language, format, accessibility, and rights metadata | Choose an acceptable representation and disclose changes in coverage, revision, or usability. |
| Different work with comparable coverage | Verified topic and capability coverage, prerequisites, audience, and quality evidence | Prefer alternatives that satisfy the intended requirement. Use embeddings to find candidates and content evidence to validate the replacement. |
| Partial fallback or a collection of works | Capability graph and explicit coverage assessment | Replan using introductory material, related subtopics, or a multi-item replacement. Record the capability gap and the resulting byte cost. |

Fallback planning should terminate predictably and cache unavailable sources. It should distinguish temporary network failure, unavailable upstream content, rights restrictions, and checksum mismatch. Acquisition recipes should have bounded retries and respect upstream access limits. Identical mirrors and prevalidated equivalent works are suitable initial fallback scopes. Composing several partial replacements is a substantially harder planning and validation problem. Preserve that extension in the schema, but allow an initial implementation to report a coverage gap when it lacks evidence for a valid composition.

## Acquisition and distribution

A small GitHub repository can hold source code, catalog manifests, permitted metadata, hashes, and recipes. A static site can link to installers and catalog releases. Large files can come directly from publishers, Kiwix, software distributions, model hosts, or permitted peers. aria2 already supports multiple source URLs, Metalink, BitTorrent, resume, and integrity checks, making it a candidate acquisition component. [aria2 manual](https://github.com/aria2/aria2/blob/master/doc/manual-src/en/aria2c.rst)

Existing projects use several approaches. Offline Emergency Library downloads content from original sources and records per-source licensing. Svalbard provisions archives from recipes and presets and can reuse existing cached files. Kiwix distributes downloadable archive files. The project can follow the manifest approach without operating a large content origin. Availability still depends on someone retaining and serving each artifact. [Offline Emergency Library](https://github.com/elhajahmadj/offline-emergency-library), [Svalbard](https://github.com/pkronstrom/svalbard), [Kiwix](https://get.kiwix.org/en/solutions/applications/)

GitHub is suitable for code and initial installer distribution within its current terms and limits. Releases currently allow assets below 2 GiB each, while Pages has a soft monthly bandwidth limit. This supports a low-hosting-cost starting point, with a plan to move distribution if usage or service terms demand it. Compute for agents, catalog validation, OCR, and model evaluation remains a separate operating cost. [GitHub release limits](https://docs.github.com/en/repositories/releasing-projects-on-github/about-releases), [Pages limits](https://docs.github.com/en/pages/getting-started-with-github-pages/github-pages-limits)

Preserve artifact-level license evidence, source location, retrieval date, required notices, and any applicable restrictions, including evidence for derived OCR text and other generated representations. Acquiring, modifying, redistributing, and commercially redistributing material can have different conditions. A download pointer supplies no permission by itself. Copyleft material can permit commercial use with obligations, while noncommercial material imposes different conditions. Commercial kit export can be evaluated later using this evidence. [Creative Commons license conditions](https://creativecommons.org/faq/), [CC BY-SA sharing conditions](https://creativecommons.org/licenses/by-sa/4.0/deed.en)

Simple downloads also have a release-maintenance cost. Direct macOS distribution can use Developer ID signing and notarization, which requires the relevant Apple developer enrollment. These credentials, release identities, and any fees need an owner even when agents perform the build steps. [Apple enrollment](https://developer.apple.com/help/account/membership/program-enrollment/), [Developer ID](https://developer.apple.com/developer-id/)

## Catalog storage and open data

A database is compatible with an open-source project. Keep the schemas, migrations, import and export tools, and validation policy open, and distribute the catalog under explicit data licenses. A complete, documented export should make the system usable without an obligatory hosted service.

A practical starting design keeps schemas, policies, adapter code, small ontology mappings, and manageable declarative catalog records in Git. Use local SQLite for indexed lookup, graph tables, materialized plans, search, and device state. At larger scale, store append-only comparison evidence, source observations, benchmark records, and derived catalogs in partitioned files or an operational database with versioned public exports. A local graph can initially be represented as node and edge tables without requiring a dedicated graph server.

Choose and document which records are authoritative. Generate database snapshots and indexes deterministically from those records where practical. Avoid independently editing a database and Git copy of the same fact. PR submissions can be validated and imported into the canonical data store, with their provenance preserved. Large evidence dumps and frequently changing population state should not force every source checkout to retain their complete history. Public exports need stable schemas, revisions, checksums, and reproducible import instructions.

## Community digest submissions and trust

User PRs can propose digests and download observations without the project downloading the entire catalog centrally. A digest identifies bytes. It does not establish who published them, whether a link is legitimate, or whether the artifact is safe. A contributor can honestly download a malicious file from a malicious URL and compute its correct digest. Proving execution of a hash command does not resolve source trust.

Use a graduated evidence policy. Prefer authenticated publisher digests, signatures, immutable repository identities, or verified upstream provenance when available. Accept community reports as attributed observations containing source URL, redirects or final location where relevant, artifact version, size, algorithm, digest, timestamp, and retrieval method. Distinguish publisher-authenticated records, trusted-fetch observations, independently corroborated reports, and unverified proposals. Multiple accounts alone do not establish independent corroboration.

When stronger acquisition evidence is useful, a project-controlled or otherwise trusted isolated fetcher can execute an approved fetch recipe and attest the observed source and digest. Verification must check the expected signer or workflow identity and allowed recipe revision. A contributor's own workflow attesting its own result supplies attribution, with trust still determined by policy. Existing attestation tools can support this structure. GitHub explicitly distinguishes artifact provenance from a guarantee that the artifact is secure. [GitHub artifact attestations](https://docs.github.com/en/actions/concepts/security/artifact-attestations), [SLSA provenance](https://slsa.dev/spec/v1.2/provenance), [Sigstore verification](https://docs.sigstore.dev/cosign/verifying/verify/)

An ordinary client can verify content while downloading it for its own use, adding no separate central full-catalog download requirement. Conflicting digests for a supposedly identical version should prevent silent replacement of an accepted artifact and retain both observations for diagnosis. Mutable URLs may legitimately change, so observations must be tied to time and version. Exact mirrors require identical digests. Distinct editions and representations require separate artifact records.

Initially restrict trusted automatic admission to a tractable set of source types and verification methods. More general proof-of-execution or trusted-hardware schemes remain possible research extensions. A downloadable PDF, model, software package, and executable recipe may need different admission and execution policies. Contribution workflows should avoid running untrusted PR code with repository secrets or privileges. [GitHub guidance on untrusted pull requests](https://docs.github.com/en/actions/reference/security/securely-using-pull_request_target)

## Interfaces and reusable components

| Concern | Candidate components | Evidence required before adoption |
| --- | --- | --- |
| Catalog and planning | Versioned manifests, a local database, and a portable planning library | Reproducible plans, bounded storage, explainable substitutions, stable upgrades |
| Download and verification | aria2 or comparable mature transfer libraries | Mirror failure, resume, hash mismatch, interruption, and upstream access tests |
| Reading and full-text search | Existing PDF and ebook readers, Kiwix for ZIM, SQLite FTS5 | Useful results with source locations after network removal |
| Similarity search | A small local embedding model with a portable index such as sqlite-vec | Language coverage, relevance, rebuild cost, index size, and mobile compatibility |
| Inference | Existing local endpoint, Ollama, llama.cpp, or a platform-specific adapter | Offline startup, complete model assets, hardware performance, no duplicate download of existing weights |
| Agent workspace | OpenCode, Continue, or another evaluated local harness | Offline file search, citations, Git use, artifact creation, and recovery from a failed tool call |
| Software environments | Pinned package caches, build closures, OCI images, installers, and source bundles | Install, run, and selected builds with network access disabled |
| App packaging | A shared core with platform adapters and a framework such as Tauri | Native install, update, permissions, filesystem access, and offline launch on every supported platform |

SQLite FTS5 supports local full-text search. sqlite-vec offers a small portable vector extension but is still described as pre-v1. Tauri supports desktop and Android targets. These are candidates rather than a committed stack. [SQLite FTS5](https://www.sqlite.org/fts5.html), [sqlite-vec](https://github.com/asg017/sqlite-vec), [Tauri](https://github.com/tauri-apps/tauri)

OpenCode provides an existing agent harness with local-provider support, and Continue documents configuration for offline use. A harness still needs its binaries, dependencies, configured tools, model assets, and local endpoints available. The eventual system should test those together. Desktop and Android support should have an explicit capability matrix. Docker Engine’s documented host installations cover Linux and Docker Desktop platforms, so identical container execution on Android should not be assumed. [OpenCode](https://github.com/anomalyco/opencode), [Continue offline setup](https://docs.continue.dev/guides/running-continue-without-internet), [Docker platforms](https://docs.docker.com/engine/install/)

Agent tools operate in user-controlled workspaces with configurable permissions. Untrusted documents and downloaded recipes should not acquire execution authority by containing instructions. Users can authorize substantial creation and build work while retaining clear control over the archive, credentials, and host system.

## Model selection and hardware evidence

Maintain a structured catalog of model checkpoints, quantizations, runtimes, licenses, complete supporting assets, hardware compatibility, memory use, context settings, throughput, startup latency, and evaluation results. Initially populate Mac performance records from published benchmark data, maintainer measurements, and sufficiently detailed articles. A local maintainer need not own every Mac or rerun every measurement.

For each observation, preserve the source URL, date, relevant passage or table reference, exact model revision and quantization where available, runtime and version, Mac chip and memory, context and input or output lengths, concurrency, measured metrics, and reported test conditions. Separate direct measurements from estimates and mark missing fields. Keep conflicting reports and their provenance rather than turning them into an unsupported precise average. Agents may extract candidate records and validate their structure, while source-quality rules determine recommendation confidence. Public Apple Silicon benchmark projects already provide examples with runtime and context variation. [Apple Silicon LLM benchmark example](https://github.com/alexhiesch/apple-silicon-llm-bench)

This evidence supports hardware-specific recommendations and identified gaps. A benchmark report on a given configuration does not by itself prove that every installation or a different runtime version will work. Local startup or smoke tests can validate the selected installation without reproducing a full hardware benchmarking program.

Represent major AI model categories, including language and coding, image generation or diffusion, vision and OCR, embeddings and reranking, speech recognition and synthesis, translation, and other useful modalities as the catalog expands. A tight storage budget can leave a category uninstalled, with its capability gap visible.

Selection policy should vary by category. Language models may benefit from a ladder that supports complex reasoning and inexpensive sub-agents. An image generation category may initially select one strong, compatible model whose end-to-end cost fits the device and budget. Evaluate additional sizes only when they enable a distinct useful capability. Include complete inference dependencies such as tokenizers, encoders, decoders, and runtime assets where the selected model needs them. Model families and categories should not inherit a universal size-ladder rule.

For communication and instruction, image generation should be evaluated on the intended task, such as intelligible diagrams or illustrations, and output should remain distinguishable from sourced reference images. Other image or video generation uses may be legitimate user preferences. Additional modalities should compete for storage according to measured or evidenced capability, hardware usability, and user priorities.

GPU memory fit must include weights, runtime overhead, and context or KV cache. Apple unified memory also has to leave capacity for the operating system and other tools. Initially use externally reported measurements and explicit estimates to select suitable Mac configurations. Preserve context and runtime assumptions and identify uncertainties rather than requiring a complete local benchmark program. Ollama documents that increasing context length increases memory requirements. [Ollama context and memory](https://docs.ollama.com/context-length)

A model is a compact learned representation of patterns and knowledge, with potential utility across many tasks. Its value should be measured by successful tasks together with retrieval and tools. It cannot provide a guaranteed exact copy of a particular book, source history, diagram, or cultural work. Preservation therefore needs original artifacts as well as capable models.

## Updates and upstream changes

Use content-addressed artifacts and signed, versioned catalog snapshots. Record desired state, verified local state, pending work, and reasons for changes. Fetch into staging, verify, and commit a consistent plan. Retain a working previous state when space permits and expose rollback and recovery options. A tight budget needs an explicit plan for temporary space and for any removal required before replacement.

Update changed artifacts and affected indexes, while reusing identical local content. Rebuild or version embeddings when their model or chunking changes. Avoid assuming delta updates exist for every format. An upstream ZIM or model publisher may require downloading a complete new artifact.

Software update authenticity should reuse established mechanisms such as The Update Framework where appropriate. Archive reading and local use must continue during long disconnection. The design must handle expired update metadata or inaccurate clocks explicitly when reconnecting, with user-visible recovery through the trusted update mechanism. [The Update Framework](https://theupdateframework.io/docs/overview/)

Support taking a signed update snapshot and required artifacts to another machine by USB or local network. A disconnected machine should be able to verify the transfer, explain changes, and update from the available bundle.

Pin dependency, runtime, adapter, ontology, and acquisition-recipe versions or digests in a released snapshot. A separate monitor can discover upstream version changes and open candidate update PRs, using tools such as Renovate or Dependabot where applicable. Each candidate reruns the affected contract, regression, and offline smoke tests. A monitoring event should not silently replace an installed dependency or rewrite artifact identity. Scheduled manual pin-update PRs are also a valid initial operating policy. [Renovate automated update policies](https://docs.renovatebot.com/configuration-options/#automerge), [Dependabot configuration](https://docs.github.com/en/code-security/reference/supply-chain-security/dependabot-options-reference)

Admission and promotion policies should identify changes eligible for automated merge and release preparation. Changes outside those policies remain explicit proposals. Use bounded work queues, retry limits, backoff, and failure records so a broken upstream cannot cause uncontrolled polling, repeated model calls, or download loops. Record which tests support each promoted version and preserve rollback to the prior usable snapshot.

## Population allocation and coordination

Every installation should use the same importance-weighted allocation logic for immediate local utility, diversity among equivalent works, breadth into the long tail, and replication. A high-priority capability can justify coverage on almost every device. Several sufficiently equivalent works can provide that capability through different artifacts on different devices. Lower-priority distinct material should retain a meaningful chance or scheduled opportunity for placement when the budget supports the local coverage requirements.

When several works offer comparably useful coverage, distribute those works across eligible users while preserving each user's local capability. Quality, language, prerequisites, artifact size, availability, user pins, and hardware compatibility can justify unequal allocations. The policy should avoid sending every user the same highest-rated work solely because it is available.

Give local coverage of essential capabilities explicit constraints or a protected utility level. A copy on someone else's device cannot provide a required local capability while the devices cannot communicate. Diversity operates among qualifying local collections, with coverage gaps visible when a budget cannot satisfy the requirements.

Storage capacity, category balances, breadth-versus-replication preferences, model ladders, and operating thresholds are continuous or configurable inputs. Choose evaluation budgets and default parameter values empirically after a working planner exists. Test a range of budgets and difficult boundary cases without committing the product or its acceptance criteria to an illustrative capacity.

Configure the tradeoff between reaching previously unpreserved works and adding copies of already stored works. High importance can justify early replication of medium-tail material before every remote part of the long tail has a copy. Low participation should permit breadth with few copies where appropriate. Replica targets should emerge from importance, eligible capacity, and desired resilience, with extra value assigned to copies across distinct failure domains when that information is available.

Preserve complete files and useful bundles that each recipient can read or execute independently. Chunk-level erasure-coded preservation is outside the intended product design. A usable software or model bundle includes its required assets.

Use locally retained assignment seeds or explicit scheduling tickets to avoid reshuffling on each update. Compare coordinated scheduling strategies against independent weighted sampling. A shared ticket sequence can distribute assignment opportunities predictably without a complete per-device holdings database. Stable seeds make assignments reproducible but do not by themselves coordinate independent users or reveal what they retained.

The scheduler needs an explicit model of what its available evidence can establish. Allocation can improve over independent random selection without maintaining a central inventory of every user. Candidate designs differ in their coordination and confidence.

| Design | Shared information | What it can establish |
| --- | --- | --- |
| Static published placement plan | Catalog revision, policy, stable assignment rules, and locally retained seed | Reproducible diversity and useful offline plans. Independent clients with no shared progress information cannot identify actual coverage deficits. |
| Ticket-based scheduling | A small shared sequence or reservation mechanism, with artifact eligibility and budget inputs | More controlled assignment distribution and cycling among equivalent works. Assignments can remain unfulfilled, and obtaining distinct global tickets requires some coordination. |
| Aggregate evidence | Per-artifact or per-capability counts of assignments, completed downloads, and any subsequent retention reports | A deficit-aware scheduler can estimate coverage without storing full personal inventories. Evidence freshness, duplicate reports, and attrition remain explicit uncertainties. |
| Peer or federated scheduling | Assignment records, summary counters, or receipts exchanged through peers or multiple independent services | Coordination without a sole central authority, at the cost of handling stale state, concurrent assignments, and reconciliation. |

Download counts are a possible proxy. Distinguish installer downloads, artifact assignments, completed artifact downloads, and later evidence of retention. An estimated retained-copy count can sum completed downloads weighted by an empirically fitted retention probability for their age and relevant cohort. Raw installer counts are much weaker evidence of actual artifact placement. Third-party download counters may also include unrelated users, caching, repeated downloads, and incomplete transfers.

Attrition parameters must be estimated or clearly labeled assumptions. An assumed attrition rate supports expected coverage; it cannot establish where deleted or failed copies actually occurred. A device that stops communicating may still be intentionally preserving the archive offline. Absence of a heartbeat must therefore not be treated automatically as loss of the stored data. Include sensitivity analysis for correlated failures and uncertainty in retention.

A lightweight initial candidate is a deterministic scheduler informed by versioned aggregate assignment or completion counters, with an explicit attrition model and stable local plans. Periodically published snapshots let clients operate between contacts and can be mirrored. Stronger retention reports or decentralized exchange can be added if measurements justify them. The architecture and counter storage remain open choices; a live centralized holdings server is not a prerequisite.

There is an information limit. Two populations that produce the same observations but retain different artifacts are indistinguishable to the scheduler. Fresh evidence is required to know which population has which gaps. The requirements should distinguish planned placement, estimated retained coverage, and observed holdings rather than promise exact retention from assignment statistics. CRUSH provides a useful precedent for calculating placement without a per-object central directory, while still relying on a shared map of participating storage. It is an architectural reference, not a proposed drop-in solution for this system. [Ceph CRUSH placement documentation](https://docs.ceph.com/en/latest/rados/operations/crush-map/)

Population-aware local placement belongs in the base product. Network upload, seeding, and sharing private material remain separate user-controlled actions. An optional BitTorrent adapter can reuse existing tooling with upload limits, AC-power preferences, and license eligibility. Keep exact artifact identities aligned with the catalog manifest. Local-network and removable-drive exchange let surviving users discover, copy, and repair complete works when ordinary online peer discovery is unavailable.

The preservation objective is a verifiable portion of the finite, eligible catalog, together with enough redundancy for important material. Complete catalog coverage becomes feasible only when participating capacity, source availability, artifact compatibility, and the assignment policy permit it. Replication and uniqueness should be reported separately, along with last-observed availability and uncertainty about disconnected devices.

LOCKSS provides a precedent for replicated storage, integrity checking, and repair. IPFS documentation also makes clear that persistent availability depends on retained copies. These are references for durability mechanisms. The product's proposed combination of personal utility, importance-weighted diversity, equivalent-work balancing, and preservation is a design to evaluate. [LOCKSS network preservation](https://www.lockss.org/join-lockss/build-network), [IPFS persistence](https://docs.ipfs.tech/concepts/persistence/), [BitTorrent v2](https://www.bittorrent.org/beps/bep_0052.html)

## Additional capabilities to consider

- Provide capability coverage reports that show missing prerequisites and practical gaps alongside download completion.

- Preserve maps and guidance for the user’s region, languages, climate, infrastructure, equipment, and skill level, with exportable profiles.

- Detect image-only PDFs, measure extraction coverage, preserve page references, and flag failed or low-confidence OCR. Preserve diagrams and tables in the original files.

- Detect duplicate works, superseded editions, corrections, and withdrawn material. Keep historical versions accessible with clear dates and status where preservation is intentional.

- Include printable quick references, EPUB and PDF exports, low-power reading modes, offline accessibility tools, and packages that can be carried to another device.

- Support private user manuals, plans, family documents, and local observations. Keep these separate from the public catalog and protect them according to user settings.

- Verify checksums periodically when the owner chooses, detect damaged artifacts, and repair from local drives, peers, or upstream mirrors.

- Include boot and recovery media, offline installers, architecture and driver information, and enough documentation to restore the archive on a replacement machine.

- Produce a kit inventory containing versions, hashes, licenses, storage allocation, supported hardware, and results of disconnected task tests.

- Show power and runtime estimates based on measurements. A 1 kWh battery and a large computer do not establish a particular operating duration without load and conversion-loss data.

- Make the archive exportable in ordinary formats so it can still be used if the app, website, or original maintainers disappear.

## Pilot and implementation strategy

The most distinctive work is the catalog model, selection policy, substitution graph, offline validation, and maintenance process. Existing tools already provide much of the downloading, reading, serving, and inference. Svalbard is particularly close in recipes, presets, portable runtime, reconciliation, and model support. Its repository also identifies testing gaps and roadmap work. Evaluate extension or contribution against a separate implementation using concrete acceptance cases and licensing preferences. [Svalbard](https://github.com/pkronstrom/svalbard)

Reduce initial cost by selecting macOS and one small content domain, such as survival guides or existential philosophy. The pilot should exercise the shared ingestion, catalog, ranking, allocation, search, and maintenance infrastructure through that domain. Reuse an existing local model and a modest corpus. Defer broad crawling, full multi-model coverage, additional operating systems, and difficult partial-replacement composition until the shared infrastructure earns confidence.

Simulate a cohort of devices with different budgets, preferences, and stable assignment seeds. This can test individual coverage, equivalent-work distribution, replica-versus-breadth policies, update churn, and convergence without downloading a separate full archive for every simulated user. Metadata-only simulations should be distinguished from actual acquisition and offline tests. Include at least one real end-to-end path through the pilot's supported content adapter.

Require evidence for interrupted-download recovery, corrupt-mirror rejection, equivalent-work fallback, realistic final and peak-space accounting, reuse of an installed model, source-linked search or answers, and an update that survives interruption. Test network-disconnected use within the claimed pilot scope. Exercise changes such as a URL failure, source revision, new topic, conflicting community digest report, benchmark report, ontology version change, and proposed ranking-model update. Broader capabilities can be tested when their adapters are implemented.

The key acceptance condition is that ordinary growth happens through well-defined data and adapter contracts. Adding an allowed topic, catalog record, benchmark observation, or mirror should use repeatable validation without a person reading every record or the generated code. Tests should cover schema validity, stable identifiers, provenance, budget limits, dependency completeness, assignment stability, and regression cases across representative and generated inputs.

Define permitted change classes and protected boundaries. Automated admission of a declarative topic record can use an established schema and trusted validator. Changes to the validator, scoring rubric, trust roots, execution recipes, dependency versions, or release permissions need their own stronger evidence and change policy. A contribution should not be able to weaken the very check that authorizes its acceptance. Passing tests establishes the declared checks and confidence level, rather than a universal guarantee of semantic correctness.

Track escalations, recurring failure types, validation cost, and coverage growth. Use these to identify components that still require item-by-item human work. Fixed wall-clock budgets are unnecessary for the proto-requirements. Operating-cost limits can be set for a particular trial or deployment.

The maintainer acts as an architectural steward. Routine ingestion, structured extraction, scoped contributions, and policy-permitted maintenance should be delegable to agents and ordinary automation. Human involvement should concentrate on changing the structure, policies, and evaluation standards, plus exceptional cases. The pilot succeeds when it demonstrates that additional data within its scope can be handled through this process at acceptable cost. It does not need to prove the complete future stack in advance.

The requirements may be published as an open GitHub specification before implementation is justified. Include acceptance cases, machine-readable schema examples when developed, unresolved choices, and explicit status so a developer or future agent can work from a stable handoff. Potential implementers include the original project initiator, a collaborating developer interested in emergency preparedness, or independent contributors. A public specification makes that work possible, while future agent discovery and implementation remain contingent on someone or some system initiating them.

The next design choices are the pilot domain, initial ontology subset, trusted source and hash-evidence policy, canonical catalog storage and export format, coordination and retention-evidence design, distribution-versus-replication policy, language scope, and the allowed scope of automated contributions. macOS is the initial platform. Linux, Windows, and Android remain longer-term targets. License evidence is foundational, while commercial kit production is a later possibility. Domain-specific Laya training, the preference estimator, embedding model, database implementation, UI framework, and agent harness remain choices to evaluate against these requirements.

