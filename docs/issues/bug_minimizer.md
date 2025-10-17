# Bug Minimizer Issues - rocq-prover/bot

This document contains all **7** bug minimizer issues from the [rocq-prover/bot](https://github.com/rocq-prover/bot/issues) repository.

## [#292 - [CI minimization] File name should include project info rather than be `bug.v`](https://github.com/rocq-prover/bot/issues/292)

- **Status**: Open
- **Opened by**: JasonGross on Sep 02, 2023
- **Labels**: bug minimizer
- **Description**: Suggested by @herbelin , I believe, on the feedback survey    This plausibly needs some edits on the bug minimizer runner, and we'll need to edit the yml job to have the custom file name

## [#284 - Issue in the bug minimizer code with the call to git push --delete.](https://github.com/rocq-prover/bot/issues/284)

- **Status**: Open
- **Opened by**: Zimmi48 on May 25, 2023
- **Labels**: bug minimizer
- **Description**: There is no call to `init_git_bare_repository` in the code leading to:    https://github.com/coq/bot/blob/692ddd94dec4dd4a3e90e2b41ffe40884ec452ed/src/actions.ml#L1997    So this can fail with the following error:    ```  Executing command: git push https://coqbot:XXXXX@github.com/coq-community/run-...

## [#271 - Document coqbot autominimizer defaults](https://github.com/rocq-prover/bot/issues/271)

- **Status**: Open
- **Opened by**: SkySkimmer on Feb 12, 2023
- **Labels**: bug minimizer
- **Description**: https://github.com/coq/coq/wiki/Coqbot-minimize-feature says  >`@coqbot minimize` can take options after the minimize, including things like coq: 8.16 ...    It seems 8.16 is the default? Not sure what the default ocaml is, or what the other options are.  Also I guess the version is an opam package ...

## [#195 - Fix pluralization of unfound jobs](https://github.com/rocq-prover/bot/issues/195)

- **Status**: Open
- **Opened by**: JasonGross on Dec 30, 2021
- **Labels**: bug minimizer
- **Description**: "could not be found among the jobs lint" is mis-pluralized in https://github.com/coq/coq/pull/14748#issuecomment-1003116849, and maybe we should indicate what the list of jobs we're reporting is (is "lint" a candidate for minimization? a failed job?  a job?)

## [#194 - CI targets should be sanitized or quoted](https://github.com/rocq-prover/bot/issues/194)

- **Status**: Open
- **Opened by**: JasonGross on Dec 30, 2021
- **Labels**: bug minimizer
- **Description**: There's a hidden target `</code>` and a misleading target `ci-unimath</summary>` at https://github.com/coq/coq/pull/14748#issuecomment-1003116849

## [#193 - CI targets should handle de-duplication better in response message](https://github.com/rocq-prover/bot/issues/193)

- **Status**: Open
- **Opened by**: JasonGross on Dec 30, 2021
- **Labels**: bug minimizer
- **Description**: Currently there's no indication that a target was doubled in the request, even though it only triggers once.     https://github.com/coq/coq/pull/14748#issuecomment-1003120077

## [#191 - Coqbot could automatically update minimization suggestions that are waiting base pipeline completion.](https://github.com/rocq-prover/bot/issues/191)

- **Status**: Open
- **Opened by**: Zimmi48 on Dec 27, 2021
- **Labels**: bug minimizer
- **Description**: There are two options:    1. it goes back and edits comments containing "you may want to wait until the base pipeline finishes running"  2. it delays posting any comment until the base pipeline completes    The base pipeline check could be edited with links back to the CI minimization instances that...

---
*Last updated: 2025-10-17 14:46:15*
*Source: [rocq-prover/bot Issues](https://github.com/rocq-prover/bot/issues)*
