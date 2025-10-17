# Issue #178: coqbot should be able to do traditional delta debugging on a PR

**Priority**: 🚨 High
**Difficulty**: 🟡 Medium

## Problem Description

I was reading up on delta debugging and found that one use case is to find the minimal set of changes in a diff that can be removed to restore working behavior.  When we get a fast enough file (such as https://github.com/coq/coq/pull/14727#issuecomment-957082119 , perhaps in conjunction with #177), ...
