# Issue #108: Support @coqbot: merge tomorrow and @coqbot: merge when CI passes

**Priority**: 🔧 Medium
**Difficulty**: 🔴 Hard

## Problem Description

In the two cases, @coqbot should immediately answer with a comment (after having checked the usual conditions for merging a PR). Any comment, review or review comment posted afterward should abort the merge. In the case of "merge tomorrow" (or variants), the bot should clearly announce after which p...
