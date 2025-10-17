# Issue #325: @rocqbot Response Support

**Priority**: :rocket: Low
**Difficulty**: Easy

## Problem Description

The bot should respond to `@rocqbot` mentions in addition to the current `@coqbot` mentions to provide a more consistent user experience.

## Current Implementation Analysis

### Location in Code
- **File**: `src/bot.ml` (webhook handling)
- **Function**: Bot name pattern matching in webhook handlers
- **Issue**: Only responds to `@coqbot`, not `@rocqbot`

### Current Flow
1. Comment contains `@coqbot` → Bot processes command
2. Comment contains `@rocqbot` → Bot ignores (no response)

## Proposed Solution

### 1. Update Bot Name Patterns
```ocaml
(* Modify src/bot.ml *)
let bot_name_patterns = [
  github_bot_name;  (* "coqbot" *)
  "rocqbot";        (* Add rocqbot support *)
  "rocq-bot";       (* Alternative format *)
]

let is_bot_mentioned ~bot_name_patterns comment_body =
  List.exists bot_name_patterns ~f:(fun pattern ->
    String.is_substring comment_body ~substring:("@" ^ pattern)
  )
```

### 2. Update Comment Processing
```ocaml
(* Modify existing comment handlers *)
let process_comment ~comment_body =
  if is_bot_mentioned ~bot_name_patterns comment_body then
    (* Process bot command *)
    handle_bot_command comment_body
  else
    Lwt.return_unit
```

### 3. Add Configuration Support
```toml
[bot]
names = ["coqbot", "rocqbot", "rocq-bot"]
primary_name = "coqbot"
```

## Implementation Steps

### Step 1: Update Pattern Matching
1. Add `rocqbot` to bot name patterns
2. Update comment processing logic
3. Test with both bot names

### Step 2: Add Configuration
1. Add bot names to configuration
2. Update pattern matching to use config
3. Test configuration loading

## Why This Approach?

### :white_check_mark: Advantages
- **Simple**: Minimal code changes required
- **Backward Compatible**: Existing `@coqbot` still works
- **Configurable**: Easy to add more bot names
- **Consistent**: Same behavior for all bot names

### :x: Alternative Approaches Considered
- **Regex-based**: More complex than needed
- **Case-insensitive**: Adds complexity without clear benefit
- **Fuzzy Matching**: Overkill for this simple requirement

## Testing Strategy

### Unit Tests
```ocaml
let test_bot_mention_detection () =
  let patterns = ["coqbot"; "rocqbot"] in
  assert (is_bot_mentioned ~bot_name_patterns:patterns "@coqbot run ci");
  assert (is_bot_mentioned ~bot_name_patterns:patterns "@rocqbot run ci");
  assert (not (is_bot_mentioned ~bot_name_patterns:patterns "@otherbot run ci"))
```

### Integration Tests
- Test with real GitHub comments
- Verify both bot names work
- Test with various comment formats

## Success Criteria

- [ ] Bot responds to `@rocqbot` mentions
- [ ] Bot still responds to `@coqbot` mentions
- [ ] Configuration allows multiple bot names
- [ ] No regression in existing functionality
- [ ] All bot commands work with both names

## Dependencies

- None (can be implemented immediately)

## Related Issues

- Issue #342: Hide bot command messages (related to bot interaction)
- Issue #300: Reaction support (alternative to comments)

## Configuration Options

```toml
[bot]
names = ["coqbot", "rocqbot", "rocq-bot"]
primary_name = "coqbot"
case_sensitive = false
```

## Future Enhancements

- Add support for custom bot names per repository
- Add bot name aliases
- Add bot name validation

## User Experience Impact

- **Positive**: Users can use `@rocqbot` consistently
- **Neutral**: No breaking changes
- **Minimal**: Very small change with big UX improvement
