# :test_tube: Phase 1.3: Testing Infrastructure

**Navigation:** [:house: Home](README.md) | [:arrow_left: Previous: Configuration Refactoring](05-phase0-conf-refact.md) | [Next: Phase 2 Overview :arrow_right:](07-phase1-overview.md)

---

## Table of Contents
- [Unit Test Framework](#unit-test-framework)

---

## :microscope: Unit Test Framework

### Problem

- Zero automated tests currently
- Manual testing only (requires Docker + ngrok + real GitHub/GitLab accounts)
- Refactoring is risky without safety net
- Edge cases aren't systematically verified
- Contributors need complex local setup

**Related Issues**: [#170](docs/issues/roadmap/templates/bug-minimizer/issue-170-ci-minimization-responses-should-allow-easy-creation-of-prs-augmenting-the-test-suite.md) (CI Minimization Responses Should Allow Easy Creation of PRs Augmenting the Test Suite)

### Solution

Set up OCaml testing framework with **Alcotest**:

### Why Alcotest?

**Alcotest** is chosen as the primary testing framework for this project because:

- **Lightweight & Simple**: Minimal setup, easy to learn for contributors
- **OCaml Ecosystem Standard**: Widely adopted in OCaml community
- **Async Support**: Built-in support for `Lwt` (which this bot uses extensively)
- **Good Error Messages**: Clear, readable test failure output
- **CI Integration**: Works well with continuous integration pipelines
- **Dependency Minimal**: Doesn't add heavy dependencies to the project

**Alternative OCaml Testing Frameworks**:

- **OUnit2**: More feature-rich but heavier, good for complex test scenarios
- **QCheck**: Property-based testing, excellent for testing with random inputs
- **Crowbar**: Fuzzing-based testing, great for finding edge cases
- **Ppx_expect**: Snapshot testing, useful for testing output formatting

**Recommendation**: Start with Alcotest for basic unit tests, consider adding QCheck later for property-based testing of configuration parsing and URL handling.

**Project structure**:
```
test/
├── dune                    # Test configuration
├── test_helpers.ml         # Test string/URL utilities
├── test_config.ml          # Test TOML parsing
├── test_git_utils.ml       # Test git operations (mocked)
└── fixtures/               # Test data files
    ├── valid_config.toml   # Sample valid TOML config for testing
    └── invalid_config.toml # Sample invalid TOML config for testing
```

### Test Fixtures Explained

The `fixtures/` directory contains **test data files** used by unit tests:

- **`valid_config.toml`** - A properly formatted TOML configuration file containing:
  - Valid repository mappings
  - Correct field formats
  - All required configuration sections
  - Used to test successful configuration parsing

- **`invalid_config.toml`** - A malformed TOML file containing:
  - Invalid TOML syntax (missing quotes, brackets, etc.)
  - Missing required fields
  - Invalid field values
  - Used to test error handling and validation

These fixtures allow tests to verify that the configuration parser correctly handles both valid and invalid input scenarios without requiring real configuration files.

### Test Coverage Priority

1. **helpers.ml** - String manipulation and URL parsing
   - `pr_from_branch` - Extract PR number from branch names
   - `github_repo_of_gitlab_url` - Repository mapping
   - `parse_gitlab_repo_url` - URL parsing (already has inline tests!)
   - `trim_comments` - HTML comment removal

2. **config.ml** - TOML configuration parsing
   - Valid configuration loading
   - Invalid TOML rejection
   - Missing required fields
   - Duplicate repository detection

3. **git_utils.ml** - Git operations (with mocking)
   - Branch name generation
   - Error handling for failed git commands

### Success Criteria

- :white_check_mark: `dune runtest` runs successfully
- :white_check_mark: 30+ unit tests covering core utilities
- :white_check_mark: Tests run in CI pipeline
- :white_check_mark: Can refactor confidently with test safety net

---

**Navigation:** [:house: Home](README.md) | [:arrow_left: Previous: Configuration Refactoring](05-phase0-conf-refact.md) | [Next: Phase 2 Overview :arrow_right:](07-phase1-overview.md)