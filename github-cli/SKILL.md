---
name: github-cli
description: Rules for using the GitHub CLI to interact with GitHub repositories.
---

# GitHub CLI Skill

This skill defines how the coding agent should use the `gh` command‑line tool to interact with GitHub. The agent runs inside an existing git repository and the GitHub authentication token is available in the environment variable `GH_TOKEN`.

All instructions in this document are **mandatory** and must be followed.

## Prerequisites

* `gh` must be installed and available in the PATH.
* The environment variable `GH_TOKEN` must be set and contain a valid GitHub personal access token with appropriate scopes (repo, workflow, admin:org if needed).
* The agent is already inside a local git repository that is optionally linked to a remote GitHub repository.

## Authentication

Before any `gh` command, ensure the token is loaded:

```bash
export GH_TOKEN=$GH_TOKEN
# Authenticate gh using the token
gh auth login --with-token <<< "$GH_TOKEN"
```

The agent should run the above only once per session. Subsequent commands will use the stored authentication.

## Creating a New Repository

To create a new repository (optionally under an organization):

```bash
# Create a repository in the current user account
gh repo create <repo-name> --public --source=. --remote=origin

# Create a repository under an organization
gh repo create <org>/<repo-name> --public --source=. --remote=origin
```

Parameters:
* `<repo-name>` – name of the new repository.
* `<org>` – GitHub organization name (optional).
* Use `--private` instead of `--public` for private repos.
* `--source=.` tells gh to use the current directory as the repo source.
* `--remote=origin` creates the remote named `origin`.

After creation, push the initial commit if needed:

```bash
git push --set-upstream origin main
```

## Issue Management

### Create an Issue

```bash
# Create a new issue with title and optional body
ngh issue create --title "<title>" --body "<body>"
```

### List Issues

```bash
# List open issues
gh issue list --state open
```

### Update an Issue

```bash
# Edit an issue (by number) – can change title, body, or state
gh issue edit <issue-number> --title "<new title>" --body "<new body>" --state <open|closed>
```

### Close an Issue

```bash
# Close an issue
gh issue close <issue-number>
```

## Pull Request Management

### Create a Pull Request

```bash
# Create a PR from the current branch to the default branch
gh pr create --title "<title>" --body "<body>" --base <base-branch> --head <head-branch>
```

If the current branch is the head, omit `--head`.

### List Pull Requests

```bash
# List open PRs
gh pr list --state open
```

### Update a Pull Request

```bash
# Edit a PR (by number)
# pr edit <pr-number> --title "<new title>" --body "<new body>" --state <open|closed|merged>
```

### Merge a Pull Request

```bash
# Merge a PR using the default merge method
gh pr merge <pr-number>
```

You can specify `--merge`, `--squash`, or `--rebase` for different merge strategies.

### Close a Pull Request without merging

```bash
gh pr close <pr-number>
```

## Common Patterns for the Agent

* Always check the exit status of `gh` commands. If a command fails, capture the error output and retry or report the failure.
* Use `--json` flag to retrieve structured data when the agent needs to parse information, e.g.:

```bash
gh issue list --json number,title,author,state
```

* For automation, pipe the output to `jq` or parse it within the agent’s code.

## Security

* Never log the value of `GH_TOKEN`.
* Ensure that any repository creation respects organization policies and naming conventions.

## Example Workflow

1. Authenticate with `gh auth login` using `GH_TOKEN`.
2. Create a repository if it does not exist.
3. Create an issue for a new feature.
4. Open a branch, commit changes, push, and create a PR.
5. Once the PR is approved, merge it and close the related issue.

These steps should be performed using the commands described above.

---

**End of GitHub CLI Skill**
