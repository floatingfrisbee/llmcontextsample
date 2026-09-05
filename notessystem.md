# LLM Context Note System

## Purpose
A shared place to store reusable notes from AI assistant sessions — independent of any single work repo or project checkout, and independent of which assistant (Claude Code, Codex, etc.) is being used.

The goal is not to create exact legal transcripts. The goal is to preserve the most useful current context so future sessions can restart quickly with less repetition, while also giving Jaspreet a readable record of current state, decisions, and open questions.

This system is intentionally generic. It can hold repo-specific coding notes, cross-repo engineering topics, general research notes, life/admin/planning topics, or completely non-technical discussions — a given topic can be repo-specific when needed, but the storage model stays the same either way.

## Notes Location

This file sits at the root of the notes directory, alongside `sessions/`, `topics/`, `principles/`, and `bootstrap/`. Wherever that directory happens to live on disk, these instructions apply the same way — nothing below depends on a specific path, or on it being a git repo. See `Git Setup (Optional)` at the bottom for the versioning/portability case for treating it as one.

## Folder Structure
- `sessions/`: short per-session summaries
- `topics/`: longer-lived topic files that accumulate durable context over time
  - `topics/_index.md`: a quick map of current topics and what each one covers — check here first when looking for a relevant topic
- `principles/`: standing rules for how to work, not tied to any one subject
- `bootstrap/`: setup files for bringing the note system onto a new machine

## Three Kinds of Notes

### Session Notes
Use a session note for a compact, timeline-oriented record of one specific session — useful for a quick context check later, e.g. "what were we thinking last session when we worked on CRM integration?" Session notes carry more detail than topic notes, but they aren't meant to be a durable or forensic record — just enough to reorient quickly, not full history.

Typical contents:
- summary of the discussion
- decisions made
- important findings
- next steps

Session notes are useful for:
- remembering what we covered on a specific day
- capturing progress on an ongoing issue
- creating a checkpoint before switching topics

### Topic Notes
Use a topic note for a subject — technical or non-technical — that's long-lasting, where we add more information and context as we iterate on it over time. Topic notes read like specs: how a specific area relates to the business/product, favoring general, durable knowledge over minutiae or timeline-specific detail.

Typical contents:
- durable background context
- current theory or understanding
- constraints
- key decisions
- unresolved questions
- important evidence or references

Topic notes are useful for:
- recurring coding issues
- product ideas
- research topics
- personal planning topics
- any subject that benefits from cumulative context

Topic notes are also living specs, not only historical memory. For product features, domains, and recurring workstreams, treat the relevant topic file as the current human-readable spec for that subject:
- intent and business/user goals
- current expected behavior
- business rules and requirements
- key decisions, constraints, and tradeoffs
- unresolved questions
- implementation notes only when they clarify product behavior

When a conversation changes a feature's requirements, rules, or expected behavior, update the topic note so it remains current. Prefer keeping the note organized around current truth over appending a purely chronological diary.

### Principle Notes
Use a principle note for code, design, maintainability, and organization concepts that should be followed to keep repos healthy, maintainable, and understandable in the long term — especially as more of the codebase is written by AI assistants rather than held in one person's head.

The test: would this still make sense as guidance if the current topic, repo, or product area were swapped out for a different one? If yes, it's a principle. If it only makes sense in the context of one subject, it belongs in that topic file instead.

Typical contents:
- a default approach or bias (e.g. how to handle changes in legacy code)
- a standing constraint on how the assistant should behave
- a cross-project convention

Principle notes are useful for:
- engineering defaults that should apply across every repo
- working-style preferences that aren't specific to one topic

Unlike topics, principle notes are small in number and meant to be read in full every session — they are the closest thing this system has to standing instructions, so keep each one short and genuinely general-purpose. If a principle file starts accumulating repo-specific detail, that detail belongs in a topic file instead, cross-linked from the principle.

Currently in `principles/`:
- `principles/build-to-existing-patterns.md`
- `principles/engineering-principles-legacy-core.md` (moved from `topics/`; the fuller, actively-maintained ruleset — static service ban, thin controller rule, project reference direction, check-in checklist, settings-schema checklist, and more)

## Loading at Session Start

At the start of every session:
- Read `notessystem.md` (this file)
- Read every file in `principles/` in full — these are standing instructions, not conditional context
- Check `topics/_index.md` for a quick map, then read the topic files relevant to the current work
- Treat relevant topic notes as current context and living specs
- Update topic notes when the current state, decisions, requirements, or open questions change
- Save a short reusable session summary in `sessions/` when meaningful progress was made

