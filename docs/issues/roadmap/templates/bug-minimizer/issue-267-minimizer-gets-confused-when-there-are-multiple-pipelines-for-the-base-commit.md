# Issue #267: Minimizer gets confused when there are multiple pipelines for the base commit

**Priority**: 🚀 Low
**Difficulty**: 🟡 Medium

## Problem Description

eg https://github.com/coq/coq/pull/17243#issuecomment-1424342254  it sees https://gitlab.com/coq/coq/-/pipelines/762836836 ("Bot merge 542d3718 and e9c7feda" from the backport testing PR) and https://gitlab.com/coq/coq/-/pipelines/763974119/ (from the actual push to v8.17)
