# Issue #38: Add/Remove pull request from backporting project when its milestone is updated.

**Priority**: 🔧 Medium
**Difficulty**: 🔴 Hard

## Problem Description

In particular, when a milestone is removed, the issue event with action "demilestoned" provides the previous milestone as a top-level value, and the new milestone as a child of the issue value (however, it doesn't contain the info whether the pull request has been merged).
