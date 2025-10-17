# :file_folder: Feature Modules

> Break down monolithic `actions.ml` into 10 independent feature modules

## :jigsaw: Current Problem

`actions.ml` contains 3223 lines with all features mixed together:
- **Monolithic Structure**: All features in one giant file
- **No Separation**: Features are tightly coupled
- **Hard to Test**: Can't test individual features in isolation
- **Difficult Maintenance**: Changes affect unrelated features

## :gear: Feature Modules (10 modules)

### CI Reporting (`src/features/ci_reporting.ml`)
**Purpose**: Report CI results from GitLab to GitHub
**Key Functions**:
- `send_status_check` - Send detailed status checks
- `job_failure` - Handle failed job reporting
- `trace_action` - Extract and format trace information
**Related Issues**: #247, #335, #327

### Documentation URLs (`src/features/doc_urls.ml`)
**Purpose**: Post links to generated documentation artifacts
**Key Functions**:
- `send_doc_url_job` - Extract and post doc URLs
- `send_doc_url` - Entry point for doc URL feature

### Benchmark Tracking (`src/features/benchmarks.ml`)
**Purpose**: Track and report benchmark results (Rocq-specific)
**Key Functions**:
- `fetch_bench_results` - Download benchmark data
- `bench_comment` - Post benchmark comparison comments
- `run_bench` - Execute benchmark runs

### CI Minimization (`src/features/ci_minimizer.ml`)
**Purpose**: Minimize failing CI tests automatically
**Key Functions**:
- `run_ci_minimization` - Run minimization on CI failures
- `ci_minimization_suggest` - Suggest minimization opportunities
- `minimize_failed_tests` - Execute test minimization
**Related Issues**: #170

### Bug Minimization (`src/features/bug_minimizer.ml`)
**Purpose**: Minimize bug reports using `coq-bug-minimizer`
**Key Functions**:
- `run_coq_minimizer` - Execute bug minimizer script
- `coq_bug_minimizer_results_action` - Handle minimization results

### Pipeline Handler (`src/features/pipeline_handler.ml`)
**Purpose**: Handle GitLab pipeline events and management
**Key Functions**:
- `pipeline_action` - Handle GitLab pipeline events
- `create_pipeline_summary` - Create pipeline summaries

### Milestone Sync (`src/features/milestone_sync.ml`)
**Purpose**: Sync milestones between closed issues and PRs
**Key Functions**:
- `adjust_milestone` - Adjust milestone when issues are closed

### Backport Manager (`src/features/backport_manager.ml`)
**Purpose**: Manage backport workflow (Rocq Prover only)
**Key Functions**:
- `project_action` - Handle project card movements
- `add_to_column` - Add items to project columns
- `add_remove_labels` - Manage labels

### Stale PR Management (`src/features/stale_pr.ml`)
**Purpose**: Mark and close stale PRs
**Key Functions**:
- `rocq_check_stale_pr` - Check for stale PRs
- `rocq_check_needs_rebase_pr` - Check PRs needing rebase
- `apply_after_label` - Apply labels after time delay

### Command Parser (`src/features/command_parser.ml`)
**Purpose**: Parse bot commands from comments and issues
**Key Functions**:
- Command parsing logic from `bot.ml`
- `coqbot_minimize_text_of_body` - Parse minimization commands
- `coqbot_ci_minimize_text_of_body` - Parse CI minimization commands
**Related Issues**: #325
