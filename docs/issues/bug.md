# Bug Issues - rocq-prover/bot

This document contains all **29** bug issues from the [rocq-prover/bot](https://github.com/rocq-prover/bot/issues) repository.

## [#339 - Recent issue when pushing status check](https://github.com/rocq-prover/bot/issues/339)

- **Status**: Open
- **Opened by**: Zimmi48 on Mar 31, 2025
- **Labels**: bug
- **Description**: Reported at https://github.com/rocq-prover/rocq/pull/20421#issuecomment-2763298699.  The bot was listening for incoming webhooks, but had not any access token in memory since its last restart (after idling). Then, it received two job webhooks, which apparently triggered it to do two requests for acc...

## [#335 - Confusion between stdlib and stdlib-flambda when it fails](https://github.com/rocq-prover/bot/issues/335)

- **Status**: Open
- **Opened by**: SkySkimmer on Feb 09, 2025
- **Labels**: bug
- **Description**: https://github.com/coq/coq/pull/20215#issuecomment-2646324322 >🏃 @coqbot ci minimize will minimize the following targets: ci-stdlib, ci-stdlib

## [#334 - Issues are sometimes closed multiple times, triggering the bot as many times.](https://github.com/rocq-prover/bot/issues/334)

- **Status**: Open
- **Opened by**: Zimmi48 on Jan 31, 2025
- **Labels**: bug
- **Description**: https://github.com/coq/coq/issues/19978  The issue was closed both by the PR and the commit that were merged. As a consequence, coqbot receives two webhooks that triggered its milestone changing procedure.  This seems like a minor annoyance that may not even be worth fixing.

## [#295 - "needs" and "request full CI" should be removed only after the pipeline is successfully started](https://github.com/rocq-prover/bot/issues/295)

- **Status**: Open
- **Opened by**: SkySkimmer on Nov 21, 2023
- **Labels**: bug
- **Description**: If gitlab is having issues we can end up with no label and no pipeline.

## [#289 - Add backoff strategy to auto retry of failing CI jobs](https://github.com/rocq-prover/bot/issues/289)

- **Status**: Open
- **Opened by**: erikmd on Jul 12, 2023
- **Labels**: bug
- **Description**: Salut @Zimmi48     FYI despite the line https://github.com/coq/bot/blob/master/src/actions.ml#L545  over the 49 jobs of docker-mathcomp https://gitlab.inria.fr/math-comp/docker-mathcomp/-/pipelines/833107/  there's always a dozen of jobs that fail, and that we have to retry by hand  cf. the list of ...

## [#287 - coqbot can show light pipeline as cancelled when full pipeline is running.](https://github.com/rocq-prover/bot/issues/287)

- **Status**: Open
- **Opened by**: Zimmi48 on Jun 09, 2023
- **Labels**: bug
- **Description**: Calling the `@coqbot run full CI now` command may lead to the GitLab pipeline appearing like this while the full pipeline is running:

![image](https://github.com/coq/bot/assets/1108325/deb9325b-2adc-4fc5-82f3-80572b6a62af)

This is due to GitLab having sent the "pending" and "running" webhooks ...

## [#283 - `update_bench_status` should check whether status is valid before querying GitHub.](https://github.com/rocq-prover/bot/issues/283)

- **Status**: Open
- **Opened by**: Zimmi48 on May 22, 2023
- **Labels**: bug
- **Description**: In `update_bench_status`, there are a few statuses ("created", "pending"...) that won't trigger any status check creation on GitHub:  https://github.com/coq/bot/blob/a6605542c2344a54859af39284c9149b388563d2/src/actions.ml#L505-L508    But we query GitHub in all cases, only to discard the result of t...

## [#267 - Minimizer gets confused when there are multiple pipelines for the base commit](https://github.com/rocq-prover/bot/issues/267)

- **Status**: Open
- **Opened by**: SkySkimmer on Feb 09, 2023
- **Labels**: bug, bug minimizer
- **Description**: eg https://github.com/coq/coq/pull/17243#issuecomment-1424342254  it sees https://gitlab.com/coq/coq/-/pipelines/762836836 ("Bot merge 542d3718 and e9c7feda" from the backport testing PR) and https://gitlab.com/coq/coq/-/pipelines/763974119/ (from the actual push to v8.17)

## [#250 - "bench native" should be "bench native=on"](https://github.com/rocq-prover/bot/issues/250)

- **Status**: Open
- **Opened by**: SkySkimmer on Nov 02, 2022
- **Labels**: bug
- **Description**: As discussed in https://github.com/coq/coq/wiki/Coq-Call-2022-11-02

## [#235 - Look at CACHEKEY in the gitlab yml instead of at the Dockerfile to trigger the docker job](https://github.com/rocq-prover/bot/issues/235)

- **Status**: Open
- **Opened by**: SkySkimmer on Jul 18, 2022
- **Labels**: bug
- **Description**: cf https://github.com/coq/coq/pull/16241

## [#224 - Webhook signature will be incorrect if an escape sequence is used in the body](https://github.com/rocq-prover/bot/issues/224)

- **Status**: Open
- **Opened by**: mattiasdrp on Mar 31, 2022
- **Labels**: bug
- **Description**: I haven't investigated it thoroughly but if my body contains the escape sequence `^[`, it looks like Mirage_crypto is producing the wrong SHA1 and the bot fails because it thinks that the webhook is signed with a wrong signature.    What I find strange is that I'm sure I used it previously with no p...

## [#219 - Does coqbot only look amongst suggested jobs for minimizing?](https://github.com/rocq-prover/bot/issues/219)

- **Status**: Open
- **Opened by**: JasonGross on Mar 25, 2022
- **Labels**: bug, bug minimizer
- **Description**: https://github.com/coq/coq/pull/15807#issuecomment-1077917098 :    You requested minimization of suggested failing CI jobs, but no jobs were suggested at commit fb47d4f. You can trigger minimization of ci-analysis with ci minimize all or by requesting some targets by name.    SkySkimmer commented 9 ...

## [#215 - Merging can fail if base branch has moved.](https://github.com/rocq-prover/bot/issues/215)

- **Status**: Open
- **Opened by**: Zimmi48 on Mar 22, 2022
- **Labels**: bug
- **Description**: Today, I've seen this for the first time in the bot log:    ```  Error while merging PR: Server responded to GraphQL request with errors: Base branch was modified. Review and try the merge again.  ```    It seems like this is a case that we should handle (retry automatically or warn the user?).

## [#203 - New issue when creating check runs.](https://github.com/rocq-prover/bot/issues/203)

- **Status**: Open
- **Opened by**: Zimmi48 on Feb 28, 2022
- **Labels**: bug
- **Description**: The creation of some check runs have failed in the last hours / days (?). An example failed query (that I could reproduce) is:    ```graphql  mutation newCheckRun($name: String!, $repoId: ID!, $headSha: String!, $status: RequestableCheckStatusState!, $title: String!, $text: String, $summary: String!...

## [#202 - coqbot should not post a failure message when rerunning individual jobs that succeed](https://github.com/rocq-prover/bot/issues/202)

- **Status**: Open
- **Opened by**: ppedrot on Feb 16, 2022
- **Labels**: bug, bug minimizer
- **Description**: Suppose I have a CI that fails for jobs X and Y. Then coqbot posts a message about minimization for X and Y. Now, assume I rerun only X and it succeeds. Then coqbot still posts a message informing me that minimization is available, despite Y having been untouched.    Instead, it should keep quiet. A...

## [#196 - coqbot gives bug minimizer non-existent urls](https://github.com/rocq-prover/bot/issues/196)

- **Status**: Open
- **Opened by**: JasonGross on Jan 20, 2022
- **Labels**: bug, bug minimizer
- **Description**: @Zimmi48 do you know what's up with the following failures?    - ci-sf failed because https://gitlab.com/coq/coq/-/jobs/1991212828/artifacts/download is a 404, idk what's up here  - ci-itauto failed because https://gitlab.com/coq/coq/-/jobs/1991212857/artifacts/download is a 404  - ci-relation_algeb...

## [#192 - CI targets should not match substrings ("the" should not match "category_theory")](https://github.com/rocq-prover/bot/issues/192)

- **Status**: Open
- **Opened by**: JasonGross on Dec 30, 2021
- **Labels**: bug, bug minimizer
- **Description**: https://github.com/coq/coq/pull/14748#issuecomment-1003116849

## [#174 - Coqbot minimization suggestion should be in checks tab rather than comment when the suggested list of target is empty](https://github.com/rocq-prover/bot/issues/174)

- **Status**: Open
- **Opened by**: Zimmi48 on Oct 28, 2021
- **Labels**: bug, bug minimizer
- **Description**: No description

## [#167 - Feature adding milestone to close issues is currently broken.](https://github.com/rocq-prover/bot/issues/167)

- **Status**: Open
- **Opened by**: Zimmi48 on Sep 13, 2021
- **Labels**: bug
- **Description**: This started when I upgraded graphql-ppx to 1.2.0. I've introduced many GraphQL fragments while doing this upgrade. This problem is due to reasonml-community/graphql-ppx#270.

## [#137 - Retrieved GitLab trace can be wrong](https://github.com/rocq-prover/bot/issues/137)

- **Status**: Open
- **Opened by**: Zimmi48 on Apr 28, 2021
- **Labels**: bug
- **Description**: In https://github.com/coq/coq/pull/14202/checks?check_run_id=2459425982, the retrieved trace contains:    ```html  <body>    <a href="/">      <img src="data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iMjEwIiBoZWlnaHQ9IjIxMCIgdmlld0JveD0iMCAwIDIxMCAyMTAiIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyI+CiAgP...

## [#136 - New strange errors in log.](https://github.com/rocq-prover/bot/issues/136)

- **Status**: Open
- **Opened by**: Zimmi48 on Apr 23, 2021
- **Labels**: bug
- **Description**: I've seen this happen several times in the log from yesterday and it's new to me. The "No space left on device" error is surprising because sometimes after, we can see some `git fetch` succeeding. I don't know what this `/app/config.lock` is.    ```  22 Apr 2021 15:32:56.695156 <190>1 2021-04-22T13:...

## [#127 - coqbot starts creating empty, unconfigured repositories](https://github.com/rocq-prover/bot/issues/127)

- **Status**: Open
- **Opened by**: myii on Feb 06, 2021
- **Labels**: bug
- **Description**: All of the info is in this topic:    https://coq.zulipchat.com/#narrow/stream/243318-coqbot-devs.20.26.20users/topic/coqbot.20starts.20creating.20empty.2C.20unconfigured.20repositories    As @Zimmi48 mentioned, this is currently unavoidable because of:    * https://docs.gitlab.com/ee/gitlab-basics/c...

## [#125 - JSON parsing failure on PR close event webhook.](https://github.com/rocq-prover/bot/issues/125)

- **Status**: Open
- **Opened by**: Zimmi48 on Jan 23, 2021
- **Labels**: bug
- **Description**: ```  Error: Json type error: Can't get member 'html_url' of non-object type null  ```    Payload was:    ```json  {    "action": "closed",    "number": 62,    "pull_request": {      "url": "https://api.github.com/repos/saltstack-formulas/packages-formula/pulls/62",      "id": 322405876,      "node_i...

## [#124 - GitLab subgroups fix for #120 still needs some finalisation](https://github.com/rocq-prover/bot/issues/124)

- **Status**: Open
- **Opened by**: myii on Dec 18, 2020
- **Labels**: bug
- **Description**: #120 has been fixed but there are still a couple of outstanding problems to be resolved, as based on this discussion with @Zimmi48 in Zulip:    https://coq.zulipchat.com/#narrow/stream/243318-coqbot-devs.20.26.20users/topic/How.20to.20troubleshoot.20coqbot.3F/near/220304637    If I understand the di...

## [#121 - Removing (and changing?) the `coqbot.toml` does not work without intervention](https://github.com/rocq-prover/bot/issues/121)

- **Status**: Open
- **Opened by**: myii on Dec 12, 2020
- **Labels**: bug
- **Description**: Linked to #120.  Was also discovered by @Zimmi48 in [this topic](https://coq.zulipchat.com/#narrow/stream/243318-coqbot-devs.20.26.20users/topic/How.20to.20troubleshoot.20coqbot.3F) in Zulip.    This was the `coqbot.toml` config that failed:    ```toml  [mapping]  gitlab = "saltstack-formulas/formul...

## [#114 - Do not take into account deleted or modified overlays, only new ones.](https://github.com/rocq-prover/bot/issues/114)

- **Status**: Open
- **Opened by**: Zimmi48 on Oct 23, 2020
- **Labels**: bug
- **Description**: Cf. https://github.com/coq/coq/pull/13177#issuecomment-715185323

## [#111 - coqbot did not push to GitLab (or rather GitLab didn't answer to `git push`).](https://github.com/rocq-prover/bot/issues/111)

- **Status**: Open
- **Opened by**: Zimmi48 on Sep 29, 2020
- **Labels**: bug
- **Description**: Something strange happened two days ago:    ```  28 Sep 2020 07:15:54.496129 <190>1 2020-09-28T05:15:53.867188+00:00 app web.1 - - From https://github.com/jfehrle/coq  28 Sep 2020 07:15:54.566205 <190>1 2020-09-28T05:15:53.867244+00:00 app web.1 - - * [new branch] prodn_misc_chapts -> head-prodn_mis...

## [#87 - deploy shouldn't run on forks](https://github.com/rocq-prover/bot/issues/87)

- **Status**: Open
- **Opened by**: SkySkimmer on Aug 20, 2020
- **Labels**: bug
- **Description**: to prevent spurious errors like https://github.com/SkySkimmer/bot/runs/1007329647

## [#34 - Possible race when pushing quickly several times to a PR](https://github.com/rocq-prover/bot/issues/34)

- **Status**: Open
- **Opened by**: Zimmi48 on May 06, 2019
- **Labels**: bug
- **Description**: People can push quickly several times to a PR, especially when using GitHub's suggestion feature, and applying suggestions. If the `make-ancestor.sh` script is run several times in parallel for the same PR, the script can fail with:    ```  fatal: 'pr-10002' is already checked out at '/tmp/tmp.oKe1D...

---
*Last updated: 2025-10-17 14:46:15*
*Source: [rocq-prover/bot Issues](https://github.com/rocq-prover/bot/issues)*
