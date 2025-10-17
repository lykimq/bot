# Issue #335: stdlib/stdlib-flambda Confusion

**Priority**: :wrench: Medium
**Difficulty**: Easy-Medium

## Problem Description

The bot shows confusing messages when CI targets include both `stdlib` and `stdlib-flambda`, displaying "ci-stdlib, ci-stdlib" instead of distinguishing between the two variants.

## Current Implementation Analysis

### Location in Code
- **File**: `src/actions.ml` (CI target parsing and display)
- **Function**: Likely in CI target extraction and status check creation
- **Issue**: No distinction between stdlib variants

### Current Flow
1. GitLab CI runs with targets: `ci-stdlib, ci-stdlib-flambda`
2. Bot extracts targets and creates status checks
3. Display shows: "ci-stdlib, ci-stdlib" (confusing)

## Proposed Solution

### 1. Improve Target Parsing
```ocaml
(* Add to src/helpers.ml *)
let parse_ci_targets targets_string =
  let targets = String.split ~on:',' targets_string in
  let targets = List.map ~f:String.strip targets in
  let targets = List.dedup_and_sort ~compare:String.compare targets in
  targets

let format_ci_targets targets =
  let format_target = function
    | "ci-stdlib" -> "stdlib"
    | "ci-stdlib-flambda" -> "stdlib-flambda"
    | target -> target
  in
  List.map ~f:format_target targets
  |> String.concat ~sep:", "
```

### 2. Update Status Check Creation
```ocaml
(* Modify src/actions.ml *)
let create_status_check ~targets ~context =
  let formatted_targets = format_ci_targets targets in
  let status_text = f "CI targets: %s" formatted_targets in
  (* Use status_text in status check creation *)
```

### 3. Add Target Validation
```ocaml
let validate_ci_targets targets =
  let valid_targets = [
    "ci-stdlib"; "ci-stdlib-flambda"; "ci-test"; "ci-doc"
  ] in
  List.filter targets ~f:(fun target ->
    List.mem valid_targets target ~equal:String.equal
  )
```

## Implementation Steps

### Step 1: Improve Target Parsing
1. Add `parse_ci_targets` function
2. Add `format_ci_targets` function
3. Test with various target combinations

### Step 2: Update Status Check Logic
1. Find status check creation code
2. Integrate new formatting
3. Test with real CI runs

### Step 3: Add Validation
1. Add target validation
2. Add error handling for invalid targets
3. Update documentation

## Why This Approach?

### :white_check_mark: Advantages
- **Clear Display**: Users can distinguish between variants
- **Backward Compatible**: No breaking changes
- **Extensible**: Easy to add new target types
- **Robust**: Handles edge cases and duplicates

### :x: Alternative Approaches Considered
- **Regex-based Parsing**: Less maintainable
- **Configuration-driven**: Overkill for this use case
- **Complete Rewrite**: Too risky for simple fix

## Testing Strategy

### Unit Tests
```ocaml
let test_parse_ci_targets () =
  let input = "ci-stdlib, ci-stdlib-flambda, ci-test" in
  let expected = ["ci-stdlib"; "ci-stdlib-flambda"; "ci-test"] in
  assert (parse_ci_targets input = expected)

let test_format_ci_targets () =
  let input = ["ci-stdlib"; "ci-stdlib-flambda"] in
  let expected = "stdlib, stdlib-flambda" in
  assert (format_ci_targets input = expected)
```

### Integration Tests
- Test with real GitLab CI output
- Verify status check messages are clear
- Test with various target combinations

## Success Criteria

- [ ] stdlib variants are clearly distinguished
- [ ] Status check messages are readable
- [ ] Duplicate targets are handled properly
- [ ] Invalid targets are filtered out
- [ ] No regression in existing functionality

## Dependencies

- None (can be implemented immediately)

## Related Issues

- Issue #287: Pipeline status display (similar display issues)
- Issue #295: Label management (related to CI status)

## Configuration Options

```toml
[ci]
target_display_names = {
  "ci-stdlib" = "stdlib"
  "ci-stdlib-flambda" = "stdlib-flambda"
  "ci-test" = "test"
  "ci-doc" = "doc"
}
```

## Future Enhancements

- Add support for custom target display names
- Add target grouping (e.g., "stdlib variants")
- Add target status icons (:white_check_mark: :x: :hourglass_flowing_sand:)
