# :hammer_and_wrench: Utility Modules

> Split `helpers.ml` into 4 focused utility modules

## :wrench: Current Problem

`helpers.ml` contains 185 lines of mixed utilities:
- **Mixed Concerns**: String, HTTP, and branch utilities together
- **No Focus**: Utilities for different purposes in one file
- **Hard to Find**: Related functions scattered throughout
- **No Reusability**: Utilities tightly coupled to specific use cases

## :gear: Utility Modules (4 modules)

### String Utils (`src/utils/string_utils.ml`)
**Purpose**: String manipulation and parsing utilities
**Key Functions**:
- `string_match`, `fold_string_matches` - String matching utilities
- `trim_comments` - Remove HTML comments
- `strip_quoted_bot_name` - Handle quoted bot mentions
- `first_line_of_string` - Extract first line
- `remove_between` - String manipulation

### Branch Utils (`src/utils/branch_utils.ml`)
**Purpose**: Branch name and Git branch utilities
**Key Functions**:
- `pr_from_branch` - Extract PR number from branch name
- `github_repo_of_gitlab_url` - Map GitLab URLs to GitHub repos
- `parse_gitlab_repo_url` - Parse GitLab repository URLs

### Comment Utils (`src/utils/comment_utils.ml`)
**Purpose**: Comment processing and formatting
**Key Functions**:
- `code_wrap` - Code formatting for comments
- Comment parsing utilities
- Comment validation functions

### HTTP Utils (`src/utils/http_utils.ml`)
**Purpose**: HTTP operations and file downloads
**Key Functions**:
- `download`, `download_cps`, `download_to` - File download utilities
- `copy_stream` - Stream copying utility
- HTTP request/response utilities
