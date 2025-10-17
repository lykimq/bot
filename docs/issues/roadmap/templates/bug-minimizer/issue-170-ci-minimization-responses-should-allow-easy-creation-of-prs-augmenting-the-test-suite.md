# Issue #170: CI minimization responses should allow easy creation of PRs augmenting the test-suite

**Priority**: 🚀 Low
**Difficulty**: 🔴 Hard

## Problem Description

It might be nice to have a link in the "here's the minimized file" comment for "Click here to have coqbot create a PR against this PR branch adding this file to the test-suite.". This link could target an endpoint of coqbot with a GET request containing a link to the workflow run, the CI target, and...