Prefer concise reusable notes over verbatim transcripts. Prefer appending to or reorganizing an existing topic when the subject is clearly the same, and start a new topic when the subject has materially changed. If something durable and general-purpose surfaces mid-session, add or update a file in `principles/` rather than folding it into a topic.

## How to Interact with the Notes System

Most of the time you don't need to think about any of this — the loading behavior above happens automatically, so the assistant already has the right context by the time you start talking.

When you want something explicit — saving a session, starting or updating a topic, adding a principle, or making sure a specific topic gets loaded — just tell Claude or Codex directly, in plain language, what you want. For the most part it should be able to figure out where that belongs, using the descriptions above, and just do it.

Useful prompts include:
- `save this session`
- `update the topic notes`
- `start a new topic for X`
- `append this to the existing topic`
- `summarize what we learned and save it`
- `add a principle for X`
- `update the principle notes`
- `load the topic on X before we start`

## How We Decide Where to Save Things

### Append to an Existing Topic File When
- the conversation is clearly about the same underlying subject
- the new information would help future sessions on that topic
- we are refining an existing theory, diagnosis, plan, or decision trail

Examples:
- continuing work on the same Entra auth issue
- revisiting a repo-specific bug already documented
- adding new findings to a travel plan or research topic

### Start a New Topic File When
- the subject has materially changed
- the old topic would become confusing if mixed with the new one
- the new discussion deserves its own long-lived context

Examples:
- moving from auth debugging to deployment strategy
- switching from a repo bug to a broader architecture discussion
- changing from one non-code topic to a clearly different one

### Save a Session Summary When
- we want a dated checkpoint
- we made meaningful progress worth preserving
- the conversation included findings or decisions that may matter later

In many cases we do both:
- update the long-lived topic file
- save a short session summary for that specific chat

### Save or Update a Principle Note When
- a rule or bias about *how* to work is established, changed, or confirmed
- the guidance would apply regardless of which repo or topic we're in
- we catch ourselves about to write the same standing instruction into a second topic file — that's a sign it belongs in `principles/` instead, cross-linked from both topics

If a topic file already contains something that reads as a general rule rather than subject-specific context, move that piece into a principle note and leave a short pointer behind in the topic.

## Naming Conventions

### Topic Files
Topic files should be named by subject, not automatically by repo. When a topic file is added, renamed, or removed, update its entry in `topics/_index.md` in the same pass.

Examples:
- `entra-auth-iframe-timeout.md`
- `solar-system.md`
- `falcon-search-performance.md`
- `travel-planning-italy.md`

If the repo name is important context, it can appear in the topic name, but the system stays generic.

### Session Files
Session files are dated summaries.

Typical pattern:
- `YYYY-MM-DD-topic-slug.md`

### Principle Files
Principle files are named by the rule itself, not by the topic that surfaced it.

Examples:
- `build-to-existing-patterns.md`
- `legacy-core-area-changes.md`

## What Makes a Good Note
A good note should optimize for future usefulness.

That usually means:
- short summary first
- concrete findings over raw transcript text
- decisions and tradeoffs called out clearly
- open questions listed explicitly
- enough context to resume the topic later without re-explaining everything

## What We Usually Avoid
- full verbatim chat dumps unless specifically wanted
- excessive low-value detail
- mixing unrelated topics into one long file
- tying the whole system too tightly to one project or storage location

## Git Setup (Optional)

Nothing above requires a particular storage mechanism, but putting the notes folder in its own small git repo — separate from any project repo — makes it portable and lets changes be committed and reviewed over time.

New machine setup, after cloning:
- Claude Code: copy the "Note System" section from `bootstrap\CLAUDE.md` into the global `CLAUDE.md` (`C:\Users\<you>\.claude\CLAUDE.md`) — it's already written as the loading pointer.
- Codex: copy `bootstrap\AGENTS.md` to the global Codex instructions file (`C:\Users\<you>\.codex\AGENTS.md`) — it's already written as the loading pointer.

Keep note-loading guidance in the global/user instructions file only (`CLAUDE.md` or `AGENTS.md`), not in any repo-level file — do not create or duplicate a repo-level `AGENTS.md`/`CLAUDE.md` just for note-taking guidance. Both bootstrap files above should stay minimal pointers; substantive note-system guidance belongs here, not in the bootstrap copies.
