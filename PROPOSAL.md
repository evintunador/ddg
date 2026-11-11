# DDG

## What we want to build

Imagine downloading a useful slice of civilization onto a drive. Books, maps, repair manuals, engineering references, source code, software that actually runs, local AI models, music, and whatever else you would want around if the internet disappeared. You pick how much space to give it. The system figures out what fits, downloads it, and leaves you with something you can use offline.

That part already exists in various forms. Solomon sells a curated package, Kiwix provides offline readers, and Svalbard builds portable archives from recipes. The interesting part for DDG is how the collection gets chosen, how it improves over time, and what happens when a lot of people use it. The [comparison with existing projects](#existing-projects-and-what-ddg-would-add) goes into what to reuse and where this proposal differs.

A person with limited laptop storage should get a compact, useful collection. Someone filling a stack of drives should get much deeper coverage. Increasing the budget should add material to the collection they already have. Nobody should need to manually assemble the whole thing or become its full-time curator.

Now imagine that the people downloading it get somewhat different collections. Everybody gets the important stuff their device can accommodate, but they might get different, comparably good books on the same subject. People with more space also get increasingly obscure material. Taken together, those drives preserve far more knowledge than a population of identical survival-library USB sticks would.

This matters because every collection has blind spots. If everybody gets the same textbook and that textbook leaves out something important, everybody inherits that omission. Distributing several good treatments of a subject makes the population more robust to individual selection mistakes. It also preserves different explanations, traditions, and perspectives. The same principle extends from survival manuals to scientific papers, software, literature, and music.

The collection should include the tools to do things with the knowledge. A local model can search the references, help someone understand them, write code, or make an illustration. Existing programs provide the editor, model runtime, terminal, Git, and other tools. DDG puts useful material and working tools on the device and makes them easy to find.

The ambitious end of this is a civilization-recovery kit. Someone could provision drives and computers, put them in a chest with a battery, solar panels, keyboards, and portable displays, and leave it ready for a very bad day. The same software should also make sense for someone who just wants a small offline library on the computer they already own. Selling physical kits is a possible downstream use, not the initial business plan.

This repository describes the product and the decisions still to make. **Product requirement** means behavior the system needs to provide. **Proposed requirement** means an additional goal still being considered. **Candidate design** means one possible way to provide the behavior. A label applies to its following passage until the next label or heading. Examples illustrate the behavior; their incidental numbers and tool names do not become requirements. Naming an algorithm or dependency does not commit the project to using it.

## The system has three pieces

The **catalog publishing pipeline** discovers material, checks it, organizes it, and publishes information about what is available. The **local client** uses that catalog to plan and download a collection, then makes the files and existing tools usable offline. A **coordination mechanism** helps different installations choose complementary collections. Coordination might use a small shared service or exchanged snapshots; the offline archive continues working after it stops communicating.

The catalog is information about the collection, not necessarily a hosted copy of the collection itself. A book is a **work**; its original PDF and a checked text edition are different **artifacts** of that work. A **catalog revision** is a published snapshot of these records and the selection policies needed to interpret them. A **profile** is the user's saved priorities and settings. An installation also keeps a stable seed or scheduling ticket, called its **allocation identity**, so it can extend its choices rather than shuffle them every time it runs.

The **planner** decides what fits on this device. The **scheduler** coordinates selection across devices. Those responsibilities may share code, but distinguishing them makes the behavior easier to explain. The **managed archive** is just the files DDG downloaded and the metadata needed to maintain them. It does not include everything else on the computer.

Here is the whole flow. Someone chooses a practical-reference profile and gives it some disk space. The publisher has already checked several gardening manuals and determined which provide comparable coverage. The local plan covers the critical baseline, and the scheduler's assignment helps choose which qualified gardening manual this installation gets. Existing readers and a local model can use that manual and the rest of the archive. Later, the user frees more space: DDG keeps the manual and adds engineering references or other useful material. Still later, the user explicitly accepts a newer catalog revision that corrects a bad reference. That update can replace the affected managed file. The user's own code and notes were elsewhere on disk the whole time.

## What using it should feel like

**Product requirements.** Windows, macOS, and Linux are equal desktop targets. Android is worth supporting later. The person initiating the project happens to use a Mac; that is a development convenience, not a reason to design the desktop product around macOS.

There should be a simple website with normal installers for supported platforms, plus source and build instructions. After installation, someone can accept recommended settings or answer an optional short quiz. The quiz might ask whether they are mostly preparing for emergencies, building a technical reference library, preserving culture, or some mixture. It should suggest a few understandable profiles. An advanced settings screen lets them adjust storage shares across subjects, models, software, music, and other categories, along with pins and exclusions.

The user supplies a storage budget. The app shows what it intends to acquire, what that enables, and any important gaps it cannot fill. It accounts for the actual installed collection, supporting tools, indexes, and space needed to complete the download. Defaults, category weights, cutoffs, and model choices should be tuned empirically. The product does not come in a set of fixed drive-size editions.

Someone can then browse files directly, use ordinary text search, or ask a local agent to find and use relevant material. Similarity search is useful for questions that do not use the book's exact words. Answers and search results should lead back to the source book, page, section, repository revision, or media timestamp.

An app should not be the only entry point. CLI and TUI interfaces need to support provisioning, searching, inspecting the plan, and updating on machines without a desktop. The archive should remain useful through ordinary readers and file tools if DDG itself stops working.

A later user may be a different person from whoever installed the collection years earlier. Priorities, location, and language can help choose material, but the archive should not assume that its eventual reader has the original downloader's skill level.

## What belongs in the collection

**Product requirement.** The catalog must accommodate several kinds of material without treating everything as a PDF with a different filename.

| Material | Examples | What makes it usable offline |
| --- | --- | --- |
| Practical references | Survival, medicine, agriculture, repair, construction, communications | Readable text, diagrams, provenance, and enough surrounding explanation to use the reference |
| General knowledge and education | Encyclopedias, textbooks, scientific papers, courses, languages | Searchable content and relationships between introductory, advanced, and supporting material |
| Maps and geographic data | Regional street maps, topography, navigation references | Compatible readers and the actual regional data |
| Software and source | Linux, compilers, editors, engineering tools, Git repositories | Runnable packages or a declared offline build path, including dependencies |
| AI models | Language, coding, image generation, OCR, vision, embeddings, speech, translation | Weights, runtime, tokenizer and other required assets, plus a supported hardware configuration |
| Culture and recreation | Music, films, art, literature, games, history | Useful complete works, compatible players, and rights evidence |

There is importance within every domain. A default music collection might give Beethoven more weight than Tay-K because of historical influence and breadth of reference, while a user or community could reasonably choose a different music policy. Cultural priorities deserve the same explicit treatment as practical-reference priorities.

Software deserves special treatment. Preserving the Linux kernel is useful, but a kernel repository alone does not give someone an operating system they can install or rebuild. A useful package might also need boot media, compilers, libraries, firmware where redistributable, build tools, and documentation. Repositories can refer to submodules and Git LFS objects that a casual clone misses. The catalog must distinguish having source, having a runnable program, and having demonstrated an offline build. Full history versus a selected source revision is another storage tradeoff. Git bundles and package dependency closures are existing mechanisms to investigate. [Git bundles](https://git-scm.com/docs/git-bundle), [Nix dependency closures](https://releases.nixos.org/nix/nix-2.28.3/manual/command-ref/nix-copy-closure.html)

## How a useful local collection becomes a shared archive

**Product requirements.** Useful local coverage comes first. The system should estimate a practical baseline of critical information and cover it on every device that can fit it. If the budget is too small, it should explain what is missing. Rare material must not displace that baseline simply because it is rare.

The baseline needs evidence. Existing survival collections provide a starting comparison for what subjects people expect, how much space those subjects consume, and where their coverage is thin. Evaluate DDG's proposed collections against those profiles, inspect practical examples, and tune the policy. A giant manually labeled ontology of critical facts is not a prerequisite. The definition can evolve as the catalog, ranking evidence, and evaluation cases improve.

Recommended profiles protect that baseline before allocating the remaining space. Advanced users can deliberately exclude subjects or choose a specialized collection, but the app must show the resulting coverage gaps and stop describing that plan as a complete baseline. Category sliders should not accidentally erase critical coverage. The precise override interaction is still to be designed.

Once that baseline is covered, additional space buys depth, more software and models, culture, and increasingly rare material according to the user's category choices. The point at which those additions become sensible depends on measured collection costs and the selected profile. No capacity in an example should become a product threshold by accident.

Equivalence is what makes diversity possible without sacrificing local usefulness. Suppose there are several comparably good books on growing food. Instead of always sending everyone the highest-ranked book, the scheduler distributes the qualifying books across people. Each person gets useful coverage. The population retains more approaches and is less exposed to one author's omission or one curator's bad choice.

The same rule does not mean treating every candidate as equally good. A clearly inferior, unreadable, inappropriate-language, or incomplete book should not replace a good one just to make the population more diverse. Work-level quality differences still matter. Diversity should operate among collections that satisfy the local coverage requirements.

Replication and breadth are a sliding tradeoff. Another copy of an important work may be worth more than the first copy of something very obscure. At other times, making room for an otherwise absent work is the better choice. The planner needs a tunable policy for that tradeoff, with diminishing value from additional copies and explicit attention to the importance of what is being copied.

This shared-preservation behavior belongs in ordinary selection. It is not a separate opt-in partition of the drive. Network uploading is a different decision and remains optional. Storing a complete book locally does not imply permission to upload it, and DDG should not silently turn on seeding.

Preserve complete files or useful bundles. Someone who carries a drive away should have things they can read or run, not fragments whose only value depends on finding a crowd of other surviving disks. Copies on separate devices, owners, or locations can have different resilience value, but collecting detailed personal location information is not a prerequisite for the basic system. After disconnection, people can exchange complete works over a local network or by carrying drives between locations. Each isolated person has their own useful collection; the larger population archive becomes accessible as people establish those connections.

Diversity reduces correlated mistakes in individual works. It does not by itself fix a broken catalog policy that excludes an entire subject, a bad model that ranks everything incorrectly, or a shared software defect. Open policies and alternate catalogs are part of addressing those wider failures.

## Increasing the budget must only add files

**Product requirement.** Within a fixed catalog revision and an otherwise unchanged user profile and allocation identity, increasing the storage budget must preserve the selected files and download additional ones. It must not throw away an existing book because a new combination scores slightly better at the larger budget. It must not redownload content that is already present and verified.

The smaller allocation must be a subset of the larger allocation. It is a strict subset when the additional space is enough to fit another eligible item or complete dependency bundle. Tiny increases may leave the selected set unchanged because files are indivisible.

The desirable stronger property is path independence. A user starting with a larger budget would get the same final collection as that same user starting smaller and then expanding, provided the revision, preferences, assignment state, and relevant availability conditions match. This is a candidate strengthening to evaluate. Immediate additive expansion is the firm requirement.

For example, a user finishes downloading an emergency library and later frees more space. DDG adds an engineering reference, additional maps, or another useful model. The existing emergency books stay put. If the catalog itself is updated later because a book was superseded or a ranking error was corrected, replacing managed files can be part of that separate, explicit update.

This constraint affects algorithm choice. Solving an independent best-fit optimization from scratch for each budget can produce incompatible selections. A nested acquisition order or a planner that extends a saved plan may fit better. Extending a saved plan satisfies additive growth; matching the result of a fresh larger installation additionally requires frozen assignment and acquisition rules. Filling small gaps around a large dependency bundle is a useful counterexample to test. Dependency costs, unused space, and cross-category allocations need tests for the same property. Later scheduling observations must not silently rewrite an already accepted plan under the guise of a budget increase. The requirement is predictable growth, with globally optimal packing left subordinate to that behavior.

**Proposed requirement.** Protect the managed archive from accidental modification or deletion by ordinary tools and local agents. Read-only file permissions or a controlled update path are possible implementations. The intended protection is worth retaining in the design; the exact mechanism and authority of an explicitly authorized update still need specification.

## Where the categories and rankings come from

### Organize the world before deciding what to keep

**Product requirement.** Maintain a stable, inspectable organization of subjects and their relationships. Someone should be able to browse from agriculture to soil management, or from computing to operating systems, without depending on whatever clusters the latest embedding model happened to produce.

Reuse existing knowledge graphs where they help. Wikidata is a promising backbone for stable identifiers, multilingual labels, and cross-domain links. Its structured data is CC0 and available for download. A selected, versioned subset is enough to begin. End-user devices do not need to download the whole database merely to understand the catalog. [Wikidata downloads](https://www.wikidata.org/wiki/Wikidata:Database_download), [Wikidata licensing](https://www.wikidata.org/wiki/Wikidata:Licensing)

| Candidate source | Useful contribution | What DDG would still need to decide |
| --- | --- | --- |
| Wikidata | Broad subjects, aliases, classes, instances, and relationships | Which parts to import and which relations have useful meanings for the catalog |
| ConceptNet | Commonsense relationships and candidate dependencies | Whether a relation really establishes a learning or practical prerequisite in this context |
| OpenAlex | Research-topic organization and scholarly metadata | How research categories connect to practical knowledge and other media |
| SUMO | Formal upper-level and domain concepts | Whether its structure and licensing fit the chosen application |

These graphs have different purposes. ConceptNet's prerequisite relation concerns dependencies between actions or events; it cannot simply be interpreted as a complete curriculum. OpenAlex's supported Topics hierarchy is research-oriented, and its older Concepts system is deprecated. SUMO offers formal ontology files. Preserve the original IDs, licenses, and mapping evidence when combining sources. [ConceptNet relations and downloads](https://github.com/commonsense/conceptnet5/wiki/Relations), [ConceptNet downloads](https://github.com/commonsense/conceptnet5/wiki/Downloads), [OpenAlex aboutness](https://help.openalex.org/data/aboutness/), [SUMO](https://github.com/ontologyportal/sumo)

A subject hierarchy and a dependency graph serve different jobs. A surgery book belongs under medicine. Reading it may require background knowledge; practicing a procedure may require equipment. A software package may require an exact library version. Those relationships should have distinct meanings and evidence. A general ontology supplies a head start, not a ready-made proof of every prerequisite.

### Rank with evidence that can be revisited

**Product requirement.** The collection needs scalable importance and quality judgments. Those judgments must remain understandable and recomputable as new material arrives. Avoid a system where whichever LLM is running today invents a score and the catalog accumulates years of incompatible numbers.

Comparisons need an explicit question. For example, when choosing between two food-growing manuals for a practical offline library, consider subject coverage, applicability, reliability, readability, completeness, diagrams, prerequisites, and storage cost. If one manual mostly repeats material already selected while another fills a missing topic, that matters too. Different categories can supply different evidence. Scientific papers may have citation counts, citation diversity, arXiv categories, and venue information; repositories may have stars, releases, documentation, and build evidence. Popularity is an input whose limitations need to remain visible.

**Candidate design.** A small encoder produces embeddings that help group related items and find likely duplicates or alternatives. A trained decision model compares candidate works in pairs. For each pair it receives relevant metadata and content evidence and decides which better serves the stated purpose, or records a tie, insufficient evidence, or incomparability. A stronger LLM could generate training examples for a compact judge such as Laya. Laya would need task-specific fine-tuning and evaluation before use here. [Laya](https://github.com/NandhaKishorM/laya)

The pairwise results then support a ranking. Elo is one candidate: treat each comparison as a match that updates the candidates' relative ratings. A Bradley–Terry model fits relative strengths from a collection of pairwise outcomes and is available through existing libraries. Elo alone does not solve accumulated inconsistency: comparison order, opponents, and changing judge behavior can affect the results. Store the evidence and comparison outcomes so rankings can be refitted and checked. [choix preference inference](https://github.com/lucasmaystre/choix)

Preserve the rubric, model checkpoint, content revision, sampled passages, decision, and uncertainty. Check document-order bias, behavior on long or truncated inputs, disconnected comparison groups, stable reference comparisons, and regression cases. Teacher-generated labels help with training; independent evaluation examples are still needed to detect the teacher's mistakes.

Avoid comparing every document with every other document. Compare new items with reference items and close competitors, spend more effort near uncertain selection boundaries, cache the results, and retain some exploration beyond the nearest embedding cluster. The exact comparison schedule is an implementation decision. Its cost and ranking stability need measurement.

Embeddings should help discover related things. They should not silently redefine stable topic IDs or establish equivalence just because two documents are nearby in vector space. Ranking-model and ontology changes need versions and migrations so an old installed plan can still be explained.

## A planner has more to do than sort a list

**Product requirement.** Select a collection that fits the budget, covers useful subjects, includes needed dependencies, respects the user's category preferences, and supports the shared-preservation policy already described.

Sorting by importance and filling the disk is not enough. Several high-ranked books might repeat the same material. A slightly lower-ranked book might cover something otherwise absent. A model might appear to fit until its runtime and supporting assets are counted. A repository might be useless for an intended build without its dependencies.

The planner therefore needs artifact sizes, subject coverage, overlap, equivalence, dependencies, platform compatibility, and evidence about availability. It should count a shared artifact once even if several categories or packages use it. It should report final installed size and peak space needed for downloads, extraction, indexes, and updates.

**Candidate design.** Treat a planned collection as a sequence of useful additions, where an addition can be an individual artifact or a dependency-complete bundle. Prefer additions that fill important coverage gaps or add useful diversity for their incremental byte cost. Construct that sequence so growing the budget preserves the earlier selection. This sacrifices some freedom available to an unconstrained best-fit optimizer, but satisfies the additive behavior users need.

The minimum useful baseline, category weights, relative value of another replica, and model-versus-reference tradeoffs should be evaluated on actual collections and tuned. No example storage size or hand-wavy priority coefficient in this proposal is a fixed acceptance threshold.

## Scheduling different collections without tracking everyone's life

**Product requirement.** Coordinate allocation better than independent random weighted selection, keep accepted plans stable, and distinguish assignments from evidence that files were actually retained. A central database of every person's files is not required.

There are several workable levels of coordination. A published catalog can include a deterministic schedule, and a small shared service can issue successive assignment tickets. Clients can use those tickets, their budgets, and the same policy to choose different qualifying works. The service need not know the rest of anyone's drive. Another design records anonymous aggregate counts for assigned or completed artifacts and schedules against estimated coverage deficits. A later system could exchange evidence through peers or federated services.

**Candidate design.** Keep aggregate counts and use an attrition model. A recently completed download contributes evidence that a copy exists; the weight of that evidence can depend on age and whatever retention measurements the project actually has. Parameters should be fitted or labeled as assumptions. An installation deliberately disconnected for years may still be preserving everything, so silence must not automatically mean deletion.

A redirect wrapper is another possible source of cheap evidence. Instead of publishing the original URL directly, a link first reaches a counter service, which records the request and redirects to the upstream host. The file travels from the upstream host to the client, so the counter avoids paying for the file's bandwidth. This measures initiated requests. Completion needs a client acknowledgment after verification or other evidence. Neither event proves permanent storage, and retries or fabricated events can distort the count. The design should label what each counter measures.

Periodically published counter snapshots could let clients plan between contacts, and the snapshots could be mirrored. Static rules and locally retained seeds still provide useful diversity when no coordination service is reachable. Seeds alone do not reveal actual gaps. A membership-aware placement algorithm can avoid a per-object directory, but it still needs shared information about the participating storage. CRUSH is an example of that distinction, rather than a component this project has committed to adopting. [Ceph CRUSH](https://docs.ceph.com/en/latest/rados/operations/crush-map/)

Anonymous aggregate coordination, ticket issuance, distributed receipts, and the retention model remain design choices. Evaluate them in simulations with different budgets, abandoned downloads, copied installations, changing preferences, stale reports, and correlated device loss. The useful outputs are planned coverage, estimated retained coverage, observed copies, and uncertainty. None should be mislabeled as an exact inventory when it is only an estimate.

## Acquiring material and handling missing sources

**Product requirement.** A catalog record identifies the work and its actual downloadable artifacts. It includes source locations, version or edition, expected size, available digests, license evidence, topic relationships, and acquisition instructions. A pointer catalog lets clients download large content from upstream hosts without requiring the project to host the whole collection.

Use existing transfer tools for ordinary download work. Resume, bounded retries, mirror handling, checksums, and repair are already supported by tools such as aria2. Data should land in staging, be checked, and then become part of the managed archive. Distinguish a network problem from a missing source, wrong content, changed revision, or rights restriction. [aria2](https://github.com/aria2/aria2/blob/master/doc/manual-src/en/aria2c.rst)

There are different kinds of replacement, and each needs a clear meaning.

| Situation | Example | Expected behavior |
| --- | --- | --- |
| Exact mirror | The same PDF is available from another host | Verify that it has the expected bytes and keep the selection unchanged |
| Another representation or edition | An EPUB or revised edition of the same book is available | Treat it as a distinct artifact and check whether its content and usability satisfy the requirement |
| Equivalent work | The preferred repair manual is unavailable, but another qualified manual covers the same job | Use the validated alternative and record the substitution |
| Partial replacement | No suitable advanced textbook exists, but introductory and specialist texts cover parts of it | Report the gap initially; composing and validating the replacement collection is a stretch goal |

Mirrors should use artifact identity, not a filename or a successful HTTP response. Different editions and translations are not exact mirrors. Embeddings can suggest equivalent works, but the equivalence needs evidence about actual coverage and prerequisites. Replacement choices should also respect the local plan and population-diversity policy.

## Who checks the catalog, and how much infrastructure that takes

**Product requirements.** New material needs evidence of relevance, integrity, source identity, and licensing before it becomes a trusted catalog addition. A public PR containing a URL and a hash is a proposal, not an automatic instruction for users to download and execute it. Routine trusted additions should be automatable through defined rules, without a maintainer manually curating every item.

A hash identifies bytes. If a contributor makes a malicious file, hosts it, downloads it, and reports its correct hash, they have proved consistency with their own file. They have not established that the file is the intended publisher's release or that it is safe. A proof that some code ran has the same source-trust problem.

Start with source types that are tractable to validate: approved publishers, immutable repository revisions, authenticated release digests, signatures, and known acquisition recipes. Community reports can add useful evidence, including retrieval time, final URL, version, size, and digest. They should retain their source and confidence. Conflicting reports for the same purported artifact should trigger diagnosis rather than silently replacing an accepted identity. An authentic origin does not establish relevance to the catalog; content evaluation remains a separate job.

**Candidate operating model.** A shared catalog worker downloads proposed additions into temporary, isolated storage, computes hashes, parses and checks the material, extracts text, produces embeddings, and runs a fine-tuned judge and a small LLM for relevance and metadata work. This could run on a rented machine with suitable compute, on donated machines, or through managed jobs. It can publish compact catalog and index outputs while ordinary clients continue downloading the actual material directly from upstream.

This service is a credible eventual expense for a large, actively growing catalog. It does not have to exist before a narrow trusted-source pilot. Publisher evidence and user-verified downloads can cover some work initially. Distributed trusted workers are another option. The important distinction is between a fully offline end-user installation and the infrastructure used to produce reliable catalog releases.

Downloading and scanning a file centrally improves what can be checked. It does not prove the absence of exploits, factual errors, or malicious instructions. Format checks, source policies, isolated processing, software-specific checks, and content evaluation have different jobs. Executables, build recipes, model files, and documents need appropriately different admission rules. Manual review can be reserved for exceptions and policy changes rather than every artifact.

Attestations can identify an approved worker and recipe that produced a result. Verify the expected identity and bind the report to the source revision and output digest. A contributor's own attestation still requires a trust decision. GitHub's documentation explicitly distinguishes provenance from an assurance that an artifact is secure. Untrusted PR code should not run with release privileges or repository secrets. [GitHub artifact attestations](https://docs.github.com/en/actions/concepts/security/artifact-attestations), [SLSA provenance](https://slsa.dev/spec/v1.2/provenance), [GitHub PR security](https://docs.github.com/en/actions/reference/security/securely-using-pull_request_target)

Keep acquisition and processing cost visible. Processing can be incremental: an unchanged accepted artifact need not be downloaded, OCR'd, embedded, and judged again because someone added an unrelated topic. Pin the evidence and model versions needed to reproduce important decisions.

## PDF originals, extracted text, and storage efficiency

**Product requirement.** The system should understand representations of a work and their different costs and capabilities. Searchable text, the original PDF, extracted figures, and an EPUB are separate artifacts linked to the same work. The planner can make an informed representation choice instead of blindly keeping every form or assuming they are interchangeable.

Text or Markdown can be dramatically smaller than a scanned PDF and easier for a local model to search. It can also lose diagrams, equations, tables, page layout, and occasionally important words. OCR quality varies. For a literary essay, a checked text edition might be enough. For a carpentry manual whose instructions depend on drawings, throwing away the images can destroy much of its value.

**Candidate design.** Offer compact text editions where coverage checks support them, preserve source and page references, and retain figures or full originals where their informational value justifies the bytes. A larger plan can add the original or a better representation later without discarding the compact one. Deduplication, compression, and a model-friendly text index are worth evaluating; the smallest file is not automatically the best archive.

Detect image-only pages and missing text. Record how a representation was created and what was checked. Derived editions need their own hashes, sizes, source versions, and rights evidence. If a publisher's whole-archive ZIM or another format cannot be incrementally updated, account for the required full-artifact transfer in update planning.

## Models and tools that make the collection useful

### Reuse the existing ecosystem

**Product requirement.** Reuse existing readers, inference engines, editors, agent harnesses, and build tools whenever they do the job. DDG's contribution is the catalog, selection, acquisition, and working configuration that connect them.

A useful installation might provide Ollama or llama.cpp, a compatible local model, an existing harness such as OpenCode, and the archive location in a skill, tool configuration, or system prompt. The agent should be able to search and read the archive, cite sources, run authorized commands, use Git, and create files. VSCodium, Docker or another compatible environment, and specialist tools can be provisioned where the selected profile and platform need them. Everything required for the claimed workflow must be present before going offline. [Ollama API](https://docs.ollama.com/api/introduction), [llama.cpp](https://github.com/ggml-org/llama.cpp), [OpenCode](https://github.com/anomalyco/opencode), [Continue offline configuration](https://docs.continue.dev/guides/running-continue-without-internet)

DDG does not manage the future work people create with those programs. Their code, notes, documents, and generated images live elsewhere on disk, outside DDG's catalog, storage allocation, indexing, and update logic. DDG may tell an agent how to find the archive; that does not turn the rest of the filesystem into part of the archive. A deliberate later submission to a public catalog is a separate contribution.

Keep downloaded material as data unless an approved workflow explicitly uses it as code. A book or retrieved page should not acquire authority to change the host merely because it contains instructions. The existing harness's permission and isolation mechanisms should do as much of this work as possible. DDG should not invent a new general agent security system.

### Different model categories need different selection rules

**Product requirement.** Include major useful model categories in the catalog, including language and coding, image generation or diffusion, OCR and vision, embeddings and reranking, speech recognition and synthesis, and translation. Storage and hardware determine which are installed. Preserve complete inference bundles, including any required encoders, decoders, tokenizers, and runtime assets.

Language models can benefit from a ladder. A larger model may handle difficult work while smaller models support quick interactions or sub-agents. For image generation used to communicate an explanation to a person, one strong compatible model may be the sensible choice. Additional size tiers need to earn their storage through distinct benefits. The fact that LLM ladders are useful does not make a ladder mandatory for every model category.

Models provide a compact, lossy form of broadly useful knowledge and capability. That gives them a legitimate place in the same physical storage budget as references. Original artifacts still preserve exact text, diagrams, software history, and cultural works that model outputs cannot reliably reproduce. Evaluate combinations of models, references, and tools through useful tasks rather than treating parameter count as a universal value score.

### Recommend models from structured hardware evidence

**Product requirement.** Reuse existing installed model assets where possible. Recommend models with a realistic chance of running at useful speed on the selected hardware. Let users also archive larger models for hypothetical future hardware when they explicitly want that.

Gather published performance evidence instead of requiring maintainers to own and benchmark every machine. Agents can extract records from benchmark projects, release notes, and detailed articles. Record exact model and quantization, runtime version, GPU or integrated chip, memory, context settings, workload, concurrency, measured speed, source URL, date, and missing conditions. Preserve conflicting reports and separate measurements from extrapolations. Apple Silicon benchmark projects and DGX Spark documentation are examples of existing evidence sources. [Apple Silicon benchmark example](https://github.com/alexhiesch/apple-silicon-llm-bench), [DGX Spark documentation](https://docs.nvidia.com/dgx/dgx-spark/)

For customizable PCs, GPU model and VRAM are a useful initial grouping. Keep material qualifications such as system RAM, architecture, runtime support, and multi-GPU behavior when the evidence depends on them. A GPU name alone does not establish every runtime's compatibility.

Allow different deployment roles. A desktop that runs its editor, harness, and model together needs headroom for them. A dedicated inference server can devote more memory to a larger model while other devices run their own harnesses and query it over the local network. It still needs room for the operating system, inference runtime, and context/KV cache. A large server tier and a slightly smaller workstation tier might be useful presets. The precise tiers should come from evidence and tests. [Ollama context and memory](https://docs.ollama.com/context-length)

DDG does not need a custom battery-endurance or power-management subsystem. Hardware-kit builders can assess batteries and solar equipment separately. Reused runtimes can handle their normal resource controls.

## Updates, durability, and what lives where

**Product requirements.** A user can reconnect, request an update, and receive changed catalog data, better model options, corrected priorities, or revised content without wiping the drive. Reuse unchanged verified artifacts. Show what will change and distinguish a revision update from a budget-only increase.

Downloads can fail, disks can fill, and power can disappear halfway through an update. Stage and verify replacements, keep the installed plan consistent, and provide recovery or rollback where space permits. Changes that need temporary space must say so. Repair damaged managed files from valid sources when possible. Ordinary offline reading must continue without an account, an available website, or a fresh online check.

Pin dependencies, acquisition recipes, ontology snapshots, model checkpoints, and catalog releases. A monitor can discover upstream changes and open candidate PRs that rerun affected tests. Renovate or Dependabot can handle supported dependency types. Manual pin-update PRs are also a legitimate early policy. An upstream bump should not silently rewrite an installed collection. Changing the embedding model or text-chunking method may require rebuilding or versioning the affected indexes, while unrelated source artifacts stay cached. Update authenticity can reuse existing mechanisms such as The Update Framework. Long-disconnected archives need a defined way to recover trusted update metadata when clocks or metadata have aged. [Renovate](https://docs.renovatebot.com/configuration-options/#automerge), [Dependabot](https://docs.github.com/en/code-security/reference/supply-chain-security/dependabot-options-reference), [The Update Framework](https://theupdateframework.io/docs/overview/)

Support ordinary-format exports and transfer by removable drives or local networks. Someone should be able to put a field reference on a paper-like tablet, take an EPUB to a smaller reader, or print instructions to carry away from the computer. A machine may need updates or repairs after the original internet distribution services disappear. Preserve an exportable inventory with versions, artifact digests, license notices, and the information needed to explain what an archive contains.

Optional integrity checks should detect damaged managed files after long storage and support repair from another local drive, a permitted peer, or an upstream mirror. Their frequency is a user or deployment choice. A later recovery bundle could include offline installers, selected boot or recovery media, architecture and driver information, and instructions for restoring the archive onto a replacement computer. Restoring after host failure is a larger job than updating a working installation, so it should be evaluated as a distinct capability.

**Candidate data architecture.** Keep schemas, policies, adapter code, and manageable declarative records in Git. Use a local database for catalog queries, subject and dependency edges, search, installed state, and plans. Larger comparison logs, benchmark observations, extracted evidence, and coordination counters can live in partitioned datasets or a publishing database with versioned open exports. A database is compatible with an open-source project; an undocumented mandatory hosted service would make independent use harder.

Choose one authoritative representation of each fact. Generate snapshots and indexes deterministically where practical. Preserve source provenance and record migrations. SQLite can cover a useful starting scope, with FTS5 for ordinary search and a portable vector index if similarity search needs one. There is no requirement to introduce a dedicated graph database or vector service immediately. [SQLite FTS5](https://www.sqlite.org/fts5.html), [sqlite-vec](https://github.com/asg017/sqlite-vec)

## Existing projects, and what DDG would add

These projects solve real parts of the problem and are candidates for reuse, extension, or comparison. The comparison describes their published approaches. DDG's additions below are proposed requirements, not claims that these projects could never support similar behavior.

| Project | Existing approach | What the DDG proposal adds or changes |
| --- | --- | --- |
| Solomon Digital | A paid, curated downloadable library with a searchable interface, maps, and selectable modules | Continuously tunable storage, explicit ranking evidence, automated catalog growth, working software and model bundles, and deliberately diverse collections across users |
| Survivor Library | A broad collection of downloadable references, particularly historical practical trades and technologies | A way to select, verify, search, and distribute a useful subset without each user manually curating the whole library |
| Kiwix | Readers and serving tools for offline ZIM archives such as encyclopedias and reference websites | A cross-format planner deciding which archives and other material belong on a particular device; Kiwix can remain a reader underneath |
| Svalbard | Recipes, storage-size presets, portable archives, models, search, and desired-state reconciliation | Arbitrary additive budgets, evaluated ranking and equivalence policies, and coordinated diversity; it is a particularly close candidate to extend |
| Offline Emergency Library | Scripts assembling a reference, map, and local-AI collection from upstream sources | Finer category control, durable catalog evidence, nested budget growth, and shared allocation across different users |
| Last Light / Bunker Bot | A compact survival guide, reference PDFs, maps, and source-grounded local-model assistance | Broader subjects and formats, scalable acquisition and selection, and preservation beyond one fixed bundle |
| Project NOMAD | A Linux-oriented offline server integrating Kiwix, maps, education, and optional local AI | Equal desktop targets and an adaptive corpus policy; its integrated tools may supply parts of a larger installation |
| Internet-in-a-Box and PrepperPi | Offline knowledge served to nearby phones and computers over a local network | Selection and population preservation policies; their serving model remains useful for household and kit deployments |
| WROLPi | An off-grid library with local serving and support for collecting several media types | Explicit cross-user diversity, comparison evidence, and a shared planning model across content, software, and models |
| Survival LLM | A local model and searchable reference collection with source-linked answers | A wider archive lifecycle and allocation policy that can supply material to an existing model interface |
| Ark | A mobile-oriented combination of knowledge packs, maps, private tools, and local AI | Desktop priorities and a managed public archive; DDG leaves personal creative work outside its lifecycle |

Project sources support these descriptions. Solomon's product page presents its current bundle and acquisition terms. Survivor Library presents downloadable category collections. Kiwix documents its readers and servers. [Solomon Digital](https://go.solomonsurvivaltech.com/digital/), [Survivor Library](https://www.survivorlibrary.com/), [Kiwix](https://get.kiwix.org/en/solutions/applications/)

The closest acquisition and archive-building references are Svalbard, Offline Emergency Library, and Last Light. Check their current implementations and licenses before deciding whether DDG should extend one of them. [Svalbard](https://github.com/pkronstrom/svalbard), [Offline Emergency Library](https://github.com/elhajahmadj/offline-emergency-library), [Last Light](https://github.com/hamtechautomation/survivalai)

Server and application references include NOMAD, Internet-in-a-Box, PrepperPi, WROLPi, Survival LLM, and Ark. Their code licenses and the licenses of downloaded data are separate questions. [Project NOMAD](https://github.com/Crosstalk-Solutions/project-nomad), [Internet-in-a-Box](https://github.com/iiab/iiab), [PrepperPi](https://github.com/jmarler/prepperpi), [WROLPi](https://github.com/lrnselfreliance/wrolpi), [Survival LLM](https://github.com/anonnerdfrenzy/survival-llm), [Ark](https://github.com/rodrgds/ark)

Other experiments worth tracking include Carlos Polop's Offline Survival library builder, dedsec's Offline Survival Project, Just In Case, Apocalypse Drive, refugiOS, and survivalRAG. Source visibility, licensing, maturity, and scope vary. They offer examples of desktop builders, standalone readers, conversational retrieval, bootable environments, and prebuilt retrieval corpora. They should be evaluated as components rather than treated as interchangeable finished products. [Offline Survival](https://github.com/carlospolop/offline-survival), [Offline Survival Project](https://github.com/dedsec1121fk/Offline-Survival-Project), [Just In Case](https://github.com/companionintelligence/JustInCase), [Apocalypse Drive](https://github.com/hratterman/apocalypse-drive), [refugiOS](https://github.com/Ganso/refugiOS), [survivalRAG](https://github.com/bdkoeh/survivalRAG)

## Language, viewpoints, and community versions

**Product requirements.** Language is a separate selection dimension. Two books can cover the same topic while only one is readable to the person using the archive. Record language, script, available translations, and relevant model capabilities. Early releases may narrow their language scope, but the catalog should leave room for multilingual data and users. Translation models and machine-generated editions need provenance and quality checks before being treated as equivalent replacements.

People should be able to publish additions, corrections, alternate ranking policies, or forks of the subject graph and catalog. A default collection will reflect choices about sources, geography, language, and culture. Those choices should be visible and contestable. A community that wants better coverage of non-Western philosophy or local agricultural practice should have a practical way to build it rather than waiting for the default maintainer to become an expert.

**Candidate design.** Support versioned, attributable catalog feeds or overlays that can map to shared subject IDs while adding their own concepts and preferences. Users can select a feed or profile and inspect conflicts and origins. A community feed does not automatically inherit the default publisher's trust for executable content or acquisition recipes. Dataset licenses, export formats, and migrations should make independent publishing possible.

Preserve artifact-level license evidence, source locations, retrieval dates, restrictions, and required notices, including those for derived text or editions. A public URL is not permission to redistribute. Copyleft and noncommercial licenses have different conditions. Commercial kit exports can be evaluated later using this evidence. Select software and catalog licenses before incorporating code or data whose terms would constrain the project. [Creative Commons licensing](https://creativecommons.org/faq/), [GNU guidance on redistribution for a fee](https://www.gnu.org/philosophy/selling.en.html)

## Components that can be specified and worked on separately

The interfaces below are proposed boundaries. Agree on the records exchanged at those boundaries before dispatching independent implementation work. Each component should have fixtures and general tests that can run without requiring the whole production catalog.

| Component | Takes in | Produces | Can be evaluated through |
| --- | --- | --- | --- |
| Catalog schema and versioning | Work, artifact, source, license, and relationship records | Validated records, stable IDs, migrations, release snapshots | Round-trip exports, referential integrity, backward compatibility |
| Source adapters and catalog intake | Approved source metadata, candidate URLs, contribution proposals | Attributed candidate records and acquisition recipes | Saved source fixtures, changed pages, missing fields, bounded processing |
| Validation and evidence workers | Candidate artifacts and trust policy | Digests, parsing and source checks, extraction reports, admission evidence | Corrupt or conflicting inputs, isolated runs, reproducible reports |
| Text and representation pipeline | PDFs, ebooks, websites, and other supported formats | Searchable text, page mappings, figures where supported, representation relationships | Scanned pages, diagrams, extraction gaps, output size and fidelity |
| Subject graph and community mappings | Upstream ontology subsets and community additions | Browsable subjects, typed relations, mappings, provenance | Meaningful relation tests, migrations, conflict examples |
| Ranking pipeline | Artifact evidence, rubric, comparisons, judge checkpoint | Versioned preference evidence, rankings, uncertainty | Reference comparisons, order reversal, judge changes, held-out cases |
| Local acquisition planner | Budget, profile, fixed revision, assignment state, ranked bundles | Nested selections and explainable additions | Budget bounds, additive expansion, shared dependencies, missing sources |
| Population scheduler | Eligible works, importance policy, assignment or retention evidence | Stable assignment opportunities and coverage estimates | Simulated heterogeneous devices, attrition, equivalence, breadth versus replication |
| Downloader and managed store | Accepted plan and acquisition records | Verified local artifacts, resumable progress, repair state | Interruptions, full disks, bad mirrors, accidental modification |
| Search and agent adapter | Managed archive and indexes | File browsing, source-linked search, configured existing harness | Offline retrieval, citations, tool use without cataloging user outputs |
| Model and software provisioning | Supported hardware evidence and selected capability bundles | Working runtimes, models, tools, local-service configuration | Offline startup, dependency completeness, workstation and server roles |
| App, CLI, TUI, and packaging | Shared planning and archive operations | Desktop installers, headless interfaces, guided and advanced setup | Equivalent core behavior on desktop targets, usable error and recovery paths |
| Releases and maintenance automation | Pinned versions, change proposals, acceptance policies | Tested updates, signed metadata where used, rollback information | A source change, topic addition, dependency update, and failed promotion |

Some code and records will be shared across components. A boundary is useful if an agent can understand its inputs, outputs, and failure behavior without reading the entire project, and if another agent can test the result independently.

## How to build this without turning it into a second job

**Product requirement.** Every growing part of the system needs a scalable workflow. A maintainer should design the structure, contracts, and evaluation standards while agents and ordinary automation handle routine ingestion, metadata extraction, scoped changes, and upkeep. Requiring a person to read every new topic or every line of every agent PR would defeat a major reason to build DDG.

That means defining classes of changes whose acceptance can be automated. A new topic using an established schema can be checked for identifiers, relationships, evidence, licenses, and planner behavior. A change to the validator that approves topics is a different class of change. Contributors must not be able to weaken the checks that authorize their own contribution. Tests support confidence within a defined scope; no collection of green checks proves every factual or security claim about arbitrary internet content.

General tests should cover the behaviors that matter across many inputs: additive budget growth, critical coverage before rare additions, counted dependency costs, stable assignment identities, valid source links, extraction coverage, interrupted downloads and updates, and preserved distinctions between trusted and unverified evidence. Fixed examples are useful fixtures, but the tests should also exercise generated cases and edge conditions.

Start with a small domain such as survival guides or existential philosophy to keep acquisition and model-processing costs down. Existing local models can support the experiment. Simulate different budgets and device populations instead of downloading separate physical archives for every test user. A developer may iterate first on whatever machine they own; the desktop architecture and product targets remain Windows, macOS, and Linux.

The pilot should show a real path from candidate source to useful offline material, then survive representative maintenance changes. Add a topic, change a source URL, submit a conflicting digest, update a ranking model, expand a budget, and interrupt an update. Use the results to identify which components still require item-by-item human work. Hard-coded capacities, replica counts, and founder time allowances are not the success criteria. Empirical coverage, stable behavior, processing cost, and scalable acceptance rules are.

The public proposal can exist before current agents can build and maintain the complete system. A developer interested in the idea, a future agent team, or an independent contributor should be able to take it from here. The useful next specifications are the shared data contracts and acceptance cases, followed by evidence about whether extending an existing project is cheaper and cleaner than a new implementation.

## Decisions still open

The important behavior is defined above. The implementation still needs choices about the initial subject domain, ontology subset, default catalog and code licenses, critical-coverage evaluation, comparison rubric, judge training, nested planner, scheduler coordination, retention evidence, trusted source policies, representation tradeoffs, and publishing infrastructure.

Those decisions should be made against the product requirements and concrete test cases. The goal is a useful archive that grows predictably on one person's drive, adds up to something more resilient across many people's drives, and can keep improving without a human doing all the curation by hand.
