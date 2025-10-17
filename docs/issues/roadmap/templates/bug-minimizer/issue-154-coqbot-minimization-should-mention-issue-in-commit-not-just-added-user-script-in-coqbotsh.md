# Issue #154: coqbot minimization should mention Issue # in commit, not just "Added user script in coqbot.sh"

**Priority**: 🔧 Medium
**Difficulty**: 🔴 Hard

## Problem Description

Currently when a user initiates (non-ci) minimization, the commit message is just "Added user script in coqbot.sh".  It should be something like "Added user script from coq/coq#<ISSUE #> in coqbot.sh" and should ideally include a link to the particular comment in the body of the commit message.
