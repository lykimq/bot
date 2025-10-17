# Issue #327: Skip Full CI After Trivial Rebase

**Priority**: :rocket: Medium
**Difficulty**: Medium

## Problem Description

The bot should not add "needs: full ci" after trivial rebases (e.g., force-pushes that only change commit hashes without changing the actual code). This would improve the developer experience by allowing silent rebasing.

## Current Implementation Analysis

### Location in Code
- **File**: `src/actions.ml` (PR update handling)
- **Function**: `pull_request_updated_action` and related CI triggering logic
- **Issue**: No detection of trivial vs. non-trivial changes

### Current Flow
1. PR updated (force-push) → Webhook received
2. Bot triggers CI → Adds "needs: full ci" label
3. Even trivial rebases trigger full CI

## Proposed Solution

### 1. Implement Trivial Change Detection
```ocaml
(* Add to src/git_utils.ml *)
let is_trivial_rebase ~old_commit ~new_commit =
  (* Check if only commit metadata changed *)
  let old_tree = get_commit_tree old_commit in
  let new_tree = get_commit_tree new_commit in
  String.equal old_tree new_tree

let detect_rebase_type ~owner ~repo ~old_sha ~new_sha =
  Git_utils.fetch_commit ~owner ~repo ~sha:old_sha >>= fun old_commit ->
  Git_utils.fetch_commit ~owner ~repo ~sha:new_sha >>= fun new_commit ->
  Lwt.return (is_trivial_rebase ~old_commit ~new_commit)
```

### 2. Add Configuration for Trivial Rebase Detection
```toml
[ci]
detect_trivial_rebases = true
skip_full_ci_on_trivial_rebase = true
trivial_rebase_confidence_threshold = 0.95
```

### 3. Update PR Update Handler
```ocaml
(* Modify src/actions.ml *)
let pull_request_updated_action ~bot_info ~action ~gitlab_mapping ~github_mapping =
  match action with
  | ForcePushed {old_sha; new_sha} ->
      detect_rebase_type ~old_sha ~new_sha >>= fun is_trivial ->
      if is_trivial then
        Lwt_io.printl "Trivial rebase detected - skipping full CI"
      else
        (* Trigger full CI as usual *)
        trigger_full_ci ~bot_info
  | _ -> (* Handle other actions normally *)
```

### 4. Add Heuristic Detection
```ocaml
let is_likely_trivial_rebase ~old_commit ~new_commit =
  let old_message = get_commit_message old_commit in
  let new_message = get_commit_message new_commit in
  let old_author = get_commit_author old_commit in
  let new_author = get_commit_author new_commit in

  (* Check if only commit hash changed *)
  String.equal old_message new_message &&
  String.equal old_author new_author &&
  (* Add more heuristics as needed *)
  true
```

## Implementation Steps

### Step 1: Implement Git Analysis
1. Add commit comparison functions
2. Implement trivial rebase detection
3. Add configuration options

### Step 2: Update PR Handler
1. Integrate detection into PR update handler
2. Add logging for rebase decisions
3. Test with various rebase scenarios

### Step 3: Add Fallback Logic
1. Add confidence scoring
2. Add manual override options
3. Add monitoring and metrics

## Why This Approach?

### :white_check_mark: Advantages
- **User Experience**: Reduces CI noise for developers
- **Configurable**: Can be disabled if needed
- **Safe**: Has fallback to full CI if uncertain
- **Extensible**: Can add more sophisticated detection

### :x: Alternative Approaches Considered
- **GitHub API Only**: Limited to commit metadata
- **File Content Analysis**: Too expensive for large repos
- **Machine Learning**: Overkill for this use case

## Testing Strategy

### Unit Tests
```ocaml
let test_trivial_rebase_detection () =
  let old_commit = create_test_commit ~message:"fix: bug" ~tree:"abc123" in
  let new_commit = create_test_commit ~message:"fix: bug" ~tree:"abc123" in
  assert (is_trivial_rebase ~old_commit ~new_commit)
```

### Integration Tests
- Test with real GitHub PRs
- Test various rebase scenarios
- Verify CI behavior changes

## Success Criteria

- [ ] Trivial rebases are detected correctly
- [ ] Full CI is skipped for trivial rebases
- [ ] Non-trivial changes still trigger full CI
- [ ] Configuration allows fine-tuning
- [ ] Fallback works when detection is uncertain

## Dependencies

- Git utilities (already available)
- May benefit from Issue #323 (better logging)

## Related Issues

- Issue #304: Auto-schedule CI on tag (related CI automation)
- Issue #295: Label management (CI label handling)

## Configuration Options

```toml
[ci]
detect_trivial_rebases = true
skip_full_ci_on_trivial_rebase = true
trivial_rebase_confidence_threshold = 0.95
enable_manual_override = true
log_rebase_decisions = true
```

## Monitoring

- Track trivial rebase detection accuracy
- Monitor CI skip rate
- Alert on false positives/negatives

## Future Enhancements

- Add support for different rebase types
- Add user feedback mechanism
- Add machine learning for better detection
