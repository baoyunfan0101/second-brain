---
name: note-meta
description: Generate frontmatter for knowledge notes
---

# Input

- Note path under `knowledge/`

# Workflow

1. Read `templates/note.md` as schema.
2. Read the target note.
3. Search `knowledge/` for 3-5 similar notes.
4. Read only their frontmatter.
5. Infer metadata style from similar notes:
   - tag style (domain-oriented)
   - summary style (concept-dense)
   - use_cases style (practical)
6. Generate frontmatter using:
   - schema from template
   - style from similar notes
   - content from target note
7. Replace only the frontmatter.

# Rules

- Keep metadata concise and structured.
- Prefer phrase-based summaries.