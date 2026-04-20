---
description: when typing `vct` or `version-commit-tag`: update AGENTS.md, update README.md, if git repository not created, create it, commit and tag.
---

# /version-commit-tag

When the user types `version-commit-tag` or `vct` in the chat, the skill will:

1. if the git repository is not created, create it
2. if not present, create a proper .gitignore file
3. if there is already a tag, bump up the version everywhere, if not use the current version,
   it is up to you to decide which version number to increase (major, minor, patch) depending on how big the change is,
4. update AGENTS.md with latest informationa about:
  1. project description
  2. design decisions
  3. implementation details
  4. changelog
5. update README.md with latest information
6. commit the changes
7. tag the commit with the new version number

## Mandatory rules

- If a git repository is already present inside the current top level directory do not create a new one.
- AGENTS.md file must be located in the root of the repository.
- README.md file must be located in the top level project directory containing all the source code directories and files.
