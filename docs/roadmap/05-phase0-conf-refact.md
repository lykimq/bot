# :gear: Phase 1.2: Configuration Refactoring

**Navigation:** [:house: Home](README.md) | [:arrow_left: Previous: Critical Fixes](04-phase0-critical-fixes.md) | [Next: Testing Infrastructure :arrow_right:](06-phase0-testing-infra.md)

---

## Table of Contents
- [Move Repository Mappings to TOML](#move-repository-mappings-to-toml)
- [Configuration Validation](#configuration-validation)

---

## :clipboard: Move Repository Mappings to TOML

### Problem

- Repository configurations hardcoded in pattern matches in `bot.ml`
- Adding new repository requires code changes and redeployment
- Can't enable/disable features per repository dynamically
- Feature logic scattered across multiple files

**Current State**: The bot already has TOML config for GitHub-GitLab mappings (`example-config.toml`), but feature-specific logic is hardcoded in `bot.ml`.

**Related Issues**: [#157](docs/issues/roadmap/templates/unlabeled/issue-157-parse-webhooks-for-github-app-installations.md) (Parse Webhooks for GitHub App Installations)

### Solution

Extend existing TOML configuration with feature definitions per repository:

**Current** (`example-config.toml`):
```toml
[mappings]
  [mappings.coq]
  github="coq/coq"
  gitlab="coq/coq"
```

**Proposed Extension**:
```toml
[[repositories]]
github = "rocq-prover/rocq"
gitlab = "coq/coq"
gitlab_domain = "gitlab.inria.fr"
features = ["sync", "ci_reporting", "bug_minimizer", "stale_pr", "milestone_sync", "backport_manager"]

[[repositories]]
github = "rocq-community/docker-coq"
gitlab = "rocq-community/docker-coq"
gitlab_domain = "gitlab.com"
features = ["sync", "ci_reporting"]

[[repositories]]
github = "math-comp/math-comp"
gitlab = "math-comp/math-comp"
gitlab_domain = "gitlab.inria.fr"
features = ["sync", "ci_reporting"]

# Feature-specific configurations
[features.sync]
auto_merge = true
delete_branch_on_close = true

[features.bug_minimizer]
default_timeout_minutes = 30

[features.stale_pr]
warn_after_days = 30
close_after_days = 30
```

### Implementation Steps

1. Extend `src/config.ml` to parse repository definitions
2. Create repository lookup functions
3. Replace hardcoded pattern matches with config lookups
4. Keep backward compatibility with existing `[mappings]` section

### Success Criteria

- :white_check_mark: Zero hardcoded repository names in `bot.ml` pattern matches
- :white_check_mark: All repository configurations defined in TOML
- :white_check_mark: New repositories addable by editing TOML (no code changes)
- :white_check_mark: Backward compatible with existing TOML config

---

## :white_check_mark: Configuration Validation

### Problem & Impact

The current bot's configuration system, primarily managed through `src/config.ml` and consumed by `src/bot.ml` and `src/actions.ml`, suffers from several critical deficiencies that lead to instability, poor debuggability, and limited flexibility:

1.  **Invalid TOML crashes bot at runtime:** The parsing functions (`toml_of_file`, `toml_of_string`) in `src/config.ml` use `Toml.Parser.unsafe`. This means any syntax error or malformed content in the TOML configuration file will cause an unhandled exception and immediately terminate the bot process upon startup. This lack of graceful error handling makes the bot fragile to configuration mistakes, leading to unexpected downtime and difficult-to-diagnose issues in production environments.


3.  **Duplicate repository entries aren't robustly detected:** While the system does detect duplicate keys in `[mappings]` and `[gitlab]` sections (e.g., two entries with the same mapping name or GitLab domain) via `Hashtbl.of_alist` and `Hashtbl.of_alist_exn`, this detection results in an abrupt program crash (`Failure "Duplicate key in config."`). This is not a user-friendly approach to configuration validation; a robust system should provide clear, actionable error messages indicating the duplicate entries rather than terminating the application.

**Related Issues**: [#157](docs/issues/roadmap/templates/unlabeled/issue-157-parse-webhooks-for-github-app-installations.md) (Parse Webhooks for GitHub App Installations)


### Solution

Add comprehensive validation in `src/config.ml`. The proposed `validate_config` function, along with its specific validation checks, directly addresses the identified problems by:

*   **Preventing runtime crashes from invalid TOML:** The `validate_config` function will integrate robust TOML parsing (e.g., using `Result.t` instead of `unsafe`) and perform early syntax checks. This ensures the bot rejects malformed configurations at startup with clear error messages, enhancing stability.
*   **Ensuring all required fields are present:** The `check_required_fields` within `validate_config` will explicitly verify the presence of all necessary configuration parameters (e.g., `github`, `gitlab`, `gitlab_domain`). This ensures that the configuration is complete before the bot attempts to use it.
*   **Robustly detecting duplicate repository entries:** The `check_duplicate_repos` function, called by `validate_config`, will implement logic to identify and report duplicate entries (e.g., in `[[repositories]]` or `[mappings]`) with clear messages, allowing for graceful correction instead of abrupt crashes.
*   **Validating the new feature configuration structure:** The `check_valid_features` and `check_gitlab_instances` functions within `validate_config` will parse the new TOML structure (with `features = [...]` arrays and `[features.*]` tables). They will validate that all listed features are from a known set, that their required sub-configurations are present, and that referenced GitLab domains exist. This ensures the correctness and integrity of the newly configurable feature system.

```ocaml
(* Validate configuration on load *)
let validate_config toml_data =
  (* Check required fields *)
  check_required_fields toml_data;

  (* Detect duplicate repositories *)
  check_duplicate_repos repositories;

  (* Validate feature names *)
  check_valid_features repositories;

  (* Validate GitLab domains are configured *)
  check_gitlab_instances toml_data repositories;
```

**Validation checks**: These are the specific checks that the `validate_config` function will orchestrate:
- Required fields present (github, gitlab, gitlab_domain)
- No duplicate repository definitions
- Feature names are valid (from known feature list)
- GitLab domains referenced in `[[repositories]]` exist in `[gitlab]` section
- Required feature configs present (e.g., bug_minimizer needs timeout)

### Success Criteria

- ✅ Invalid TOML rejected with clear, actionable error messages
- ✅ Bot fails fast on startup if config is wrong (not at runtime)
- ✅ Duplicate repositories detected and reported
- ✅ Unknown feature names reported with suggestions

---

**Navigation:** [:house: Home](README.md) | [:arrow_left: Previous: Critical Fixes](04-phase0-critical-fixes.md) | [Next: Testing Infrastructure :arrow_right:](06-phase0-testing-infra.md)
