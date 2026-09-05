# Context Management for LLM AI Assistants

A way to organize principles, topics, and other instructions and context from your interactions with AI assistants, whether in coding sessions or for other tasks. This system helps build comprehensive and evolving context—both general and topic-specific—which can lead to faster, higher-quality results and possibly even lower token consumption.

## How It Is Laid Out

A small set of folders in which the AI assistant maintains notes related to your preferences, instructions, and context from previous conversations.

| File or folder name | Description |
| --- | --- |
| `notessystem.md` | The rules that define the notes system. These are natural-language instructions that tell an AI assistant how to create notes and store context based on your interactions. The instructions themselves can evolve over time, and you can often simply ask the AI assistant to incorporate a new idea or rule. |
| `principles/` | Contains files related to long-running principles for how work should be done, independent of any specific project, topic, or task. |
| `topics/` | Contains files related to long-running topics where context is built over time as you and your colleagues work on projects and tasks. These notes capture the vision, high-level goals, and ideas rather than minutiae. |
| `sessions/` | Contains files related to specific sessions in which you interacted with the AI assistant. These are not intended to be forensically complete; they are a scratch pad of context generated during a session so the AI assistant can use it in the near future. |
| `bootstrap/CLAUDE.md` | A sample file for initializing Claude sessions. It contains instructions to load before any new session with Claude. |
| `bootstrap/AGENTS.md` | A sample file for initializing Codex sessions. It contains instructions to load before any new session with Codex. |

## Three Kinds of Notes

The distinction between these types is what keeps the system useful instead of allowing it to become a growing pile of transcripts.

### Session

A timeline-oriented record of one specific session—quick context for answering, “What were we thinking the last time we touched this?” It is detailed, but not intended to be a durable or forensic record; it contains just enough information to reorient quickly.

Example:

```text
sessions/2026-05-22-calendar-future-indexing.md
```

### Topic

A long-lasting subject, technical or otherwise, that gains context as you iterate on it. A topic note reads like a specification and explains how the area relates to the business or product, favoring general, durable knowledge over minutiae.

Example:

```text
topics/entra-id-authentication.md
```

### Principle

Code, design, maintainability, and organizational rules that keep repositories healthy and understandable over the long term, especially as more of the codebase is written by AI assistants. Principles apply across topics and code repositories.

Example:

```text
principles/code-organization-principles.md
```

## How to Interact With the Notes System

Each assistant loads these instructions automatically at the start of a session—the rules file, the principles, and relevant topics as needed. Most of the time, you do not need to think about the mechanics. In many cases, the AI assistant will automatically derive principles and topic notes that it believes would be useful to retain.

When you want something explicit—saving a session, starting or updating a topic, adding a principle, or ensuring that a specific topic is loaded—simply tell Claude or Codex what you want in plain language. For the most part, the assistant should be able to determine where the information belongs without needing more than a short instruction.

Sample prompts:

> Can you load what we’ve discussed in the past about Entra ID authentication?

> If you haven’t already, can you save the essence of our last discussion in the topic notes for Salesforce integration? Reorganize the information so it is concise and captures the key points, the flow, and the high-level goals of our product’s integration with Salesforce.

> I would like you to strictly adhere to SOLID practices while writing code. Can you save that as a required principle?

> Before checking in code, I would always like you to do a deep pass to ensure code DRYness, test coverage, and sufficient logging. Can you save that as a required principle?

Over time, your interactions with the AI assistant will create substantial information about topics and a set of cross-cutting principles that are important and relevant to your work. They will be loaded automatically into future sessions as part of the context available to the AI assistant.

## Configuring the AI Assistant

Each assistant reads its own global instructions file at the start of a session. Point that file to the notes location once, and every future session will pick it up automatically.

### Claude Code — `~/.claude/CLAUDE.md`

Copy the **Note system** section from `bootstrap/CLAUDE.md` into the global `CLAUDE.md`. The detailed instructions live only in `notessystem.md`, so this pointer does not need to change when the process evolves.

```markdown
## Note system

At the start of every session, read notessystem.md and follow its "New Session Loading Instructions" section.
```

### Codex — `~/.codex/AGENTS.md`

Copy `bootstrap/AGENTS.md` directly into the global Codex instructions file. It contains the same minimal pointer.

```markdown
## Note system

At the start of a session, read notessystem.md and follow its "New Session Loading Instructions" section.
```

## Packaging It as Its Own Repository

You can maintain the files and folders described above on the machine where you use Claude, Codex, and other assistants. You can also make the notes directory a Git repository. As principles, topics, and sessions are updated, Git provides an automatic timeline showing how your notes have evolved.

The repository also allows you to use the same system and context across machines. If you share it with colleagues, your team can develop with AI assistants using principles and topic notes contributed by everyone. Session notes can easily be excluded when sharing the repository if appropriate.

The repository behaves like any other Git repository: clone it onto a new machine, and both Claude Code and Codex can read from the same checkout. Commit changes as you go, and the history becomes a reviewable record of how the context evolved—not just its current state. Push it to a private shared location, and the same notes can travel across machines and extend to teammates with repository access.

Because session and topic notes may contain project-specific information, treat access to the repository as you would any other repository containing internal context: private by default and shared deliberately.
