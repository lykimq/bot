# Issue #18: mergify/bors-like merging process

**Priority**: 🚀 Low
**Difficulty**: 🔴 Hard

## Problem Description

This would ensure better reliability because CI would run on commits before they become the `HEAD` of `master` or a stable branch.    This would however require first a (planned) change to the way overlays are handled (cf. coq/coq#6724).    There is also a question on how to allow the `pkg:nix` job ...
