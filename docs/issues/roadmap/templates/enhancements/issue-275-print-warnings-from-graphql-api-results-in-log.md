# Issue #275: Print warnings from GraphQL API results in log.

**Priority**: 🚀 Low
**Difficulty**: 🟡 Medium

## Problem Description

A GraphQL API result can contain warnings. An example is given in https://github.blog/changelog/2022-11-10-graphql-legacy-global-id-deprecation-message/. We should look for this field and print it in the log when this happens.
