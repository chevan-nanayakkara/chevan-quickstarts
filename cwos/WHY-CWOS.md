# Why CWOS Exists

**Chevan Nanayakkara · June 2026**

---

AI-augmented work has a knowledge layer underneath it: the conventions you rely on, the decisions you've made, the procedures you reuse. Most of that layer is currently scattered. Tool-specific settings files. Conversation histories nobody re-reads. One person's head. The folder a tool wanted you to put things in three software versions ago.

CWOS organizes that layer so it survives tool changes, session restarts, and the passage of time. The argument for the architecture runs below.

---

## What CWOS means

**CWOS** stands for **Conversational Work Operating System**. Each word matters.

**Conversational**: the work happens through dialogue. Most visibly, human-to-AI dialogue (Claude Code sessions, Cursor chats, agent runs). Also human-to-human dialogue when the conversation itself is the work product (planning, decision-making, brainstorming, structured back-and-forth that produces durable outputs). And human-to-self dialogue (writing to think, working through a problem on paper). These all share the same shape: ideas develop through exchange, decisions emerge from back-and-forth, and what's left over is the artifact you can refer to later.

**Work**: the dialogue produces something. Decisions, plans, code, content, processes, conventions. CWOS is not about casual chat. It's about dialogue that yields outputs you'll want to preserve, refer back to, and build on.

**Operating System**: a layered structure that runs underneath your work and provides services so individual tasks don't have to reinvent them. A software operating system handles memory management, scheduling, the file system. CWOS handles configuration (rules), accumulated state (decisions and memory), and reusable procedures (skills) so AI sessions, future-you, and human collaborators don't have to rebuild conventions from scratch on every restart.

Put together: a way of organizing your work so the dialogue-based parts accumulate value over time, across sessions, across tools, and across people.

---

## Why this exists

Doing AI-augmented work without an operating system underneath it has compounding costs. Tool-specific configurations multiply. Conventions drift apart across tools. Important decisions get lost when you (or someone on your team) move on. AI sessions burn time and tokens loading content that's mostly irrelevant to the current task.

CWOS solves this by splitting the content underneath conversational work into three layers (configuration, state, procedure), keeping each layer in one canonical location, and making vendor-specific entry points (CLAUDE.md, AGENTS.md, Cursor rules, and so on) into thin pointers rather than content duplicates.

The friction is real if you've worked with AI agents for more than a few weeks. Four examples:

**1. Memory scattered across tool-specific paths.** Claude Code has per-project memory at `~/.claude/projects/...`. Cursor wants rules in `.cursorrules`. Codex wants `AGENTS.md`. Each tool ends up with a slightly different version of your "here's how we work" guidance, and they drift apart as you tune one and forget the others.

**2. Context bloat in always-loaded files.** The natural reflex is to put all your conventions in CLAUDE.md so the AI reads them at session start. But CLAUDE.md auto-loads into every session, including ones where you're just asking a one-off question. Every token in CLAUDE.md is per-session token cost. Eventually you're paying to load 5,000 tokens of conventions to answer "what time is it."

**3. Project resumption pain.** Come back to a project after two weeks away. The AI has no memory of what you were doing. You have no memory of what you were doing. The git log is technically true but not useful. Reasoning has to be rebuilt from scratch every time.

**4. Decision archaeology.** Six months in, you find a file with a weird convention and you can't remember why it's that way. Was there a reason? A constraint? A lesson learned the hard way? Without explicit decision records, "why did we do this" decays to "I don't know, leave it alone."

These aren't catastrophic by themselves. They compound. After a year of AI-augmented work without an operating system around it, the friction is real enough that you start dreading the setup phase of every new project.

---

## The approach: three layers

Organize the content underneath your conversational work into three layers. Each gets a canonical home. Each gets a different loading pattern. The three layers also map onto how knowledge actually accumulates in conversational work, which is why the framework feels stable even as the tools underneath churn.

**Configuration** lives in `AICONFIG.md` at the repo root. Always-on guidance: writing conventions, file naming rules, git workflow, behavioral standards. Read once per session and applied uniformly to whatever follows. This is the *reasoning* layer (the rules that govern everything).

