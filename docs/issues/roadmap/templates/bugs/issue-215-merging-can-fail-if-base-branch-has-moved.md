# Issue #215: Merging can fail if base branch has moved.

**Priority**: 🚨 High
**Difficulty**: 🔴 Hard

## Problem Description

Today, I've seen this for the first time in the bot log:    ```  Error while merging PR: Server responded to GraphQL request with errors: Base branch was modified. Review and try the merge again.  ```    It seems like this is a case that we should handle (retry automatically or warn the user?).
