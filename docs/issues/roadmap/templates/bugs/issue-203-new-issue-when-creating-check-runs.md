# Issue #203: New issue when creating check runs.

**Priority**: 🚀 Low
**Difficulty**: 🔴 Hard

## Problem Description

The creation of some check runs have failed in the last hours / days (?). An example failed query (that I could reproduce) is:    ```graphql  mutation newCheckRun($name: String!, $repoId: ID!, $headSha: String!, $status: RequestableCheckStatusState!, $title: String!, $text: String, $summary: String!...
