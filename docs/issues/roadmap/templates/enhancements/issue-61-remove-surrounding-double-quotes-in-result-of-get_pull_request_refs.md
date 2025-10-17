# Issue #61: Remove surrounding double quotes in result of get_pull_request_refs.

**Priority**: 🚀 Low
**Difficulty**: 🟡 Medium

## Problem Description

In PR #59, I had to add this trick to make the new feature properly work:    https://github.com/coq/bot/blob/d5e751272a6222efac4ef4cb95b70080c8cbd526/bot.ml#L522-L523    It would be good to remove it by finding the cause for these double quotes and/or stripping them out, but first we should verify t...
