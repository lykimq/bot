# Issue #259: Re-run all checks and re-run failed checks are not supported.

**Priority**: 🚨 High
**Difficulty**: 🟡 Medium

## Problem Description

@coqbot already supports the "Re-run" button for an individual failed check in the GitHub Checks tab (see the left-hand side in the screenshot below) but it doesn't support the new "re-run all checks" and "re-run failed checks" features (they lead to a `check_suite.rerequested` event, see example JS...
