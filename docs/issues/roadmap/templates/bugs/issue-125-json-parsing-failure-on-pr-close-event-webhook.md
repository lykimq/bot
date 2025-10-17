# Issue #125: JSON parsing failure on PR close event webhook.

**Priority**: 🚨 High
**Difficulty**: 🟡 Medium

## Problem Description

```  Error: Json type error: Can't get member 'html_url' of non-object type null  ```    Payload was:    ```json  {    "action": "closed",    "number": 62,    "pull_request": {      "url": "https://api.github.com/repos/saltstack-formulas/packages-formula/pulls/62",      "id": 322405876,      "node_i...
