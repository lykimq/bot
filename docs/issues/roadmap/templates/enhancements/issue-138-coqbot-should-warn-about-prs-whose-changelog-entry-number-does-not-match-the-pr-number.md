# Issue #138: coqbot should warn about PRs whose changelog entry number does not match the PR number

**Priority**: 🚀 Low
**Difficulty**: 🔴 Hard

## Problem Description

Sometimes I run `dev/tools/make-changelog` before actually creating the PR, and thus can get the number wrong.  It would be nice if coqbot warned me if the PR number was not linked in any changelog file created by the PR.
