# Issue #202: coqbot should not post a failure message when rerunning individual jobs that succeed

**Priority**: 🚨 High
**Difficulty**: 🔴 Hard

## Problem Description

Suppose I have a CI that fails for jobs X and Y. Then coqbot posts a message about minimization for X and Y. Now, assume I rerun only X and it succeeds. Then coqbot still posts a message informing me that minimization is available, despite Y having been untouched.    Instead, it should keep quiet. A...
