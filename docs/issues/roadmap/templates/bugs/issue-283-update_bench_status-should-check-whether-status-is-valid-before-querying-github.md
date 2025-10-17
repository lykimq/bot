# Issue #283: `update_bench_status` should check whether status is valid before querying GitHub.

**Priority**: 🚀 Low
**Difficulty**: 🟡 Medium

## Problem Description

In `update_bench_status`, there are a few statuses ("created", "pending"...) that won't trigger any status check creation on GitHub:  https://github.com/coq/bot/blob/a6605542c2344a54859af39284c9149b388563d2/src/actions.ml#L505-L508    But we query GitHub in all cases, only to discard the result of t...