**State** lives in `operations/cwos/memory/`. What's been decided, captured, learned. **Architecture Decision Records (ADRs)** are short markdown notes that capture one cross-cutting decision with its context, the options considered, the choice made, and the consequences. For example, an ADR titled "Use filesystem-based memory rather than a vector database for canonical knowledge" walks through the trade-offs and explains the choice. **Per-project re-entry briefs** capture a project's current state, last meaningful work, open questions, and next action, so future-you (or a fresh AI session) can resume after a break. **Feedback memories** capture user preferences and corrections worth remembering from session to session. All of it gets read on demand by topic. This is the *memory* layer.

**Procedure** lives in `operations/cwos/skills/`. Multi-step workflows with branching ("when the user says X, check Y, then do A or B depending on Z"). Authored in Anthropic's Agent Skills format. Activated on demand when a task matches a skill's description. This is the *inference* layer (runnable knowledge that produces consistent output).

Vendor entry points (CLAUDE.md for Claude Code, AGENTS.md for cross-vendor tools, future ones for whatever comes next) are thin pointers that say "read AICONFIG.md." They don't duplicate content. Adding a new AI tool to your stack means adding a new thin pointer, not migrating your conventions.

For larger setups, CWOS also describes a hub-and-spoke pattern. One repo serves as the canonical reasoning hub (where decisions live, where conversation files accumulate, where memory deepens). Project repos serve as spokes where code and output work happens, with thin pointers back to the hub. For solo work, a single repo can act as both hub and spoke.

The three layers (memory, inference, reasoning) together give your knowledge a lifetime measured in years rather than sessions. As AI tools improve and inference gets cheaper, the bottleneck shifts from "can the AI do this task" to "does the AI know what we've already decided about this task." CWOS-style memory and procedure capture is what turns better AI from a transient advantage into a durable one.

---

## CWOS doesn't require git

A reasonable assumption from the above is that CWOS is git-shaped. It isn't. CWOS is filesystem-shaped. Git is the recommended substrate because it works well, but the framework only requires:

- A filesystem the human and the AI can both read from
- Plain markdown files (or any text format the AI reads natively)
- Consistent, predictable locations (so vendor entry points can point at canonical content)

Everything else is implementation detail. CWOS could run on a shared cloud folder, a network drive, a local filesystem with manual backups, or any storage substrate that exposes files at stable paths.

Git gets recommended because:

- Version control comes free (commit history answers "why did we change this?")
- Hosting is free and ubiquitous (GitHub, GitLab, Codeberg, self-hosted alternatives)
- Distributed collaboration is the default
- AI coding tools already assume git as the substrate

But none of those benefits are required for CWOS to function. Where this doc says "git markdown," substitute "markdown at consistent paths" and the architecture holds.

---

## Token economics

The three-layer split is also the right answer to a quieter problem: every token loaded into an AI session costs money or burns context window space. The architecture isn't just clean. It's cheap.

The math, roughly. Suppose your `AICONFIG.md` plus auto-loaded memory totals 5,000 tokens. If you run 100 sessions in a month, that's 500,000 tokens of always-loaded content before you ask a single question. The cost in dollars depends on the model, but it accumulates whether the content is relevant or not.

Bigger context windows don't fix this. They give you more headroom for the actual task; the recurring baseline cost stays the same.

There's a quieter cost too. Model attention drifts when context is large. Stuffing every rule, every memory, and every procedure into the always-loaded file means the AI is reading a lot of irrelevant content alongside the relevant content on every session. Performance can degrade as the relevant-to-irrelevant ratio worsens.

CWOS responds with three loading patterns:

- **Configuration** (AICONFIG.md) always loads. Keep it tight. Cost: fixed, paid every session.
- **Procedure** (skills) loads on demand when a task matches. Cost: zero baseline. Pay only for skills that actually trigger.
- **State** (memory) loads on demand by topic. Cost: zero baseline. Pay only for memories actually retrieved.

The result is per-session economics that don't degrade as the project grows. You can accumulate thousands of ADRs, dozens of skills, hundreds of conversation files, and the always-loaded baseline stays small.

