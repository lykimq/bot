# Issue #190: Should coqbot compose the comment rather than run-coq-bug-minimizer?

**Priority**: 🚨 High
**Difficulty**: 🔴 Hard

## Problem Description

It may make sense to have coqbot compose the reply comment instead (this would also allow coqbot to do the truncation of the file only for the comment and not also for the submitted PR).  If we move the curl response to happen after artifact upload, then the bot could perhaps link directly to the ar...
