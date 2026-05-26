---
name: note-outline
description: Generate outline for a knowledge note
---

# Input

- Topic name

# Workflow

1. Create a new note under `knowledge/`.
2. Read `templates/note.md` as schema.
3. Search `knowledge/` for 3-5 notes related to the target topic.
4. Observe:
   - note structure
   - concept coverage
   - section depth
   - ignore the frontmatter
5. Generate:
   - H1/H2 structure
   - bold subsection titles

# Rules

- Cover the topic comprehensively.
- Keep sections logically organized without overlap.
- Add deeper explanatory subsections for important mechanisms and principles.
- Do not link to other notes.