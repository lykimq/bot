# Issue #287: coqbot can show light pipeline as cancelled when full pipeline is running.

**Priority**: 🚀 Low
**Difficulty**: 🟡 Medium

## Problem Description

Calling the `@coqbot run full CI now` command may lead to the GitLab pipeline appearing like this while the full pipeline is running:    ![image](https://github.com/coq/bot/assets/1108325/deb9325b-2adc-4fc5-82f3-80572b6a62af)    This is due to GitLab having sent the "pending" and "running" webhooks ...
