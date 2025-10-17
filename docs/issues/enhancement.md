# Enhancement Issues - rocq-prover/bot

This document contains all **78** enhancement issues from the [rocq-prover/bot](https://github.com/rocq-prover/bot/issues) repository.

## [#346 - [feature] minimizer should support regression minimization](https://github.com/rocq-prover/bot/issues/346)

- **Status**: Open
- **Opened by**: JasonGross on Jul 19, 2025
- **Labels**: enhancement, bug minimizer
- **Description**: When reporting regressions (e.g., https://github.com/rocq-prover/rocq/issues/20938 if it weren't already minimized), it would be nice to be able to tag the bot and ask it to minimize against two versions of Coq (a la CI minimization).  Probably the way to do this is to have multiple opam switches wi...

## [#342 - [wish] hide messages that are just orders to the bot](https://github.com/rocq-prover/bot/issues/342)

- **Status**: Open
- **Opened by**: gares on Jun 11, 2025
- **Labels**: enhancement
- **Description**: Like if the message is just `@bot run full ci`, the entire message should be collapsed as outdated to avoid keeping the PR tidy.

## [#327 - coqbot should not add "needs: full ci" after trivial rebase](https://github.com/rocq-prover/bot/issues/327)

- **Status**: Open
- **Opened by**: andres-erbsen on Oct 29, 2024
- **Labels**: enhancement
- **Description**: coqbot should not add "needs: full ci" after trivial rebase. I've gotten used to gerrit allowing silent rebasing and it's so nice.    E.g. [andres-erbsen force-pushed the ZModOffset branch from d02d498 to f98674d](https://github.com/coq/coq/pull/19753).

## [#325 - wish: answer to `@rocqbot`](https://github.com/rocq-prover/bot/issues/325)

- **Status**: Open
- **Opened by**: gares on Oct 25, 2024
- **Labels**: enhancement
- **Description**: so that we can get used to write that instead

## [#323 - Mask secret data in all logging functions](https://github.com/rocq-prover/bot/issues/323)

- **Status**: Open
- **Opened by**: JasonGross on Oct 14, 2024
- **Labels**: enhancement
- **Description**: BTW, it would be good to introduce a more general mask to all our logging functions. I wonder if there is a library that could be used to do this.    _Originally posted by @Zimmi48 in https://github.com/coq/bot/pull/317#pullrequestreview-2364963014_

## [#313 - Checks tab should list duration of the "Preparing the "custom" executor" or "Executing "step_script" stage of the job script" step when the job times out](https://github.com/rocq-prover/bot/issues/313)

- **Status**: Open
- **Opened by**: JasonGross on Jan 04, 2024
- **Labels**: enhancement
- **Description**: "Preparing the "custom" executor" took 26:32 [here](https://gitlab.inria.fr/coq/coq/-/jobs/3743217) and 55:40 [here](https://gitlab.inria.fr/coq/coq/-/jobs/3743281).  I'd like the corresponding checks tab (e.g., [this](https://github.com/coq/coq/pull/18094/checks?check_run_id=20146520829)) to say so...

## [#304 - Coqbot could schedule a benchmark of CI run when the tag is set.](https://github.com/rocq-prover/bot/issues/304)

- **Status**: Open
- **Opened by**: ejgallego on Jun 14, 2024
- **Labels**: enhancement
- **Description**: I was thinking that `coqbot` could schedule a CI or bench run when we set the corresponding tag; this would avoid comment noise, and it replaces often two steps (setting the tag + calling coqbot) by one.    I tried searching for this and I didn't find an issue, I hope it is not a duplicate.

## [#301 - Minimizer on issues should be able to compare coq versions ](https://github.com/rocq-prover/bot/issues/301)

- **Status**: Open
- **Opened by**: SkySkimmer on Mar 13, 2024
- **Labels**: enhancement, bug minimizer
- **Description**: ie it should be possible to do something like `@coqbot minimize good=coq.8.18 bad=coq.dev (some script)`    For instance on https://github.com/coq/coq/issues/18593 naive minimization produces bad results.

## [#300 - coqbot should be able to add reactions rather than just posting comments (e.g., for minimization)](https://github.com/rocq-prover/bot/issues/300)

- **Status**: Open
- **Opened by**: JasonGross on Mar 08, 2024
- **Labels**: enhancement
- **Description**: For future reference: we want to edit https://github.com/coq/bot/blob/master/bot-components/GitHub_mutations.ml and https://github.com/coq/bot/blob/master/bot-components/GitHub_GraphQL.ml and the references are:  - https://docs.github.com/en/graphql/reference/mutations#addreaction  - https://docs.gi...

## [#288 - Using coqbot to run OCaml benchmarks](https://github.com/rocq-prover/bot/issues/288)

- **Status**: Open
- **Opened by**: Zimmi48 on Jul 06, 2023
- **Labels**: enhancement
- **Description**: The OCaml project has two benchmark suites. One is [Sandmark](https://sandmark.ocamllabs.io/) and the other is the [ocurrent bench](https://autumn.ocamllabs.io/). The OCaml developers would like to have better support for triggering and reporting these benchmarks, and we have discussed some plans wi...

## [#286 - Support for `@coqbot run full CI` in PR body.](https://github.com/rocq-prover/bot/issues/286)

- **Status**: Open
- **Opened by**: Zimmi48 on Jun 05, 2023
- **Labels**: enhancement
- **Description**: Currently, it is only taken into account in PR comments or review comments.    Examples show that users forget about the `request: full CI` label and want to use the command in the PR body instead: https://github.com/coq/coq/pull/17684

## [#280 - Reduce log verbosity by printing less output for git commands](https://github.com/rocq-prover/bot/issues/280)

- **Status**: Open
- **Opened by**: Zimmi48 on Apr 26, 2023
- **Labels**: enhancement
- **Description**: Currently, the output of `git fetch` or `git merge` can be arbitrarily long...  But we are limited in the size of logs that we can save for free with Papertrails.

## [#279 - Document coqbot bench.](https://github.com/rocq-prover/bot/issues/279)

- **Status**: Open
- **Opened by**: Zimmi48 on Apr 21, 2023
- **Labels**: enhancement
- **Description**: If I'm not mistaken, this feature is not documented yet. A good candidate place could be: https://github.com/coq/coq/wiki/Benchmarking

## [#277 - coqbot minimizer should upload and attach the file to GH when it's truncated](https://github.com/rocq-prover/bot/issues/277)

- **Status**: Open
- **Opened by**: JasonGross on Apr 17, 2023
- **Labels**: enhancement, bug minimizer
- **Description**: We can probably use https://github.com/actions/upload-artifact/issues/50 / https://docs.github.com/en/free-pro-team@latest/rest/reference/actions#download-an-artifact to get a link to the uploaded artifact(s) from the GH Actions job, and include the URL in the data we send to the bot.

## [#275 - Print warnings from GraphQL API results in log.](https://github.com/rocq-prover/bot/issues/275)

- **Status**: Open
- **Opened by**: Zimmi48 on Apr 16, 2023
- **Labels**: enhancement
- **Description**: A GraphQL API result can contain warnings. An example is given in https://github.blog/changelog/2022-11-10-graphql-legacy-global-id-deprecation-message/. We should look for this field and print it in the log when this happens.

## [#266 - Make documentation of coqbot autominimization more discoverable](https://github.com/rocq-prover/bot/issues/266)

- **Status**: Open
- **Opened by**: JasonGross on Jan 26, 2023
- **Labels**: enhancement, good first issue, bug minimizer
- **Description**: @JasonGross The documentation of this feature, currently located at https://github.com/coq/coq/wiki/Coqbot-minimize-feature, should probably be updated.    BTW, I don't think that the current location for the documentation of this feature is good, because it's hardly discoverable. Probably, it shoul...

## [#264 - Better error messages on uncaught exceptions](https://github.com/rocq-prover/bot/issues/264)

- **Status**: Open
- **Opened by**: JasonGross on Jan 23, 2023
- **Labels**: enhancement
- **Description**: I see things like  ```  Error: Unhandled exception: (Invalid_argument Str.matched_group)  ```  and similar messages for stack overflow (on logentries), and it would be nice to get a backtrace instead, so I can   Can this be done?

## [#261 - Ensure that no unwanted TODOs or FIXMEs remain before merging](https://github.com/rocq-prover/bot/issues/261)

- **Status**: Open
- **Opened by**: maximedenes on Dec 06, 2022
- **Labels**: enhancement
- **Description**: It would be a nice feature. Not sure if it should be the job of coqbot or a linter.

## [#260 - Ensure calls to GitHub's REST API are sending the proper version header.](https://github.com/rocq-prover/bot/issues/260)

- **Status**: Open
- **Opened by**: Zimmi48 on Nov 29, 2022
- **Labels**: enhancement
- **Description**: See https://github.blog/2022-11-28-to-infinity-and-beyond-enabling-the-future-of-githubs-rest-api-with-api-versioning/

## [#259 - Re-run all checks and re-run failed checks are not supported.](https://github.com/rocq-prover/bot/issues/259)

- **Status**: Open
- **Opened by**: Zimmi48 on Nov 27, 2022
- **Labels**: enhancement
- **Description**: @coqbot already supports the "Re-run" button for an individual failed check in the GitHub Checks tab (see the left-hand side in the screenshot below) but it doesn't support the new "re-run all checks" and "re-run failed checks" features (they lead to a `check_suite.rerequested` event, see example JS...

## [#248 - Ideas for reducing the CI runner load](https://github.com/rocq-prover/bot/issues/248)

- **Status**: Open
- **Opened by**: jfehrle on Oct 18, 2022
- **Labels**: enhancement
- **Description**: - Suppose the PR has a failure only in ci_fiat_crypto and you make a fix to address that. It would be helpful to be able to rerun only that job and its prerequisites--or just the jobs that failed in the last full run, or a specific named group of jobs and their prerequisites. Of course, you would la...

## [#234 - Make coqbot workaround the GitHub→GitLab 30' lag for syncing the main branches?](https://github.com/rocq-prover/bot/issues/234)

- **Status**: Open
- **Opened by**: erikmd on Jun 27, 2022
- **Labels**: enhancement
- **Description**: ### Context    I remember discussing this issue a long time ago with @Zimmi48 and it seemed "minor" (and/or not essential for maintaining coq/coq), but it actually appeared blocking to me since this comment: https://github.com/coq-community/docker-coq/pull/50#issuecomment-1165749733    Cc @Alizter @...

## [#231 - Ability to mark ci jobs as broken](https://github.com/rocq-prover/bot/issues/231)

- **Status**: Open
- **Opened by**: Alizter on Jun 17, 2022
- **Labels**: enhancement
- **Description**: Following discussion in https://github.com/coq/coq/pull/16154#issuecomment-1159102457 we should implement the ability to mark CI jobs as broken and get coqbot to create a pull request allowing it to fail, and once that is merged a pull request renabling it. This should reduce some mechanical work.

## [#227 - coqbot should store as much info as possible in the logs when something unexpected has happened](https://github.com/rocq-prover/bot/issues/227)

- **Status**: Open
- **Opened by**: Zimmi48 on May 11, 2022
- **Labels**: enhancement
- **Description**: For instance, in the [case that has happened today](https://github.com/coq/coq/pull/16005#issuecomment-1123649999), the logs didn't contain any information but they could have printed the result of the GitHub query and which comment ID coqbot was looking for...

## [#225 - coqbot not reporting all needed conditions for merge of PR](https://github.com/rocq-prover/bot/issues/225)

- **Status**: Open
- **Opened by**: Alizter on Apr 01, 2022
- **Labels**: enhancement, good first issue
- **Description**: It seems having a requested change in the PR doesn't get reported with the other conditions for merging. Have a look at this example:    https://github.com/coq/coq/pull/15852#issuecomment-1085495043

## [#223 - Replace result with Result.t](https://github.com/rocq-prover/bot/issues/223)

- **Status**: Open
- **Opened by**: Alizter on Mar 29, 2022
- **Labels**: enhancement
- **Description**: It looks like `result` is being deprecated in favour of `Result.t`. We should clean those up at some point (mostly in mlis).

## [#221 - @coqbot bench should allow customizing the list of packages to bench](https://github.com/rocq-prover/bot/issues/221)

- **Status**: Open
- **Opened by**: JasonGross on Mar 28, 2022
- **Labels**: enhancement
- **Description**: See https://github.com/coq/coq/pull/15860#issuecomment-1080460118 for motivation.  I want to be able to write @coqbot bench coq-fiat-crypto-with-bedrock.  Ideally coqbot would let me know immediately if any of the packages don't exist in the relevant opam setup, but this is not mandatory.

## [#220 - Coqbot should report the reason it suggests not minimizing targets when we request that it minimize them anyway, at least when there are no suggested targets.](https://github.com/rocq-prover/bot/issues/220)

- **Status**: Open
- **Opened by**: JasonGross on Mar 25, 2022
- **Labels**: enhancement, bug minimizer
- **Description**: The issue is that ci-analysis also fails on master.  Coqbot should report the reason it suggests not minimizing targets when we request that it minimize them anyway, at least when there are no suggested targets.    _Originally posted by @JasonGross in https://github.com/coq/coq/issues/15807#issuecom...

## [#216 - Minimizer should report less when continuing minimization](https://github.com/rocq-prover/bot/issues/216)

- **Status**: Open
- **Opened by**: Alizter on Mar 22, 2022
- **Labels**: enhancement, bug minimizer
- **Description**: See https://github.com/coq/coq/pull/15693#issuecomment-1075206602    The minimizer should not spam the almost minimized files and instead wait for the user to ask about the status. We could have `@coqbot check minimization` or something to get a partially minimized file.    cc @JasonGross

## [#209 - Reminder if merge now failed](https://github.com/rocq-prover/bot/issues/209)

- **Status**: Open
- **Opened by**: Alizter on Mar 15, 2022
- **Labels**: enhancement
- **Description**: It would be nice if coqbot could remind the person tagging coqbot that their merge failed a few days after coqbot tells them why it failed. Here is an example: https://github.com/coq/coq/pull/15549    Something like 3 days after the attempt, coqbot should tag the user and remind them that the PR is ...

## [#201 - Deploy as an opam package](https://github.com/rocq-prover/bot/issues/201)

- **Status**: Open
- **Opened by**: mattiasdrp on Feb 11, 2022
- **Labels**: enhancement
- **Description**: Would it make sense to deploy at least bot-components as an opam package?

## [#197 - enable minimization resumption for non-ci minimization jobs](https://github.com/rocq-prover/bot/issues/197)

- **Status**: Open
- **Opened by**: JasonGross on Feb 07, 2022
- **Labels**: enhancement, bug minimizer
- **Description**: Example: https://github.com/coq-community/run-coq-bug-minimizer/issues/2#issuecomment-1031448739    Currently we only resume on ci minimization jobs.

## [#190 - Should coqbot compose the comment rather than run-coq-bug-minimizer?](https://github.com/rocq-prover/bot/issues/190)

- **Status**: Open
- **Opened by**: JasonGross on Dec 17, 2021
- **Labels**: enhancement, bug minimizer
- **Description**: It may make sense to have coqbot compose the reply comment instead (this would also allow coqbot to do the truncation of the file only for the comment and not also for the submitted PR).  If we move the curl response to happen after artifact upload, then the bot could perhaps link directly to the ar...

## [#187 - coqbot could add `suggested: rebase` when it might fix CI failures](https://github.com/rocq-prover/bot/issues/187)

- **Status**: Open
- **Opened by**: JasonGross on Dec 14, 2021
- **Labels**: enhancement
- **Description**: When checks finish at the head of a branch in Coq, all open PR with a CI failure on ci-XXX which also fails on the CI of the base commit, but not on the new head could get a `suggested: rebase` label (probably not a comment, because that would flood people with multiple open PRs) (and the checks sta...

## [#186 - Port coq bug minimizer bash script to GraphQL API for commits](https://github.com/rocq-prover/bot/issues/186)

- **Status**: Open
- **Opened by**: JasonGross on Dec 14, 2021
- **Labels**: enhancement, good first issue, bug minimizer
- **Description**: No description

## [#185 - coqbot should allow minimization from the checks tab](https://github.com/rocq-prover/bot/issues/185)

- **Status**: Open
- **Opened by**: JasonGross on Dec 14, 2021
- **Labels**: enhancement, bug minimizer
- **Description**: In addition to posting comments, and perhaps even when the test-suite fails, each failure in the checks tab ([example](https://github.com/coq/coq/pull/15341/checks?check_run_id=4494061751)) could get a line that says something like "🚀 Click [here](#) to minimize this failure".  The overall checks/pi...

## [#184 - coqbot should track uses of the minimizer itself](https://github.com/rocq-prover/bot/issues/184)

- **Status**: Open
- **Opened by**: JasonGross on Dec 14, 2021
- **Labels**: enhancement, bug minimizer
- **Description**: Probably by commenting on https://github.com/coq-community/run-coq-bug-minimizer/issues/8, or editing a comment there, with some log of uses.    Possible data that's useful to track:  - PRs that minimization was possible on  - PRs that minimization was triggered on  - Duration, time, trigerrer, and ...

## [#183 - What version of Coq should non-CI minimization default to?](https://github.com/rocq-prover/bot/issues/183)

- **Status**: Open
- **Opened by**: JasonGross on Nov 30, 2021
- **Labels**: enhancement, bug minimizer
- **Description**: Currently it's "latest released", and there's no good way to configure it when invoking the minimizer.  Perhaps it should be configurable?

## [#182 - [wish] Minimize outdated posts](https://github.com/rocq-prover/bot/issues/182)

- **Status**: Open
- **Opened by**: gares on Nov 30, 2021
- **Labels**: enhancement, bug minimizer
- **Description**: Take https://github.com/coq/coq/pull/15220 , I got spammed a lot. In order to make the thread "readable" I did *manually* mark all old coqbot messages as outdated. IMO the bot could do that automatically when posting a new message.

## [#180 - Should coqbot respond with a 😕 on comments which mention (tag?) @coqbot but don't trigger any action?](https://github.com/rocq-prover/bot/issues/180)

- **Status**: Open
- **Opened by**: JasonGross on Nov 14, 2021
- **Labels**: enhancement, question
- **Description**: This might make it easier to know whether the issue is a problem with coqbot seeing the comment at all vs coqbot parsing the comment.  (This idea came up after `@dependabot` thumbs-uped my request to rebase.)

## [#178 - coqbot should be able to do traditional delta debugging on a PR](https://github.com/rocq-prover/bot/issues/178)

- **Status**: Open
- **Opened by**: JasonGross on Nov 10, 2021
- **Labels**: enhancement, bug minimizer
- **Description**: I was reading up on delta debugging and found that one use case is to find the minimal set of changes in a diff that can be removed to restore working behavior.  When we get a fast enough file (such as https://github.com/coq/coq/pull/14727#issuecomment-957082119 , perhaps in conjunction with #177), ...

## [#170 - CI minimization responses should allow easy creation of PRs augmenting the test-suite](https://github.com/rocq-prover/bot/issues/170)

- **Status**: Open
- **Opened by**: JasonGross on Oct 19, 2021
- **Labels**: enhancement, bug minimizer
- **Description**: It might be nice to have a link in the "here's the minimized file" comment for "Click here to have coqbot create a PR against this PR branch adding this file to the test-suite.". This link could target an endpoint of coqbot with a GET request containing a link to the workflow run, the CI target, and...

## [#169 - Report on deprecation warnings raised by projects tested in Coq CI.](https://github.com/rocq-prover/bot/issues/169)

- **Status**: Open
- **Opened by**: Zimmi48 on Oct 13, 2021
- **Labels**: enhancement, question
- **Description**: Following the discussion in today's call: https://github.com/coq/coq/wiki/Coq-Call-2021-10-13    We could report the list of warnings in the GitLab trace in the GitHub Checks tab (with a neutral status if only warnings were raised---though this would make allowed failures less visible, so maybe that...

## [#162 - Improve auto-minimization resumption (and give option to stop it).](https://github.com/rocq-prover/bot/issues/162)

- **Status**: Open
- **Opened by**: Zimmi48 on Sep 03, 2021
- **Labels**: enhancement, bug minimizer
- **Description**: 1. instead of posting a new comment for each new run, coqbot could edit the previous comment  2. coqbot could give an option to the user to stop the minimization.

## [#160 - Disable GitLab synchronization by default.](https://github.com/rocq-prover/bot/issues/160)

- **Status**: Open
- **Opened by**: Zimmi48 on Aug 29, 2021
- **Labels**: enhancement
- **Description**: We should check that the GitLab repository exists *before* pulling anything from the GitHub repository.

## [#156 - Update to latest versions of libraries.](https://github.com/rocq-prover/bot/issues/156)

- **Status**: Open
- **Opened by**: Zimmi48 on Jul 19, 2021
- **Labels**: enhancement
- **Description**: - [x] Toml 6.0  - [x] #135   - [x] graphql-ppx 1.2.0 (requires `-native` command-line argument and API changes to take into account).  - [ ] Update `ocamlformat` and pin its version in `.ocamlformat`.

## [#155 - Bot auto-mute if it's going to post the same message as before.](https://github.com/rocq-prover/bot/issues/155)

- **Status**: Open
- **Opened by**: Zimmi48 on Jul 09, 2021
- **Labels**: enhancement, bug minimizer
- **Description**: The CI minimization feature can generate many coqbot messages (when PRs are frequently updated). Example: https://github.com/coq/coq/pull/13895#issuecomment-850879151    Coqbot should not post a new message if its last message (that we can look for in the last 10 messages only, for instance) is (alm...

## [#154 - coqbot minimization should mention Issue # in commit, not just "Added user script in coqbot.sh"](https://github.com/rocq-prover/bot/issues/154)

- **Status**: Open
- **Opened by**: JasonGross on Jul 08, 2021
- **Labels**: enhancement, good first issue, bug minimizer
- **Description**: Currently when a user initiates (non-ci) minimization, the commit message is just "Added user script in coqbot.sh".  It should be something like "Added user script from coq/coq#<ISSUE #> in coqbot.sh" and should ideally include a link to the particular comment in the body of the commit message.

## [#142 - Use new GraphQL query PullRequest#closingIssuesReferences](https://github.com/rocq-prover/bot/issues/142)

- **Status**: Open
- **Opened by**: Zimmi48 on Jun 02, 2021
- **Labels**: enhancement
- **Description**: See https://github.com/octokit/graphql-schema/releases/tag/v10.44.0

## [#141 - Report which commit hash was tested for external projects in Coq's CI.](https://github.com/rocq-prover/bot/issues/141)

- **Status**: Open
- **Opened by**: Zimmi48 on May 30, 2021
- **Labels**: enhancement
- **Description**: This report should appear in the GitLab pipeline summary so that it is also available for successful jobs.    The CI minimizer mechanism could use this to inform the PR participants whether a job failure is likely to be due to the PR or not.    As a second step, the CI could use this information to ...

## [#138 - coqbot should warn about PRs whose changelog entry number does not match the PR number](https://github.com/rocq-prover/bot/issues/138)

- **Status**: Open
- **Opened by**: JasonGross on May 05, 2021
- **Labels**: enhancement
- **Description**: Sometimes I run `dev/tools/make-changelog` before actually creating the PR, and thus can get the number wrong.  It would be nice if coqbot warned me if the PR number was not linked in any changelog file created by the PR.

## [#131 - enhancement: coqbot merge after CI passes](https://github.com/rocq-prover/bot/issues/131)

- **Status**: Open
- **Opened by**: JasonGross on Mar 16, 2021
- **Labels**: enhancement
- **Description**: It would be nice to be able to write something like `coqbot merge if CI passes` and have the bot wait until the CI passes and only merge if it passes (and tag me if it fails)

## [#130 - coqbot minimize should leave a link to the in-progress minimization](https://github.com/rocq-prover/bot/issues/130)

- **Status**: Open
- **Opened by**: JasonGross on Mar 15, 2021
- **Labels**: enhancement, good first issue, bug minimizer
- **Description**: To run the bug minimizer, coqbot creates a branch named run-coq-bug-minimizer-NNNNN.  It says something like `Hey NAME, the coq bug minimizer is running your script, I'll come back to you with the results once it's done.`  I'd like it to say something like `Hey NAME, the coq bug minimizer is running...

## [#129 - Improvements to the links to the rendered documentation.](https://github.com/rocq-prover/bot/issues/129)

- **Status**: Open
- **Opened by**: Zimmi48 on Feb 10, 2021
- **Labels**: enhancement
- **Description**: It might be nice to have links directly to the rendered doc for each file that is changed by the PR.  This is minor, though, and the current setup is quite good    _Originally posted by @JasonGross in https://github.com/coq/bot/issues/15#issuecomment-776932445_

## [#126 - [wish] nicer icon for coqbot app](https://github.com/rocq-prover/bot/issues/126)

- **Status**: Open
- **Opened by**: gares on Jan 28, 2021
- **Labels**: enhancement
- **Description**: While procrastinating, I told myself this should be the icon https://ilyasergey.net/pnp/ (properly trimmed)

## [#123 - Enable "Post comment when a pull request does not respect certain standards" for other `coqbot` users](https://github.com/rocq-prover/bot/issues/123)

- **Status**: Open
- **Opened by**: myii on Dec 18, 2020
- **Labels**: enhancement
- **Description**: As discussed with @Zimmi48 in Zulip, it would be useful to have this feature available in our repos as well:    * https://github.com/coq/bot#post-comment-when-a-pull-request-does-not-respect-certain-standards    Ideally, it would be helpful to be able to configure the message in `coqbot.toml` but ev...

## [#117 - Update cryptographic signature verification from sha1 to sha256](https://github.com/rocq-prover/bot/issues/117)

- **Status**: Open
- **Opened by**: Zimmi48 on Nov 16, 2020
- **Labels**: enhancement
- **Description**: Cf. https://github.blog/changelog/2020-10-26-webhooks-configuration-enhancements/

## [#115 - stale issue closing / issue triaging](https://github.com/rocq-prover/bot/issues/115)

- **Status**: Open
- **Opened by**: gares on Oct 26, 2020
- **Labels**: enhancement
- **Description**: I've seen other bots around acting on stale issues, first by giving a 1 month long grace period and then closing issues that are inactive for more than 1 year.    Such a policy would need to be discussed with all the devs.

## [#110 - Automated submission of issues from external CI](https://github.com/rocq-prover/bot/issues/110)

- **Status**: Open
- **Opened by**: ejgallego on Sep 23, 2020
- **Labels**: enhancement
- **Description**: In relation to https://github.com/coq/coq/issues/10418 and some discussion on the Coq call, it would be very nice if coq.io could submit an issue automatically to Coq's opam repository when it detects a problem. [Even a PR could be done fixing the issue, or maybe coqbot could propose a fix that coul...

## [#109 - GitHub Checks feedback](https://github.com/rocq-prover/bot/issues/109)

- **Status**: Open
- **Opened by**: Zimmi48 on Sep 21, 2020
- **Labels**: enhancement
- **Description**: Issue to get feedback on the new GitHub Checks reports for GitLab pipelines (requires having installed using the GitHub App method).

## [#108 - Support @coqbot: merge tomorrow and @coqbot: merge when CI passes](https://github.com/rocq-prover/bot/issues/108)

- **Status**: Open
- **Opened by**: Zimmi48 on Sep 17, 2020
- **Labels**: enhancement
- **Description**: In the two cases, @coqbot should immediately answer with a comment (after having checked the usual conditions for merging a PR). Any comment, review or review comment posted afterward should abort the merge. In the case of "merge tomorrow" (or variants), the bot should clearly announce after which p...

## [#106 - Support more variants of calls to coqbot](https://github.com/rocq-prover/bot/issues/106)

- **Status**: Open
- **Opened by**: Zimmi48 on Sep 09, 2020
- **Labels**: enhancement
- **Description**: Some people want to vary the message to use when calling @coqbot, for instance by adding "please". When it's added at the end, it works, but when it's added between the call to @coqbot and the directive, it currently doesn't.  In passing, we could stop ignoring messages to @coqbot that we don't unde...

## [#105 - `@coqbot rebase` feature](https://github.com/rocq-prover/bot/issues/105)

- **Status**: Open
- **Opened by**: CohenCyril on Sep 07, 2020
- **Labels**: enhancement
- **Description**: As discussed [here](https://coq.zulipchat.com/#narrow/stream/237665-math-comp-devs/topic/using.20coqbot.20to.20rebase), it would be awesome if coqbot could rebase a pr on top of master on demand, mostly to solve conflicts the triviality of which github is not aware (through `.gitattributes` merge dr...

## [#95 - Merge with coqbot feedback](https://github.com/rocq-prover/bot/issues/95)

- **Status**: Open
- **Opened by**: Zimmi48 on Aug 25, 2020
- **Labels**: enhancement
- **Description**: Some feedback from coq/coq#12778.    - [ ] The bot does not verify that the PR that it is asked to merge is not already merged nor in draft mode.  - [x] It would be nice if the bot reported all the (or more than one) unmet conditions rather than quitting at the first unmet condition.  - [ ] Comments...

## [#85 - Add generated HTML documentation to gh-pages branch for tags](https://github.com/rocq-prover/bot/issues/85)

- **Status**: Open
- **Opened by**: palmskog on Aug 11, 2020
- **Labels**: enhancement
- **Description**: It's currently a considerably manual chore to generate HTML documentation for Coq project releases and commit it to the `gh-pages` branch of a repo. This is something that coqbot could automate.    As one recent example of how to automate this, we used Jenkins to generate coqdoc for every commit for...

## [#78 - Set up GitLab mirroring of new coq-community projects](https://github.com/rocq-prover/bot/issues/78)

- **Status**: Open
- **Opened by**: palmskog on Jul 27, 2020
- **Labels**: enhancement
- **Description**: In coq-community, projects are continuously pull mirrored on GitLab. However, when a new project is transferred to coq-community, this kind of mirroring must be manually set up.     coqbot could automatically detect that a new repo has been transferred to the coq-community organization and set up mi...

## [#72 - Handling of overlays](https://github.com/rocq-prover/bot/issues/72)

- **Status**: Open
- **Opened by**: ejgallego on Jul 10, 2020
- **Labels**: enhancement
- **Description**: Related to #27 , it would be great if coqbot could perform some action when PRs with overlays are merged. It could either merge this PRs automatically [if approved], or post a reminder to the corresponding PR.    We may need some more structured overlay info for that tho.

## [#61 - Remove surrounding double quotes in result of get_pull_request_refs.](https://github.com/rocq-prover/bot/issues/61)

- **Status**: Open
- **Opened by**: Zimmi48 on Jun 25, 2020
- **Labels**: enhancement
- **Description**: In PR #59, I had to add this trick to make the new feature properly work:    https://github.com/coq/bot/blob/d5e751272a6222efac4ef4cb95b70080c8cbd526/bot.ml#L522-L523    It would be good to remove it by finding the cause for these double quotes and/or stripping them out, but first we should verify t...

## [#52 - Use GraphQL for GitLab API calls too.](https://github.com/rocq-prover/bot/issues/52)

- **Status**: Open
- **Opened by**: Zimmi48 on May 31, 2020
- **Labels**: enhancement
- **Description**: GitLab's GraphQL API has made much progress recently (https://docs.gitlab.com/ee/api/graphql/reference/index.html) although it still doesn't contain all the mutations we need yet. We should explore whether it is possible to use `graphql_ppx` with two different GraphQL schema and start using GraphQL ...

## [#51 - @coqbot could handle a lot of the release management taks.](https://github.com/rocq-prover/bot/issues/51)

- **Status**: Open
- **Opened by**: Zimmi48 on May 15, 2020
- **Labels**: enhancement
- **Description**: The current release process includes creating a release issue (cf. coq/coq#8445, coq/coq#8446, coq/coq#10843, coq/coq#11157, coq/coq#12334). These issues were created by copy-pasting the content of `dev/doc/release-process.md`.    Some steps in this checklist could be entirely automated. It would be...

## [#48 - Management of several open backport projects could be improved.](https://github.com/rocq-prover/bot/issues/48)

- **Status**: Open
- **Opened by**: Zimmi48 on Mar 03, 2020
- **Labels**: enhancement
- **Description**: What to do when multiple stable branches are actively maintained? What rules to enforce? How to combine this with the use of a single milestone per PR.    In the case of PR https://github.com/coq/coq/pull/11197, backporting occurred to v8.10 but it was rejected from v8.11. It shouldn't have been the...

## [#42 - coqbot could skim through PRs without assginees and request reviewers to choose an assignee](https://github.com/rocq-prover/bot/issues/42)

- **Status**: Open
- **Opened by**: Zimmi48 on Oct 23, 2019
- **Labels**: enhancement
- **Description**: It could do this for PRs that have been marked as ready-for-review for more than 5 days.

## [#38 - Add/Remove pull request from backporting project when its milestone is updated.](https://github.com/rocq-prover/bot/issues/38)

- **Status**: Open
- **Opened by**: Zimmi48 on Aug 28, 2019
- **Labels**: enhancement
- **Description**: In particular, when a milestone is removed, the issue event with action "demilestoned" provides the previous milestone as a top-level value, and the new milestone as a child of the issue value (however, it doesn't contain the info whether the pull request has been merged).

## [#35 - Configuration file](https://github.com/rocq-prover/bot/issues/35)

- **Status**: Open
- **Opened by**: Zimmi48 on Jul 19, 2019
- **Labels**: enhancement
- **Description**: Should be useful for a number of things:    - set the correspondence between GitLab repo and GitHub repo;  - activate various features beyond the synchronization feature;  - set a team of trusted contributors (see #33)  - etc.

## [#31 - Monitor newly created branches and tags and immediately delete non-conforming ones.](https://github.com/rocq-prover/bot/issues/31)

- **Status**: Open
- **Opened by**: Zimmi48 on Jan 19, 2019
- **Labels**: enhancement
- **Description**: Typically, we only want to allow branches in the form `vX.X` and `master` and signed tags in the form `VX.X.X`, `VX.X+alpha`, `VX.X+betaX`.

## [#18 - mergify/bors-like merging process](https://github.com/rocq-prover/bot/issues/18)

- **Status**: Open
- **Opened by**: Zimmi48 on Aug 01, 2018
- **Labels**: enhancement
- **Description**: This would ensure better reliability because CI would run on commits before they become the `HEAD` of `master` or a stable branch.    This would however require first a (planned) change to the way overlays are handled (cf. coq/coq#6724).    There is also a question on how to allow the `pkg:nix` job ...

## [#11 - Feature request: Support for `needs: bisect`](https://github.com/rocq-prover/bot/issues/11)

- **Status**: Open
- **Opened by**: JasonGross on Jul 16, 2018
- **Labels**: enhancement
- **Description**: It would be really nice to be able to have something like the following interaction mode:  1. I add a label `needs: bisect` to a PR  2. coqbot comments with a template, removes `needs: bisect`, adds `needs: bisect-info`  3. I either edit the template from coqbot's comment, or duplicate it into a new...

## [#4 - @coqbot can help with backporting](https://github.com/rocq-prover/bot/issues/4)

- **Status**: Open
- **Opened by**: Zimmi48 on May 21, 2018
- **Labels**: enhancement
- **Description**: - [x] When a PR is merged, if the milestone description contained "coqbot: backport to BRANCH (request inclusion column: URL1 / backported column: URL2)", and the PR was not targeting BRANCH already, then put the PR in the project column URL1.  - [x] When new commits are pushed, if their message is ...

---
*Last updated: 2025-10-17 14:46:15*
*Source: [rocq-prover/bot Issues](https://github.com/rocq-prover/bot/issues)*
