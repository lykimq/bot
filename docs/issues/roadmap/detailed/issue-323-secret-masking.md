# Issue #323: Secret Data Masking in Logging

**Priority**: :rotating_light: Critical
**Difficulty**: Easy

## Problem Description

The bot currently logs sensitive information (tokens, secrets, API keys) in plain text, which poses a security risk. All logging functions should mask secret data before output.

## Current Implementation Analysis

### Location in Code
- **Files**: Throughout `src/` directory
- **Functions**: Various logging calls using `Lwt_io.printl`, `print_endline`
- **Issue**: No secret masking mechanism

### Current Logging Pattern
```ocaml
(* Example from src/bot.ml *)
Lwt_io.printl (f "Error: %s" e)  (* May contain secrets *)
```

## Proposed Solution

### 1. Create Secret Masking Utility
```ocaml
(* Add to src/helpers.ml *)
let mask_secrets text =
  let patterns = [
    (* GitHub tokens *)
    (Str.regexp "ghp_[A-Za-z0-9_]{36}", "ghp_***MASKED***");
    (* GitLab tokens *)
    (Str.regexp "glpat-[A-Za-z0-9_-]{20}", "glpat-***MASKED***");
    (* Installation tokens *)
    (Str.regexp "ghs_[A-Za-z0-9_]{36}", "ghs_***MASKED***");
    (* Webhook secrets *)
    (Str.regexp "webhook_secret=[^&\\s]+", "webhook_secret=***MASKED***");
    (* API keys *)
    (Str.regexp "api_key=[^&\\s]+", "api_key=***MASKED***");
  ] in
  List.fold_left patterns ~init:text ~f:(fun acc (pattern, replacement) ->
    Str.global_replace pattern replacement acc
  )
```

### 2. Create Safe Logging Functions
```ocaml
(* Add to src/helpers.ml *)
let safe_printl text =
  Lwt_io.printl (mask_secrets text)

let safe_print_endline text =
  print_endline (mask_secrets text)

let safe_printf format =
  Printf.ksprintf safe_print_endline format
```

### 3. Replace All Logging Calls
```ocaml
(* Replace throughout codebase *)
- Lwt_io.printl (f "Error: %s" e)
+ Helpers.safe_printl (f "Error: %s" e)

- print_endline error_msg
+ Helpers.safe_print_endline error_msg
```

## Implementation Steps

### Step 1: Create Masking Functions
1. Add `mask_secrets` function to `helpers.ml`
2. Add safe logging functions
3. Test with various secret patterns

### Step 2: Replace Logging Calls
1. Find all logging calls using grep
2. Replace with safe versions
3. Test logging output

### Step 3: Add Configuration
1. Add config option to enable/disable masking
2. Add debug mode for development
3. Update documentation

## Why This Approach?

### :white_check_mark: Advantages
- **Simple Implementation**: Easy to understand and maintain
- **Comprehensive**: Covers all common secret patterns
- **Configurable**: Can be disabled for debugging
- **Non-Breaking**: No API changes required

### :x: Alternative Approaches Considered
- **Structured Logging**: Overkill for current needs
- **External Library**: Adds dependency for simple task
- **Environment Variables**: Less flexible than pattern matching

## Testing Strategy

### Unit Tests
```ocaml
let test_mask_secrets () =
  let input = "token=ghp_abc123def456" in
  let expected = "token=ghp_***MASKED***" in
  assert (mask_secrets input = expected)
```

### Integration Tests
- Test with real webhook payloads
- Verify secrets are masked in logs
- Test with various secret formats

## Dependencies

- May help with Issue #339 (better error logging)

## Related Issues

- Issue #339: Status check race condition (better error visibility)
- Issue #4: Unstructured error handling (improved logging)

## Security Considerations

- **Pattern Coverage**: Ensure all secret types are covered
- **False Positives**: Avoid masking legitimate data
- **Performance**: Minimal impact on logging performance
- **Audit Trail**: Maintain ability to debug issues safely
