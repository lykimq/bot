# Issue #187: coqbot could add `suggested: rebase` when it might fix CI failures

**Priority**: 🚨 High
**Difficulty**: 🔴 Hard

## Problem Description

When checks finish at the head of a branch in Coq, all open PR with a CI failure on ci-XXX which also fails on the CI of the base commit, but not on the new head could get a `suggested: rebase` label (probably not a comment, because that would flood people with multiple open PRs) (and the checks sta...
