# Issue #221: @coqbot bench should allow customizing the list of packages to bench

**Priority**: 🚀 Low
**Difficulty**: 🟡 Medium

## Problem Description

See https://github.com/coq/coq/pull/15860#issuecomment-1080460118 for motivation.  I want to be able to write @coqbot bench coq-fiat-crypto-with-bedrock.  Ideally coqbot would let me know immediately if any of the packages don't exist in the relevant opam setup, but this is not mandatory.
