# Bot Refactoring Milestone

**Goal**: Transform the bot into a modular, testable, and maintainable system while preserving all existing functionality.

**Total Issues**: 11 issues across 3 phases

---

# Phase 0: Immediate Correctness and Webhook Robustness

## Quick Fixes
- [ ] **[coq/bot#250](https://github.com/coq/bot/issues/250)**: "bench native" should be "bench native=on"
  - **Problem**: Incorrect benchmark command format displayed in CI comments, leading to user copy/paste errors and failed local runs
  - **Solution**: Fix command text formatting in benchmark reporting functions
  - **Impact**: Corrects user-facing command display
  - **Effort**: Simple string replacement in `run_bench` or `bench_text` functions

- [ ] **[coq/bot#125](https://github.com/coq/bot/issues/125)**: JSON parsing failure on PR close webhook
  - **Problem**: Bot crashes on malformed webhook payloads with null values (e.g., missing fields on PR close), due to unguarded pattern matches and assumptions about presence
  - **Solution**: Add robust JSON parsing with null checks and error handling
  - **Impact**: Ensures reliable webhook handling
  - **Effort**: Add null checks to JSON parsing in `receive_gitlab` and `receive_github` functions

# Phase 1: Foundation Infrastructure

**Goal**: Implement easy codebase improvements that enhance error handling, logging, and type safety

## Error Handling and Logging System

### Checklist
Covered issues: [coq/bot#223](https://github.com/coq/bot/issues/223), [coq/bot#264](https://github.com/coq/bot/issues/264), [coq/bot#227](https://github.com/coq/bot/issues/227), [coq/bot#323](https://github.com/coq/bot/issues/323), [coq/bot#280](https://github.com/coq/bot/issues/280), [coq/bot#275](https://github.com/coq/bot/issues/275)

**Implementation**
- [ ] Type modernization - `result` to `Result.t`
  - Addresses: [coq/bot#223](https://github.com/coq/bot/issues/223) (Problem: deprecated `result` type in interfaces)
  - How it resolves: Replace `result` with `Result.t` in MLI files and adjust call sites as needed to align with modern type usage and tooling expectations.

- [ ] Proposed: Create `src/errors.ml` - structured error types and context
  - Addresses: [coq/bot#264](https://github.com/coq/bot/issues/264) (Problem: uncaught exceptions lack context/stack traces)
  - How it resolves: Defines `APIError`, `GitError`, `ConfigError`, `WebhookError`, `RateLimitError` with fields for operation, inputs, and optional backtraces. Call sites wrap failures using these types so errors carry actionable context.
    - Note: `RateLimitError` type is introduced here; it will be emitted/handled by `src/rate_limiter.ml` in Phase 2.

- [ ] Proposed: Create `src/logger.ml` — structured logging and secret masking
  - Addresses: [coq/bot#227](https://github.com/coq/bot/issues/227) (Problem: logs lack context), [coq/bot#323](https://github.com/coq/bot/issues/323) (Problem: secrets leak in logs)
  - How it resolves: Provides a logger with levels and key-value fields (e.g., repo/url, command, pipeline/job IDs), plus centralized regex-based redaction for tokens/URLs. Replace direct prints to ensure consistent context and masking.

- [ ] Add verbosity reduction hooks — safe truncation and summarization
  - Addresses: [coq/bot#280](https://github.com/coq/bot/issues/280) (Problem: git output overflows log sinks)
  - How it resolves: Caps `stdout`/`stderr` size with head/tail retention and line filtering in `git_utils.execute_cmd` and `git_utils.report_status`, and emits concise summaries in `actions.trace_action` instead of full traces.

- [ ] Log GraphQL warnings — proactive API visibility
  - Addresses: [coq/bot#275](https://github.com/coq/bot/issues/275) (Problem: GraphQL warnings not surfaced)
  - How it resolves: Parses `extensions.warnings` in `bot-components/GraphQL_query.send_graphql_query` and emits WARN-level entries via `logger` with request identifiers to highlight upstream deprecations/changes.

# Phase 2: Infrastructure and Modularization

**Goal**: Build robust infrastructure systems and break down monolithic codebase into maintainable, testable modules

## Infrastructure Systems
- [ ] **Proposed: Rate Limiting and API Management**
  - **Create `src/rate_limiter.ml`**: Centralized GitHub API quota tracking
  - **Problem**: Bot makes API calls without checking rate limits, risking API exhaustion (5000 requests/hour limit)
  - **Solution**: Implement `X-RateLimit-Remaining` and `X-RateLimit-Reset` header monitoring
  - **Features**: Exponential backoff (1s initial, 2x multiplier, 60s max, +/-25% jitter), proactive waiting (<100 requests)
  - **Impact**: Prevents service interruptions and API exhaustion

- [ ] **Proposed: Configuration System**
  - **Extend TOML Configuration**: Move hardcoded repository mappings to configuration
  - **Problem**: Repository configurations are hardcoded in `bot.ml` with pattern matching, no flexibility for adding repositories
  - **Solution**: Move all repository mappings to TOML configuration with multiple repositories, feature arrays, and feature-specific settings
  - **Impact**: Eliminates hardcoded patterns and enables configuration-driven repository management
  - **Config file**: Use the existing root-level `coqbot-config.toml`; keep `example-config.toml` as the reference template

- [ ] **Proposed: Testing Infrastructure**
  - **Setup Alcotest Framework**: Lightweight OCaml testing framework with minimal setup
  - **Create Test Directory Structure**: Organized test files and fixtures
  - **Write Unit Tests**: Test core utilities and functions
  - **Add CI Integration**: Automated testing in continuous integration

- [ ] **Proposed: Package Management and Distribution**
  - **[coq/bot#201](https://github.com/coq/bot/issues/201)**: Deploy as opam package
  - **Problem**: Consider deploying bot-components as opam package for better distribution
  - **Solution**: Create opam package structure and publishing workflow
  - **Impact**: Improves distribution and dependency management

## Modularization

Objective: Split monolithic feature logic into focused modules to improve testability, change safety, and reviewer clarity - without changing behavior.

Scope:
- In-scope: Extract feature modules from `src/actions.ml`, move cross-cutting helpers from `src/helpers.ml` to `src/utils/`, keep `git_utils` and `github_installations` unchanged, keep business logic identical.

Old -> New (summary)
- `src/actions.ml` -> `src/features/*`:
  - CI reporting: `ci_reporting.ml` (ties to target parsing/display improvements: [coq/bot#335](https://github.com/coq/bot/issues/335))
  - Doc URLs: `doc_urls.ml`
  - Benchmarks: `benchmarks.ml`
  - CI minimizer: `ci_minimizer.ml` (text/target clarity and duplication messaging relate to [coq/bot#195](https://github.com/coq/bot/issues/195), [coq/bot#194](https://github.com/coq/bot/issues/194), [coq/bot#193](https://github.com/coq/bot/issues/193))
  - Bug minimizer: `bug_minimizer.ml`
  - Pipeline handler: `pipeline_handler.ml`
  - Milestone sync: `milestone_sync.ml`
  - Backport manager: `backport_manager.ml`
  - Stale PR: `stale_pr.ml` (trivial rebase detection: [coq/bot#327](https://github.com/coq/bot/issues/327))
  - Command parser: `command_parser.ml`
- `src/helpers.ml` -> `src/utils/*`:
  - Strings: `string_utils.ml` (e.g., `strip_quoted_bot_name` improvements relate to [coq/bot#194](https://github.com/coq/bot/issues/194))
  - Branch/repo: `branch_utils.ml`
  - Comments/formatting: `comment_utils.ml` (ties to message clarity issues [coq/bot#195](https://github.com/coq/bot/issues/195))
  - HTTP/file download: `http_utils.ml`
- Core simplification (later step): `src/bot.ml` reduced to webhook routing.

Acceptance Criteria (Definition of Done)
- `src/actions.ml` emptied/removed; all features live under `src/features/` with `.mli` interfaces.
- `src/helpers.ml` split into `src/utils/` modules with `.mli` interfaces.
- `src/bot.ml` contains only routing (target <200 lines).
- All builds and CI pass.

### Core Infrastructure (Proposed)
Create dynamic feature dispatch and simplify core bot logic.

- [ ] **Proposed: Feature Registry (`src/feature_registry.ml`)**
  - **Features**: Dynamic feature dispatch based on configuration, feature registration by name, webhook event routing, enable/disable features per repository
  - **Impact**: Configuration-driven feature routing

- [ ] **Proposed: Simplify Bot Logic (`src/bot.ml`)**
  - **Goal**: Reduce from 664 lines to <200 lines
  - **Changes**: Remove hardcoded repository patterns, remove feature-specific logic, focus only on webhook parsing and routing
  - **Impact**: Pure webhook routing without business logic

- [ ] **Text Formatting and Display Improvements**
  - [ ] **[coq/bot#195](https://github.com/coq/bot/issues/195)**: Fix mis-pluralized job reporting text
    - **Problem**: "requested target '%s' could not be found among the jobs %s" is mis-pluralized and unclear about job types
    - **Solution**: Fix grammar in `minimize_failed_tests` function, clarify what types of jobs are being reported
    - **Impact**: Improves clarity of CI job reporting messages
    - **Effort**: Simple text replacement in error message formatting

  - [ ] **[coq/bot#194](https://github.com/coq/bot/issues/194)**: Fix hidden and misleading target display
    - **Problem**: Hidden `</code>` tag and misleading target display in CI reports
    - **Solution**: Fix HTML tag rendering in `minimize_failed_tests` function and `strip_quoted_bot_name` function
    - **Impact**: Eliminates confusing HTML artifacts and misleading target names
    - **Effort**: Fix HTML tag escaping in string formatting

  - [ ] **[coq/bot#193](https://github.com/coq/bot/issues/193)**: Add indication for doubled targets
    - **Problem**: No indication when a target was doubled in the request, even though it only triggers once
    - **Solution**: Add clear messaging in `minimize_failed_tests` function target processing logic
    - **Impact**: Provides transparency about target duplication behavior
    - **Effort**: Add duplicate detection and messaging in target request processing

---

## Summary
**Result**: Testable, maintainable, and extensible system with 11 issues resolved across 3 phases.
- **Phase 0**: 2 issues (quick fixes requiring minimal changes)
- **Phase 1**: 6 issues (easy codebase improvements for type safety, logging, and error handling)
- **Phase 2**: 3+ issues (moderate infrastructure systems and modularization)

**Key Achievements**:
- **Security**: Eliminated credential exposure and race conditions
- **Reliability**: Robust error handling and webhook processing
- **Infrastructure**: Rate limiting, logging, configuration, and testing systems
- **Modularity**: Clean separation of concerns with dynamic feature dispatch
- **Maintainability**: Focused modules enabling independent testing and development