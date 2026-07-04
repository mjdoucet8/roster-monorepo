# context-v

This directory holds project context that should be durable, reviewable, and useful to both humans and AI agents.

The goal is to keep important context out of chat transcripts and scattered notes. Each file should explain enough background, decisions, constraints, and links that a future contributor can understand the work without rediscovering it from scratch.

## Folder types

- `specs/`: What should be built and the constraints that define success.
- `plans/`: Step-by-step execution plans for work that is about to happen or already underway.
- `prompts/`: Reusable prompts and agent instructions that are worth keeping and improving.
- `blueprints/`: Proven patterns that can be reused across projects or features.
- `reminders/`: Small but important project facts, preferences, and decisions that are easy to forget.
- `explorations/`: Research notes, investigations, experiments, and paths considered.
- `issues/`: Named problems, bugs, risks, and failure modes worth tracking as context.

## Writing guidance

Prefer clear Markdown files with specific titles. Include dates, relevant links, assumptions, and the current status when useful. Write for the next person or agent who will need this context cold.
