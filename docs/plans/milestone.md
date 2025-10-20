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

Covered issues: [coq/bot#223](https://github.com/coq/bot/issues/223), [coq/bot#264](https://github.com/coq/bot/issues/264), [coq/bot#227](https://github.com/coq/bot/issues/227), [coq/bot#323](https://github.com/coq/bot/issues/323), [coq/bot#280](https://github.com/coq/bot/issues/280), [coq/bot#275](https://github.com/coq/bot/issues/275)

**Implementation**
- Type modernization - `result` to `Result.t`
  - Addresses: [coq/bot#223](https://github.com/coq/bot/issues/223) (Problem: deprecated `result` type in interfaces)
  - How it resolves: Replace `result` with `Result.t` in MLI files and adjust call sites as needed to align with modern type usage and tooling expectations.

- Proposed: Create `src/errors.ml` - structured error types and context
  - Addresses: [coq/bot#264](https://github.com/coq/bot/issues/264) (Problem: uncaught exceptions lack context/stack traces)
  - How it resolves: Defines `APIError`, `GitError`, `ConfigError`, `WebhookError`, `RateLimitError` with fields for operation, inputs, and optional backtraces. Call sites wrap failures using these types so errors carry actionable context.

- Proposed: Create `src/logger.ml` — structured logging and secret masking
  - Addresses: [coq/bot#227](https://github.com/coq/bot/issues/227) (Problem: logs lack context), [coq/bot#323](https://github.com/coq/bot/issues/323) (Problem: secrets leak in logs)
  - How it resolves: Provides a logger with levels and key-value fields (e.g., repo/url, command, pipeline/job IDs), plus centralized regex-based redaction for tokens/URLs. Replace direct prints to ensure consistent context and masking.
  - **DoD**: All `Stdio.printf`/`Lwt_io.print*` and ad-hoc prints are routed through `logger` with redaction by default

- Add verbosity reduction hooks — safe truncation and summarization
  - Addresses: [coq/bot#280](https://github.com/coq/bot/issues/280) (Problem: git output overflows log sinks)
  - How it resolves: Caps `stdout`/`stderr` size with head/tail retention and line filtering in `git_utils.execute_cmd` and `git_utils.report_status`, and emits concise summaries in `actions.trace_action` instead of full traces.
  - **DoD**: All command outputs use capped/truncated reporting; CI comments show concise summaries by default

- Log GraphQL warnings — proactive API visibility
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

### Feature Modules (Proposed)
Break down monolithic `actions.ml` (3223 lines) into independent feature modules.

- [ ] **Proposed: CI Reporting (`src/features/ci_reporting.ml`)**
  - **Functions**: `send_status_check`, `job_failure`, `trace_action`
  - **Includes**: Improved target parsing and display formatting ([coq/bot#335](https://github.com/coq/bot/issues/335))
  - **Impact**: Clean separation of CI reporting logic

- [ ] **Proposed: Documentation URLs (`src/features/doc_urls.ml`)**
  - **Functions**: `send_doc_url_job`, `send_doc_url`
  - **Impact**: Isolated documentation artifact handling

- [ ] **Proposed: Benchmark Tracking (`src/features/benchmarks.ml`)**
  - **Functions**: `fetch_bench_results`, `bench_comment`, `run_bench`
  - **Impact**: Rocq-specific benchmark functionality

- [ ] **Proposed: CI Minimization (`src/features/ci_minimizer.ml`)**
  - **Functions**: `run_ci_minimization`, `ci_minimization_suggest`, `minimize_failed_tests`
  - **Impact**: Automated CI test minimization

- [ ] **Proposed: Bug Minimization (`src/features/bug_minimizer.ml`)**
  - **Functions**: `run_coq_minimizer`, `coq_bug_minimizer_results_action`
  - **Impact**: Automated bug report minimization

- [ ] **Proposed: Pipeline Handler (`src/features/pipeline_handler.ml`)**
  - **Functions**: `pipeline_action`, `create_pipeline_summary`
  - **Impact**: GitLab pipeline event management

- [ ] **Proposed: Milestone Sync (`src/features/milestone_sync.ml`)**
  - **Functions**: `adjust_milestone`
  - **Impact**: Milestone synchronization between issues and PRs

- [ ] **Proposed: Backport Manager (`src/features/backport_manager.ml`)**
  - **Functions**: `project_action`, `add_to_column`, `add_remove_labels`
  - **Impact**: Rocq Prover backport workflow management

- [ ] **Proposed: Stale PR Management (`src/features/stale_pr.ml`)**
  - **Functions**: `rocq_check_stale_pr`, `rocq_check_needs_rebase_pr`, `apply_after_label`
  - **Includes**: Clean implementation of trivial rebase detection ([coq/bot#327](https://github.com/coq/bot/issues/327))
  - **Impact**: Automated stale PR management

- [ ] **Proposed: Command Parser (`src/features/command_parser.ml`)**
  - **Functions**: Command parsing logic from `bot.ml`
  - **Impact**: Isolated command processing

### Utility Modules (Proposed)
Split `helpers.ml` (185 lines) into focused utility modules.

- [ ] **Proposed: String Utils (`src/utils/string_utils.ml`)**
  - **Functions**: `string_match`, `fold_string_matches`, `trim_comments`, `strip_quoted_bot_name`, `first_line_of_string`, `remove_between`
  - **Impact**: Clean string manipulation utilities

- [ ] **Proposed: Branch Utils (`src/utils/branch_utils.ml`)**
  - **Functions**: `pr_from_branch`, `github_repo_of_gitlab_url`, `parse_gitlab_repo_url`
  - **Impact**: Git branch and repository utilities

- [ ] **Proposed: Comment Utils (`src/utils/comment_utils.ml`)**
  - **Functions**: `code_wrap`, comment parsing utilities, comment validation functions
  - **Impact**: Comment processing and formatting

- [ ] **Proposed: HTTP Utils (`src/utils/http_utils.ml`)**
  - **Functions**: `download`, `download_cps`, `download_to`, `copy_stream`, HTTP request/response utilities
  - **Impact**: HTTP operations and file downloads

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
  - **[coq/bot#195](https://github.com/coq/bot/issues/195)**: Fix mis-pluralized job reporting text
    - **Problem**: "requested target '%s' could not be found among the jobs %s" is mis-pluralized and unclear about job types
    - **Solution**: Fix grammar in `minimize_failed_tests` function, clarify what types of jobs are being reported
    - **Impact**: Improves clarity of CI job reporting messages
    - **Effort**: Simple text replacement in error message formatting

  - **[coq/bot#194](https://github.com/coq/bot/issues/194)**: Fix hidden and misleading target display
    - **Problem**: Hidden `</code>` tag and misleading target display in CI reports
    - **Solution**: Fix HTML tag rendering in `minimize_failed_tests` function and `strip_quoted_bot_name` function
    - **Impact**: Eliminates confusing HTML artifacts and misleading target names
    - **Effort**: Fix HTML tag escaping in string formatting

  - **[coq/bot#193](https://github.com/coq/bot/issues/193)**: Add indication for doubled targets
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