# Issue #280: Reduce log verbosity by printing less output for git commands

**Priority**: 🚀 Low
**Difficulty**: 🔴 Hard

## Problem Description

Currently, the output of `git fetch` or `git merge` can be arbitrarily long...  But we are limited in the size of logs that we can save for free with Papertrails.
