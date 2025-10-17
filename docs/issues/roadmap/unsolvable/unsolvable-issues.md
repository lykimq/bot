# Unsolvable or Unclear Issues

This document contains issues that are either unsolvable with the current architecture, require external dependencies, or have unclear requirements.

## Issue #313: CI Duration Display

**Priority**: 🚀 Medium
**Difficulty**: Hard/Unclear

### Problem Description
The bot should display the duration of "Preparing the custom executor" or "Executing step_script" stages when jobs timeout, but this information is not readily available from GitLab webhooks.

### Why This Is Difficult
1. **GitLab API Limitation**: Job stage durations are not included in webhook payloads
2. **Timing Information**: Would require polling GitLab API during job execution
3. **Performance Impact**: Additional API calls would slow down webhook processing
4. **Complexity**: Would require significant changes to job monitoring system

### Possible Approaches
1. **Polling Approach**: Poll GitLab API during job execution (performance impact)
2. **GitLab Integration**: Use GitLab's job artifacts API (requires job configuration changes)
3. **External Service**: Use a separate monitoring service (adds complexity)

### Recommendation
- **Defer**: This requires GitLab API changes or significant architectural changes
- **Alternative**: Focus on improving job timeout handling instead
- **Future**: Consider when implementing comprehensive CI monitoring

## Issue #304: Auto-schedule CI on Tag

**Priority**: 🚀 Medium
**Difficulty**: Medium-Hard

### Problem Description
The bot should automatically schedule CI runs when specific tags are set, reducing the need for manual commands.

### Why This Is Complex
1. **Tag Detection**: Need to monitor for tag changes in real-time
2. **CI Triggering**: Need to integrate with GitLab CI scheduling
3. **Configuration**: Need to define which tags trigger which CI types
4. **State Management**: Need to track which tags have been processed

### Possible Approaches
1. **Webhook-based**: Listen for tag creation webhooks (most efficient)
2. **Polling-based**: Periodically check for new tags (simpler but less efficient)
3. **GitLab Integration**: Use GitLab's tag-based CI triggers (requires GitLab config changes)

### Recommendation
- **Implementable**: Can be done with webhook-based approach
- **Priority**: Medium - nice to have but not critical
- **Dependencies**: Requires GitLab webhook configuration

## Issue #300: Reaction Support

**Priority**: 🚀 Medium
**Difficulty**: Medium

### Problem Description
The bot should be able to add reactions (👍, ❌, etc.) instead of just posting comments, especially for minimization results.

### Why This Is Moderate
1. **GitHub API**: Reactions API is available and well-documented
2. **UI Integration**: Need to decide when to use reactions vs. comments
3. **User Experience**: Need to ensure reactions are visible and useful

### Possible Approaches
1. **Simple Reactions**: Add basic reaction support for common actions
2. **Smart Reactions**: Use reactions for status updates, comments for details
3. **Configurable**: Allow users to choose reaction vs. comment behavior

### Recommendation
- **Implementable**: Straightforward GitHub API integration
- **Priority**: Medium - improves user experience
- **Dependencies**: None

## Issue #342: Hide Bot Command Messages

**Priority**: 🚀 Low-Medium
**Difficulty**: Hard

### Problem Description
Messages that are just bot commands (like `@bot run full ci`) should be collapsed or hidden to keep PRs tidy.

### Why This Is Difficult
1. **GitHub API Limitation**: Cannot edit or hide other users' comments
2. **Permission Issues**: Bot cannot modify user comments
3. **UI Limitation**: GitHub doesn't provide API to collapse comments
4. **Workaround Complexity**: Would require complex comment management

### Possible Approaches
1. **Comment Management**: Bot could delete and repost its own comments (risky)
2. **User Education**: Encourage users to delete command comments
3. **Alternative UI**: Use reactions or status checks instead of comments
4. **GitHub Feature Request**: Request GitHub to add comment collapsing API

### Recommendation
- **Not Implementable**: GitHub API limitations prevent this
- **Alternative**: Focus on reducing comment noise through other means
- **Future**: Wait for GitHub API improvements

## Issue #325: @rocqbot Response Support

**Priority**: 🚀 Low
**Difficulty**: Easy-Medium

### Problem Description
The bot should respond to `@rocqbot` mentions in addition to the current `@coqbot` mentions.

### Why This Is Simple
1. **String Matching**: Just need to add another pattern to existing regex
2. **No API Changes**: Uses existing webhook and comment handling
3. **Backward Compatible**: Can support both `@coqbot` and `@rocqbot`

### Possible Approaches
1. **Simple Addition**: Add `@rocqbot` to existing bot name patterns
2. **Alias System**: Create a more flexible alias system
3. **Configuration**: Make bot names configurable

### Recommendation
- **Implementable**: Very straightforward
- **Priority**: Low - minor convenience feature
- **Dependencies**: None

## Summary

### Implementable Issues (3)
- Issue #304: Auto-schedule CI on tag
- Issue #300: Reaction support
- Issue #325: @rocqbot response support

### Difficult/Unclear Issues (2)
- Issue #313: CI duration display (GitLab API limitations)
- Issue #342: Hide bot command messages (GitHub API limitations)

### Recommendations
1. **Focus on implementable issues first**
2. **Consider alternatives for difficult issues**
3. **Document limitations clearly for users**
4. **Monitor for API improvements that might enable difficult features**

---

*Last updated: $(date)*
*Total unsolvable/unclear issues: 5*
