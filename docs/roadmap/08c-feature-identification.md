# :jigsaw: Feature Identification

**Navigation:** [:house: Home](README.md) | [:arrow_left: Previous: Architecture Diagrams](08b-architecture-diagrams.md) | [:arrow_up: Code Structure Analysis](08-phase1-code-structure-analysis.md) | [Next: Conclusion :arrow_right:](09-conclusion.md)


---

## Table of Contents
- [Supporting Types & Modules Explained](#supporting-types--modules-explained)
- [Feature Catalog](#feature-catalog)

---

## Supporting Types & Modules Explained

The features below reference various supporting types and modules.

**CI Reporting Types**:
- `rocq_job_info` - Stores CI job configuration extracted from traces (docker image, dependencies, targets, compiler, opam variant)
- `build_failure` - Categorizes build failures as `Warn`, `Retry`, or `Ignore` based on trace analysis

**Benchmark Tracking Module**:
- `BenchResults` - Module containing benchmark data structure with summary table, failures, slow/fast tests tables and counts

**CI Minimization Types** (complex feature with many supporting types):
- `ci_minimization_info` - Contains all parameters needed to run minimization (target, docker image, opam switch, failing/passing artifact URLs)
- `artifact_info` - Parsed GitHub artifact information (owner, repo, artifact ID) from artifact URLs
- `artifact_error` - Error categories when downloading artifacts (empty, multiple files, download failure)
- `run_ci_minimization_error` - Top-level errors during CI minimization (artifact errors, download errors)
- `ci_minimization_job_suggestion_info` - Analysis of whether a job is suitable for minimization (job status, error presence, file type)
- `ci_minimization_pr_info` - PR context for minimization (base/head commits, pipeline status, failed jobs list)
- `ci_minimization_request` - Request type (Auto, RequestSuggested, RequestAll, RequestExplicit)
- `ci_minimization_suggestion_kind` - Whether to suggest minimization (Suggested, Possible, Bad with reasons)
- `ci_pr_minimization_suggestion` - Whether to suggest minimization for entire PR (Suggest, RunAutomatically, Silent)

**Bug Minimization Types**:
- `coqbot_minimize_script_data` - Represents bug script input either as inline code (`MinimizeScript`) or attachment URL (`MinimizeAttachment`)

---

## Feature Catalog

Based on actual function analysis, here are the distinct features with their source locations:

### 1. :bar_chart: CI Reporting
**Purpose**: Report CI results from GitLab to GitHub with detailed failure info

**Source**: `src/actions.ml`

**Key functions**:
- `send_status_check` - Send detailed status checks to GitHub
- `job_failure` - Handle failed job reporting with trace extraction
- `job_success_or_pending` - Handle successful/pending jobs
- `job_action` - Main job event dispatcher
- `trace_action` - Extract and format trace information
- Supporting types: `rocq_job_info`, `build_failure`

---

### 2. :memo: Documentation URL Posting
**Purpose**: Post links to generated documentation artifacts

**Source**: `src/actions.ml`

**Key functions**:
- `send_doc_url_job` - Extract and post doc URLs from CI artifacts
- `send_doc_url` - Entry point for doc URL feature
- `send_doc_url_aux` - Helper for URL posting logic

---

### 3. :chart_with_upwards_trend: Benchmark Tracking
**Purpose**: Track and report benchmark results (Rocq-specific)

**Source**: `src/actions.ml`

**Key functions**:
- `fetch_bench_results` - Download benchmark data
- `bench_comment` - Post benchmark comparison comments
- `update_bench_status` - Update benchmark status checks
- `bench_text` - Format benchmark text output
- `run_bench` - Execute benchmark runs
- Supporting module: `BenchResults`

---

### 4. :mag: CI Minimization
**Purpose**: Minimize failing CI tests automatically

**Source**: `src/actions.ml`

**Key functions**:
- `run_ci_minimization` - Run minimization on CI failures
- `ci_minimization_suggest` - Suggest minimization opportunities
- `fetch_ci_minimization_info` - Gather info needed for minimization
- `ci_minimize` - Main CI minimization dispatcher
- `minimize_failed_tests` - Execute test minimization
- `ci_minimization_extract_job_specific_info` - Extract job details
- `shorten_ci_check_name` - Format CI check names
- `suggest_ci_minimization_for_pr` - Suggest minimization for PRs
- `format_options_for_getopts` - Parse minimization options
- `getopts`, `getopt` - Option parsing utilities
- `accumulate_extra_minimizer_arguments` - Build minimizer arguments
- `run_ci_minimization_error_to_string` - Error formatting
- `parse_github_artifact_url` - Parse artifact URLs
- `coq_bug_minimizer_resume_ci_minimization_action` - Resume minimization
- Supporting types: `ci_minimization_info`, `artifact_info`, `artifact_error`, `run_ci_minimization_error`, `ci_minimization_job_suggestion_info`, `ci_minimization_pr_info`, `ci_minimization_request`, `ci_minimization_suggestion_kind`, `ci_pr_minimization_suggestion`

---

### 5. :bug: Bug Minimization
**Purpose**: Minimize bug reports using `coq-bug-minimizer`

**Source**: `src/actions.ml`

**Key functions**:
- `run_coq_minimizer` - Execute bug minimizer script
- `coq_bug_minimizer_results_action` - Handle minimization results
- Supporting type: `coqbot_minimize_script_data`

---

### 6. :repeat: GitHub-GitLab Sync
**Purpose**: Core sync between GitHub PRs and GitLab CI, including branch cleanup

**Source**: `src/actions.ml` + `src/bot.ml`

**Key functions**:
- `pipeline_action` - Handle GitLab pipeline events (`actions.ml`)
- `mirror_action` - Mirror GitHub branches to GitLab (`actions.ml`)
- `pull_request_updated_action` - Handle PR updates (`actions.ml`)
- `pull_request_closed_action` - Handle PR closures and GitLab branch cleanup (`actions.ml`)
- `update_pr` - Update PR information (`actions.ml`)
- `run_ci_action` - Trigger CI runs (`actions.ml`)
- `rocq_push_action` - Handle Rocq-specific push events (`actions.ml`)
- `create_pipeline_summary` - Create pipeline summaries (`actions.ml`)
- `merge_pull_request_action` - Merge PRs via comments (`actions.ml`)
- `inform_user_not_in_contributors` - Notify non-contributors (`actions.ml`)
- Push/PR sync logic in `bot.ml` webhook handlers:
  - **Push Event Handlers**:
    - `rocq_push_action` - Analyzes merge/backporting info for Rocq Prover
    - `mirror_action` - Mirrors GitHub repos to GitLab (multiple mappings)
  - **Pull Request Handlers**:
    - `pull_request_closed_action` - Removes GitLab branches on PR closure
    - `pull_request_updated_action` - Manages PR sync between GitHub/GitLab
  - **Repository-Specific Logic**:
    - `rocq-prover/rocq`: Special push analysis + mirroring to GitLab.inria.fr
    - `rocq-community`: Docker repos → GitLab.com
    - `math-comp`: Math-comp repos → GitLab.inria.fr

---

### 7. :label: Milestone Sync
**Purpose**: Sync milestones between closed issues and PRs

**Source**: `src/actions.ml`

**Key functions**:
- `adjust_milestone` - Adjust milestone when issues are closed

---

### 8. :back: Backport Management
**Purpose**: Manage backport workflow (Rocq Prover only)

**Source**: `src/actions.ml`

**Key functions**:
- `project_action` - Handle project card movements
- `add_to_column` - Add items to project columns
- `add_remove_labels` - Manage labels
- `add_labels_if_absent` - Add labels conditionally
- `remove_labels_if_present` - Remove labels conditionally

---

### 9. :alarm_clock: Stale PR Management
**Purpose**: Mark and close stale PRs

**Source**: `src/actions.ml`

**Key functions**:
- `rocq_check_stale_pr` - Check for stale PRs
- `rocq_check_needs_rebase_pr` - Check PRs needing rebase
- `apply_after_label` - Apply labels after time delay
- `apply_throttle` - Apply throttling to actions
- `days_elapsed` - Calculate time elapsed

---

### 10. :gear: Utility Functions
**Purpose**: Common utilities used across features

**Source**: `src/helpers.ml`

**Key functions**:
- `string_match`, `fold_string_matches`, `map_string_matches`, `iter_string_matches` - String matching utilities
- `pr_from_branch` - Extract PR number from branch name
- `trim_comments` - Remove HTML comments
- `strip_quoted_bot_name` - Handle quoted bot mentions
- `github_repo_of_gitlab_url` - Map GitLab URLs to GitHub repos
- `parse_gitlab_repo_url` - Parse GitLab repository URLs
- `download`, `download_cps`, `download_to` - File download utilities
- `copy_stream` - Stream copying utility
- `code_wrap` - Code formatting
- `first_line_of_string` - Extract first line
- `remove_between` - String manipulation

---

### 11. :computer: Command Parsing
**Purpose**: Parse bot commands from comments and issues

**Source**: `src/bot.ml`

**Key functions**:
- Command parsing logic embedded in `callback` function
- `coqbot_minimize_text_of_body` - Parse minimization commands
- `coqbot_ci_minimize_text_of_body` - Parse CI minimization commands
- `coqbot_resume_ci_minimize_text_of_body` - Parse resume commands

---

**Navigation:** [:house: Home](README.md) | [:arrow_left: Previous: Architecture Diagrams](08b-architecture-diagrams.md) | [:arrow_up: Code Structure Analysis](08-phase1-code-structure-analysis.md) | [Next: Conclusion :arrow_right:](09-conclusion.md)
