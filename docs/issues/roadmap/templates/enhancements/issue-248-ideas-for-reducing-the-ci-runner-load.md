# Issue #248: Ideas for reducing the CI runner load

**Priority**: 🚀 Low
**Difficulty**: 🔴 Hard

## Problem Description

- Suppose the PR has a failure only in ci_fiat_crypto and you make a fix to address that. It would be helpful to be able to rerun only that job and its prerequisites--or just the jobs that failed in the last full run, or a specific named group of jobs and their prerequisites. Of course, you would la...
