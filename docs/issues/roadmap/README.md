# Bot Issues Roadmap

## Summary

| Category | Count | Status |
|----------|-------|--------|
| **Detailed Roadmaps** | 7 | Ready for implementation |
| **Template Roadmaps** | 111 | Need investigation |
| **Unsolvable Issues** | 3 | Cannot be solved |
| **Total** | **120** | **Complete coverage** |

## Detailed Roadmaps (Ready for Implementation)

| Issue | Priority | Difficulty | Category |
|-------|----------|------------|----------|
| [#339: Status Check Race Condition](detailed/issue-339-status-check-race.md) | :rotating_light: High | :yellow_circle: Medium | Bug Fix |
| [#323: Secret Data Masking](detailed/issue-323-secret-masking.md) | :rotating_light: High | :green_circle: Easy | Security |
| [#334: Duplicate Issue Closures](detailed/issue-334-duplicate-closures.md) | :rotating_light: High | :green_circle: Easy | Bug Fix |
| [#335: stdlib/stdlib-flambda Confusion](detailed/issue-335-stdlib-confusion.md) | :wrench: Medium | :yellow_circle: Medium | Bug Fix |
| [#289: CI Retry Backoff Strategy](detailed/issue-289-ci-retry-backoff.md) | :wrench: Medium | :yellow_circle: Medium | Bug Fix |
| [#327: Skip Full CI After Trivial Rebase](detailed/issue-327-skip-trivial-rebase.md) | :rocket: Low | :yellow_circle: Medium | Enhancement |
| [#325: @rocqbot Response Support](detailed/issue-325-rocqbot-response.md) | :rocket: Low | :green_circle: Easy | Enhancement |

## Template Roadmaps by Category

| Category | Count | Priority Distribution |
|----------|-------|----------------------|
| [Bugs](templates/bugs/) | 19 | 4 High, 15 Medium |
| [Enhancements](templates/enhancements/) | 54 | 4 High, 50 Medium |
| [Bug Minimizer](templates/bug-minimizer/) | 32 | 8 High, 24 Medium |
| [Questions](templates/questions/) | 1 | 1 Low |
| [Unlabeled](templates/unlabeled/) | 5 | 5 Medium |

## Unsolvable Issues

| Issue | Reason |
|-------|--------|
| [#313: CI Duration Display](unsolvable/issue-313-checks-tab-should-list-duration-of-the-preparing-the-custom-executor-or-executing-step_script-stage-of-the-job-script-step-when-the-job-times-out.md) | GitLab API limitations |
| [#342: Hide Bot Command Messages](unsolvable/issue-342-wish-hide-messages-that-are-just-orders-to-the-bot.md) | GitHub API limitations |
| [Unsolvable Issues Summary](unsolvable/unsolvable-issues.md) | Multiple API/architectural constraints |