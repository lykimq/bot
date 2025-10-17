# Unlabeled Issues - rocq-prover/bot

This document contains all **5** unlabeled issues from the [rocq-prover/bot](https://github.com/rocq-prover/bot/issues) repository.

## [#247 - Extract GitHub endpoint handling.](https://github.com/rocq-prover/bot/issues/247)

- **Status**: Open
- **Opened by**: Zimmi48 on Oct 18, 2022
- **Labels**: None
- **Description**: The goal would be to eventually be more declarative in order to add some tests of the results without executing any request.

## [#226 - Account for new pipeline status: created.](https://github.com/rocq-prover/bot/issues/226)

- **Status**: Open
- **Opened by**: Zimmi48 on May 03, 2022
- **Labels**: None
- **Description**: Should avoid issues like the one observed at: https://github.com/coq/coq/pull/15986/checks?check_run_id=6270508750

## [#213 - Change expiration date properly](https://github.com/rocq-prover/bot/issues/213)

- **Status**: Open
- **Opened by**: mattiasdrp on Mar 18, 2022
- **Labels**: None
- **Description**: I mistakenly wrote `55*10` instead of `60*8` since it's not 55 minutes instead of 60 but 8 minutes instead of 10    A safe way would be, if you want me to try it, to     > date && curl -I https://api.github.com | grep -Fi "date"    Read the two dates, compute the difference in time and create the ex...

## [#189 - Be more lax on what merged PRs we detect, but post an alert.](https://github.com/rocq-prover/bot/issues/189)

- **Status**: Open
- **Opened by**: Zimmi48 on Dec 17, 2021
- **Labels**: None
- **Description**: It happens more often than I expected that maintainers use the merge button, even though it's explicitly forbidden in the merging rules. Examples by different maintainers:    - https://github.com/coq/coq/pull/15243  - https://github.com/coq/coq/pull/11993  - https://github.com/coq/coq/pull/11629  - ...

## [#157 - Parse webhooks for GitHub App installations.](https://github.com/rocq-prover/bot/issues/157)

- **Status**: Open
- **Opened by**: Zimmi48 on Jul 19, 2021
- **Labels**: None
- **Description**: 

---
*Last updated: 2025-10-17 14:46:15*
*Source: [rocq-prover/bot Issues](https://github.com/rocq-prover/bot/issues)*
