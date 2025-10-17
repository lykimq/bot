# Issue #155: Bot auto-mute if it's going to post the same message as before.

**Priority**: 🚀 Low
**Difficulty**: 🟡 Medium

## Problem Description

The CI minimization feature can generate many coqbot messages (when PRs are frequently updated). Example: https://github.com/coq/coq/pull/13895#issuecomment-850879151    Coqbot should not post a new message if its last message (that we can look for in the last 10 messages only, for instance) is (alm...
