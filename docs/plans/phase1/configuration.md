# :gear: Configuration System

> Extend TOML configuration to eliminate hardcoded repository mappings

**Related Issues**: #160 (Disable GitLab sync by default), #201 (Deploy as opam package)

## :wrench: Current Problem

Repository configurations are hardcoded in `bot.ml`:
- **Pattern Matching**: Repository-specific logic in code
- **No Flexibility**: Adding repositories requires code changes
- **Feature Logic**: Scattered across multiple files
- **No Toggles**: Can't enable/disable features per repository

## :gear: Solution Components

### Extend TOML Configuration
**Purpose**: Move all repository mappings to configuration
**New Structure**:
```toml
[[repositories]]
github = "rocq-prover/rocq"
gitlab = "coq/coq"
gitlab_domain = "gitlab.inria.fr"
features = ["sync", "ci_reporting", "bug_minimizer", "stale_pr", "milestone_sync", "backport_manager"]

[features.sync]
auto_merge = true
delete_branch_on_close = true

[features.bug_minimizer]
default_timeout_minutes = 30
```

### Move Hardcoded Mappings
**Purpose**: Eliminate pattern matching in `bot.ml`
**Changes**:
- Replace hardcoded repository patterns
- Use configuration lookup functions
- Maintain backward compatibility

### Add Configuration Validation
**Purpose**: Prevent runtime crashes from invalid config
**Validation**:
- Required fields present
- No duplicate repository definitions
- Valid feature names
- GitLab domains exist in config

### Enable Feature Toggles
**Purpose**: Per-repository feature control
**Features**:
- Enable/disable features per repository
- Feature-specific configuration
- Runtime feature discovery
