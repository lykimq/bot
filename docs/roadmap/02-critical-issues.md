# :warning: Critical Issues

**Navigation:** [:house: Home](README.md) | [:arrow_left: Previous: Current Architecture](01-current-architecture.md) | [Next: Phase 0 Overview :arrow_right:](03-phase0-overview.md)

---

## Table of Contents
- [1. Monolithic Architecture](#1-monolithic-architecture)
- [2. Hardcoded Repository Mappings](#2-hardcoded-repository-mappings)
- [3. No Rate Limiting](#3-no-rate-limiting)
- [4. Unstructured Error Handling](#4-unstructured-error-handling)
- [5. Minimal Testing](#5-minimal-testing)

---

## 1. :office: Monolithic Architecture

**Problem**: `bot.ml` and `actions.ml` contain all webhook routing and business logic mixed together. Adding new repositories or features requires modifying these core files.

**Impact**:
- Can't add new repositories without code changes
- Difficult to test individual features (everything is in 2 giant files)
- High risk of breaking existing functionality when making changes
- ~3900 lines of code need to be understood to add a simple feature

**What Needs to Be Refactored**:
- `actions.ml` → Break into +10 feature modules
- `bot.ml` → Simplify to routing logic only
- `helpers.ml` → Split into focused utility modules

## 2. :wrench: Hardcoded Repository Mappings

**Problem**: Repository configurations are embedded in pattern matching throughout `bot.ml`:

```ocaml
match (owner, repo) with
| "rocq-community", ("docker-base" | "docker-coq" | "docker-rocq") -> ...
| "math-comp", ("docker-mathcomp" | "math-comp") -> ...
```

**Current Reality**: While `src/config.ml` uses TOML for basic GitHub-GitLab repository mappings, the *feature-specific logic* that dictates how the bot behaves for certain repositories (e.g., triggering specific actions for "rocq-community" or "math-comp" repositories) is still hardcoded within `bot.ml` using pattern matching.

**Impact**:
- Adding new repositories or modifying feature behavior for existing ones often requires direct code changes in `bot.ml` and redeployment.
- There is no flexible way to enable/disable features or customize their behavior per repository without altering the core logic.
- Feature logic remains scattered across multiple pattern matches in `bot.ml`, making it difficult to manage and extend.
- It's challenging to test different feature versions or deploy them gradually to specific repositories.

## 3. :traffic_light: No Rate Limiting

**Problem**: GitHub API has 5000 requests/hour limit (per installation). Bot has no intelligent backoff strategy or rate limit tracking.

**Impact**:
- Risk of API exhaustion during busy periods (many PRs updated simultaneously)
- Failed requests aren't retried properly
- No visibility into API quota usage
- Bot may become unresponsive when rate-limited

## 4. :x: Unstructured Error Handling

**Problem**: Errors are often handled with generic strings or `failwith`. No distinction between recoverable and fatal errors.

**Impact**:
- Difficult to debug production issues (no structured context)
- Silent failures may go unnoticed
- No automated recovery mechanisms
- Can't distinguish between temporary API failures and permanent errors

## 5. :test_tube: Minimal Testing

**Problem**: Currently no automated test suite. Testing requires running the full bot with Docker and ngrok.

**Impact**:
- Refactoring is risky (no safety net)
- Edge cases aren't systematically verified
- Hard to catch regressions
- Contributors need complex local setup to test changes

---

**Navigation:** [:house: Home](README.md) | [:arrow_left: Previous: Current Architecture](01-current-architecture.md) | [Next: Phase 0 Overview :arrow_right:](03-phase0-overview.md)
