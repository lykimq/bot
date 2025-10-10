# :building_construction: Current Architecture

**Navigation:** [:house: Home](README.md) | [:arrow_left: Previous: Overview](00-overview.md) | [Next: Critical Issues :arrow_right:](02-critical-issues.md)

---

## Table of Contents
- [What the Bot Does](#what-the-bot-does)
- [How It's Built](#how-its-built)
- [Key Files](#key-files)

---

## What the Bot Does

The bot solves a critical limitation: GitLab's GitHub integration doesn't test pull requests (PRs) from forks. It provides:

1. **GitHub-GitLab Synchronization** - Pushes PRs to GitLab branches for CI testing
2. **Automatic Merge Commits** - Creates merge commits for testing (like Travis CI)
3. **Detailed CI Reporting** - Reports failures with direct links to failed jobs
4. **PR Lifecycle Management** - Handles stale PRs, milestones, branch cleanup
5. **Bug Minimization** - Integrates with `coq-bug-minimizer` tool
6. **Backport Management** - Complex workflow for managing backports (Rocq Prover only)

## How It's Built

**Trigger-Action Model** (following GraphQL terminology):
- **Subscriptions** (`bot-components/*_subscriptions.ml`) - Listen to GitHub/GitLab webhooks
- **Queries** (`bot-components/*_queries.ml`) - Gather information from APIs
- **Mutations** (`bot-components/*_mutations.ml`) - Perform state-changing actions

**Current Flow**: Webhook → `bot.ml` routing → `actions.ml` execution → API calls via `bot-components`

## Key Files

### Main Application (`src/`)
- `src/bot.ml` (664 lines) - Main webhook handler with hardcoded business logic
- `src/actions.ml` (3223 lines) - All action implementations in one monolithic file
- `src/config.ml` (171 lines) - TOML configuration parsing
- `src/git_utils.ml` (221 lines) - Git operations wrapper
- `src/github_installations.ml` (80 lines) - GitHub App installation token management
- `src/helpers.ml` (185 lines) - Mixed utility functions

### API Components (`bot-components/`)
- GitHub API wrappers (GraphQL queries, mutations, subscriptions)
- GitLab API wrappers (GraphQL queries, mutations, subscriptions)
- `Bot_info.ml` - Bot configuration and credentials
- `GitHub_app.ml` - GitHub App authentication (JWT, installation tokens)

---

**Navigation:** [:house: Home](README.md) | [:arrow_left: Previous: Overview](00-overview.md) | [Next: Critical Issues :arrow_right:](02-critical-issues.md)