---

## Using CWOS to shape session context

CWOS provides the canonical homes for configuration, state, and procedure. What you do with those homes is up to you. One usage pattern that earns its keep over time is deliberately shaping each AI session's working context by choosing which memory files to load.

When I start a new AI session, I'm not at the mercy of whatever the tool happens to auto-load. I can:

- Load only the memory files relevant to the task at hand (a specific ADR, a project's re-entry brief, a specific feedback memory)
- Load the same memory files I used in a previous session to resume work without rebuilding context
- Load a different combination to deliberately shape the AI's working assumptions for this specific task
- Skip memory files entirely when I want a clean baseline session

This solves two practical problems that come up daily:

**Session state preservation when you exit.** Closing an AI tool doesn't lose your reasoning. The decisions and project state were already in memory files; the AI's session memory was always derivative. You can quit mid-thought, restart tomorrow, ask the AI to re-read the relevant memory files, and pick up exactly where you left off without depending on the vendor's session-recovery features.

**Cold-start configuration.** Starting fresh usually means a few minutes of "okay, here's the context you need" exposition. With CWOS-shaped memory, that becomes a short prompt: "read `projects/<project-name>/re-entry.md` and `decisions/<relevant-adr>.md`, then summarize where we left off." The AI absorbs canonical context in seconds rather than getting re-taught the basics every session.

This isn't part of the CWOS spec. It's how to use CWOS effectively. The spec gives you a place to put memory; the practice is using that place deliberately.

---

## Future-proofing: structure as substrate

It would be easy to read everything above as "this is just a smarter way to organize files for AI to find." That framing is incomplete. The real intent is forward-looking.

Today, AI tools consume CWOS content roughly the same way a human does: they read named files when pointed at them. The structure helps AI find relevant content, but the AI is still doing flatfile-level retrieval.

What CWOS sets up underneath that surface usage is a substrate that maps cleanly onto where AI capabilities are heading:

- **Graph traversal.** ADRs reference other ADRs ("superseded by ADR-007"). Re-entry briefs reference decisions. Skills reference reference docs. The cross-reference structure is already a graph, written in plain markdown. As AI tools get better at traversing graphs to assemble context, CWOS-shaped repos are already query-able as graphs.
- **Vector and semantic indexing.** ADRs, skills, and feedback memories are self-contained semantic units. Each is small enough to embed coherently and structured enough to retrieve usefully. When the next generation of tools indexes a CWOS repo, the chunks are already cut along natural boundaries.
- **Retrieval-augmented reasoning at scale.** Today's RAG systems retrieve from corpora that weren't designed for retrieval (PDFs, scraped wiki pages, unstructured Slack histories). Retrieval quality depends on how the corpus is shaped. A CWOS-shaped repo is shaped *for* retrieval.
- **Programmatic operations on knowledge.** Audits, summaries, drift detection, pattern surfacing. Anything an AI tool can do over structured corpora becomes possible against a CWOS repo without bespoke tooling.

CWOS is not the destination. It's the substrate. The structural choices make today's flatfile retrieval work, and they pre-position the same content for whatever comes next. As AI capabilities improve, CWOS-shaped repos absorb the improvements automatically.

The alternative is restructuring later. If your AI-augmented knowledge lives in unstructured form today (one big CLAUDE.md, conversation histories nobody re-reads, decisions in your head), then every capability improvement in AI tools requires retroactive structuring work. CWOS does the structuring up front, while the decisions are still fresh, in exchange for compounding leverage later.

---

## Where this fits: PKM, second brain, and other AI concepts

CWOS sits in two adjacent conversations: personal knowledge management (PKM) and emerging AI patterns.

### Compared to "second brain" and PKM

The "second brain" framing (Tiago Forte, 2022) and the PKM family (Zettelkasten, evergreen notes, Obsidian, Notion, Roam) all share a goal: capture, organize, retrieve. Notes you take should be findable later. Knowledge you've accumulated should survive forgetting.

CWOS overlaps on capture and retrieve. The state layer is a notes garden in its own way: ADRs are dense decision notes, re-entry briefs are project notes, feedback memories are pattern notes.

But CWOS is doing something the PKM family generally isn't:

- **AI tools are first-class readers.** PKM systems optimize for human retrieval. CWOS also optimizes for AI retrieval at session time. Canonical file paths, machine-readable frontmatter, and explicit thin-pointer entry points matter more than they do in a human-only system.
- **Operating system, not knowledge garden.** CWOS covers procedure (skills) and configuration (rules) alongside memory. PKM systems are mostly memory.
- **Vendor-neutral by design.** A second brain typically lives in one tool. CWOS lives in git markdown that any tool can read.
- **Event-driven capture.** PKM systems often require an ongoing capture-and-review discipline. CWOS asks you to record decisions when you make them and procedures when you reuse them.

A second brain is "a brain you can rely on for retrieval." CWOS is "an operating system you and your AI tools both work against." Different surface, different problem, can coexist.

### Compared to other AI concepts

Several AI-native concepts overlap with CWOS in various ways. Each solves a slice of the problem; CWOS gives them a place to live:

- **Agent Skills (Anthropic, December 2025)**: a format spec for reusable procedures. CWOS adopts the format wholesale for the procedure layer.
- **MCP (Model Context Protocol)**: a protocol for AI tools to call external services. CWOS references MCP configuration as standing knowledge; skills can invoke MCP tools.
- **RAG (Retrieval-Augmented Generation)**: a retrieval pattern. CWOS does manual retrieval via explicit file references rather than vector similarity. Same goal.
- **Subagents and agent orchestration**: spawning specialized AI instances. CWOS doesn't orchestrate, but skills are the unit handed off and shared memory gives multi-agent setups common ground.
- **Vendor memory features** (ChatGPT memory, Claude projects, Cursor rules, Copilot instructions): CWOS treats them as thin entry points pointing at canonical content.
- **System prompts**: vendor-set instructions applied uniformly across sessions. CWOS's AICONFIG.md is a project-level extension of the same shape.

CWOS doesn't compete with any of these. It defines where things go. When a new AI concept emerges, CWOS asks: configuration, state, or procedure? Then routes the content to the appropriate layer.

---

## Friction vs. automation: when to choose this over vendor defaults

The honest tradeoff: CWOS adds friction compared to just using whatever the AI vendor ships with their tool.

Use Claude Code without CWOS and you don't have to author AICONFIG.md. You don't have to scaffold operations/cwos/. The vendor's default memory and tool conventions just run. Less work to start. The same is true across the field. Cursor without CWOS means no `.cursorrules`. ChatGPT without CWOS means no structured conversation history. Microsoft Copilot, deployed enterprise-wide, gives the workforce defaults that "just work" for most casual interactions.

The friction CWOS adds: setup time, maintenance discipline, cognitive overhead about where things live. None catastrophic, none zero.

Where CWOS wins despite the friction:

- **Vendor independence.** When a tool gets discontinued, gets worse, or gets replaced, vendor-specific work has to be migrated or rebuilt. CWOS-shaped work just gets a new thin pointer.
- **Knowledge survival across personnel changes.** "The person who knew that decision left the company" is a chronic problem. ADRs and re-entry briefs in git markdown are durable.
- **Cross-team and cross-tool consistency.** A team using Claude Code, Cursor, and Codex against the same CWOS-shaped repo gets consistent behavior across all three.
- **AI capability upgrades compound.** Every improvement in indexing, traversal, or retrieval acts on whatever's in your repo. CWOS-shaped repos absorb capability improvements automatically.

Where vendor defaults win:

- **Quick experimentation.** Trying out a new tool for a week. Use defaults; decide later.
- **Solo, low-continuity usage.** "Ask the chatbot, use the answer once." CWOS is overkill.
- **Tools without a file-based context layer.** Some enterprise deployments (Microsoft Copilot in Office, for instance) don't expose one. CWOS as designed doesn't fit directly.

For enterprises, the realistic pattern is hybrid: vendor-default-everything for individual contributors using AI casually, plus CWOS-style infrastructure for the work that has to outlast vendor churn and personnel turnover. Strategic decisions, architectural choices, ongoing project state, client engagement context. Not every interaction needs CWOS-level care. The ones with continuity value do.

The friction is real. The argument for paying it is that the friction tax bought you something durable, and "durable" matters more as AI tooling churns faster and personnel changes faster.

---

## Beyond developers

I built CWOS for myself, and my "myself" splits roughly in half between software development and everyday AI-assisted knowledge work: writing essays, doing research, managing personal admin, drafting strategy documents, thinking through problems in dialogue with AI. The framework works the same way in both cases.

This doc reads developer-oriented partly because developers are CWOS's first natural audience. They already use git, they already write markdown, and the friction of "every AI tool wants its own config" hits them hardest. But the underlying pattern doesn't require code in the repo.

Use cases the same architecture covers:

- **Writers and researchers.** AICONFIG.md holds voice and style conventions. Memory holds research notes, sources, decisions about argument structure. Skills hold reusable workflows like outline-from-source or fact-check-pass. Conversation files capture the back-and-forth thinking that produces each piece.
- **Consultants and knowledge workers.** AICONFIG.md holds client-specific conventions. Memory holds meeting decisions, project state for paused engagements, lessons learned. Skills hold reusable workflows like meeting-prep or deliverable-review.
- **Operators running businesses or organizations.** AICONFIG.md holds tone and policy guardrails. Memory holds decisions and vendor research. Skills hold workflows like quarterly-review-prep or incident-response.
- **Personal work that benefits from durable conventions.** Relationship admin, health tracking, financial planning, family logistics. The three-layer split works at smaller scale.

CWOS also works for human-only teams, even before AI gets involved:

- **Configuration** = team handbook, coding standards, brand guidelines
- **State** = ADRs, re-entry briefs, meeting decisions worth preserving
- **Procedure** = deployment runbooks, onboarding checklists, incident response playbooks

AI is the first user that benefits from having all of this in canonical markdown locations, because AI sessions actually read it. Humans benefit too, just slower: a new teammate learns the team's conventions from AICONFIG.md in 20 minutes. A returning teammate skips a day of "what was I doing?" by reading the re-entry brief.

Non-developers may have similar or more sophisticated existing processes I don't know about. PKM systems are common among writers and researchers. SOPs, runbooks, and playbooks are common in operations. What CWOS adds is the AI-readability angle: conventions in git markdown at canonical locations get read natively by any AI tool, with no re-explanation per session.

If you don't have a git repo for your non-code work, you can. A private GitHub repo is free. The barrier is one initial setup; after that, the framework runs.

---

## Who it's for, who it's not for

**For:**

- Solo operators doing AI-augmented work across multiple repos and multiple tools
- Small teams that want consistent AI tooling across projects without vendor lock-in
- Teams that want their human-human conventions, decisions, and runbooks captured in the same durable layer the AI reads from
- Anyone tired of duplicating conventions across half a dozen tool-specific config files
- Anyone whose AI tooling works fine until they restart a session or open the project after a break

**Not for:**

- One-off projects with no continuity needs
- People who prefer their AI tooling completely managed by one vendor's defaults
- Anyone who'd rather their reasoning live in vendor-managed cloud storage than in markdown they control directly

---

## Why it's free

I built CWOS for myself. It started as an answer to my own friction: running Claude Code across five different repos and copy-pasting the same conventions across half a dozen tool-specific config files every time I switched contexts. CWOS came out of refactoring all of that into one canonical location and then noticing that the structural idea generalized.

I'm putting it in the public domain because the idea is small enough that it shouldn't need permission to use. The only reason I haven't already encountered other people's versions is probably because nobody bothered writing it up. If you've solved this differently, fine. If you haven't and you're hitting the same friction, take this. No attribution required, no commercial restrictions, no community-building obligations. Unlicense.

If it helps you, take it. If you improve it, send a PR (see [CONTRIBUTING.md](../CONTRIBUTING.md)). You don't have to.

---

## Status

CWOS is running in production today. The canonical spec evolves as real friction surfaces. This starter at [`chevan-quickstarts/cwos/`](.) is a periodically-synced derivative of the maintainer's reference implementation. Expect minor edits as the spec stabilizes; major structural changes get documented in [CHANGELOG.md](CHANGELOG.md) with migration notes.

---

## In short

Conversational work has structure. The structure has friction. Most people are paying the friction tax every day without naming it. CWOS names it, splits it into configuration, state, and procedure, and gives each piece a canonical home in git markdown that any AI tool and any human collaborator can read.

Take it. Adapt it. Improve it. Ignore it. The architecture is in the public domain because it's small enough to be common knowledge, and locking it up would defeat the point.

---

## Quick glossary

Plain-language definitions of the AI and developer terms used above, for readers who'd like them:

- **AI agent / AI tool**: a program like Claude Code, Cursor, Codex, or ChatGPT. Anything you have a conversation with that produces work output.
- **Repository (repo)**: a folder of files tracked by git or another version-control system. Most software projects live in repos. CWOS expects content to live in one.
- **Git**: a free, widely-used version-control system. Tracks file changes over time, lets multiple people edit the same files without stepping on each other. **GitHub**, **GitLab**, and **Codeberg** are popular places to host git repositories online.
- **Markdown**: plain text with simple formatting (bold, italics, headers, lists). The same formatting Slack and Discord use for messages. This file is written in markdown.
- **Frontmatter**: a small metadata block at the top of a markdown file, usually in YAML format (key-value pairs like `name: ...` and `description: ...`).
- **Token**: roughly, a chunk of text the AI model processes. A typical English word is about one or two tokens. AI providers charge by tokens (input plus output), and tools have per-session token budgets.
- **Context window**: the total amount of text an AI tool can hold in working memory for a single session.
- **ADR (Architecture Decision Record)**: a short markdown document capturing one cross-cutting decision: context, options considered, choice made, consequences. Example: an ADR titled *"Use filesystem-based memory rather than a vector database for canonical knowledge"* walks through the trade-offs and explains the choice. Lives in `operations/cwos/memory/decisions/`.
- **Re-entry brief**: a per-project document with the project's current state, last meaningful work, open questions, and next action. Lets you or an AI session resume after a break. Lives in `operations/cwos/memory/projects/<project-name>/re-entry.md`.
- **Feedback memory**: a short note capturing a user preference or correction worth remembering from session to session.
- **API (Application Programming Interface)**: a way for software to talk to other software programmatically.
- **SDK (Software Development Kit)**: a library developers use to build against a specific platform.
- **MCP (Model Context Protocol)**: Anthropic's protocol for AI tools to call external services in a standard way.
- **RAG (Retrieval-Augmented Generation)**: a pattern where the AI fetches relevant documents at query time and uses them to inform the answer.
- **Vector database / vector store**: a database that stores text as numerical vectors so similar content can be retrieved by meaning rather than exact string match. Foundation for RAG systems.
- **PR (Pull Request)**: a GitHub-and-similar mechanism for proposing changes to a repository. Fork or branch, make changes, open a PR; reviewers comment and merge.
- **SOP / runbook / playbook**: step-by-step procedures, same idea with different industry vocabulary. **SOPs** (standard operating procedures) live in compliance and operations. **Runbooks** live in DevOps. **Playbooks** live in incident response and sales.
- **PKM (Personal Knowledge Management)**: the practice of capturing and organizing your own notes, references, and connections over time. Notable systems: Obsidian, Notion, Roam, Logseq.

---

## What to read next

- **[CWOS.md](CWOS.md)** for the architectural spec (hub-and-spoke variants, three-layer split, vendor-agnostic principle, foundation files, the optional aiconversations extension).
- **[CWOS-SETUP.md](CWOS-SETUP.md)** to bootstrap a new repo or to migrate an existing one with `operations/conversational-work/` predecessor infrastructure.
- **[operations/cwos/reference/taxonomy.md](operations/cwos/reference/taxonomy.md)** if a term in the spec is unfamiliar.
- **[README.md](README.md)** for the inhabitant overview and three consumption patterns.

---

[End of Document]
