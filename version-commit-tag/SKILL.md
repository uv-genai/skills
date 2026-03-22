---
description: update AGENTS.md, update README.md, if git repository not created, create it, commit and tag.
---

# /version-commit-tag

When the user types `/version-commit-tag` in the chat, the skill will:

1. if the git repository is not created, create it
2. if there is already a tag, bump up the version everywhere, if not use the current version
3. update AGENTS.md with latest informationa about:
  1. project description
  2. design decisions
  3. implementation details
  4. changelog
4. update README.md with latest information
5. commit the changes
6. tag the commit with the new version
