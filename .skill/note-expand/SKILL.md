---
name: note-expand
description: Expand an outline note into a full knowledge note
---

# Input

- Note path under `knowledge/`

# Workflow

1. Read `templates/note.md` as schema.
2. Read other files in `templates/` as reusable content patterns.
3. Read the target outline note.
4. Search `knowledge/` for 3-5 notes related to the target topic.
5. Observe:
   - spacing, indentation, and list patterns
   - markup, linking, and abbreviation usage
   - language and explanation styles
   - ignore the frontmatter
6. Expand the outline into full note content.

# Rules

- Follow the outline structure strictly.
- Cover referenced concepts comprehensively.
- Explain concepts instead of only listing terminology.
- Keep sections logically organized and stylistically symmetric.
- Prefer concrete examples, pseudocode, and diagrams when helpful.
- Directly link related existent notes within the relevant paragraphs only once.