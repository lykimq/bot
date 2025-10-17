# Issue #284: Issue in the bug minimizer code with the call to git push --delete.

**Priority**: 🚨 High
**Difficulty**: 🟡 Medium

## Problem Description

There is no call to `init_git_bare_repository` in the code leading to:    https://github.com/coq/bot/blob/692ddd94dec4dd4a3e90e2b41ffe40884ec452ed/src/actions.ml#L1997    So this can fail with the following error:    ```  Executing command: git push https://coqbot:XXXXX@github.com/coq-community/run-...
