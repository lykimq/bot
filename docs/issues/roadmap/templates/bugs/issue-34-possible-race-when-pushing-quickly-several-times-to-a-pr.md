# Issue #34: Possible race when pushing quickly several times to a PR

**Priority**: 🚨 High
**Difficulty**: 🟡 Medium

## Problem Description

People can push quickly several times to a PR, especially when using GitHub's suggestion feature, and applying suggestions. If the `make-ancestor.sh` script is run several times in parallel for the same PR, the script can fail with:    ```  fatal: 'pr-10002' is already checked out at '/tmp/tmp.oKe1D...
