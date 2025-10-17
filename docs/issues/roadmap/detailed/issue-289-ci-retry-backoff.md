# Issue #289: CI Retry Backoff Strategy

**Priority**: :wrench: Medium
**Difficulty**: Medium

## Problem Description

The bot should implement an intelligent backoff strategy for retrying failing CI jobs instead of immediate retries, which can overwhelm the system during outages.

## Current Implementation Analysis

### Location in Code
- **File**: `src/actions.ml` (CI retry logic)
- **Function**: Likely in job failure handling and retry mechanisms
- **Issue**: No backoff strategy, immediate retries

### Current Flow
1. CI job fails → Bot detects failure
2. Bot immediately retries → Can overwhelm system
3. Multiple retries → System overload during outages

## Proposed Solution

### 1. Implement Exponential Backoff
```ocaml
(* Add to src/helpers.ml *)
module RetryStrategy = struct
  type t = {
    max_retries: int;
    base_delay: float;
    max_delay: float;
    backoff_multiplier: float;
  }

  let default = {
    max_retries = 5;
    base_delay = 1.0;  (* 1 second *)
    max_delay = 300.0; (* 5 minutes *)
    backoff_multiplier = 2.0;
  }

  let calculate_delay ~attempt ~strategy =
    let delay = strategy.base_delay *. (strategy.backoff_multiplier ** float (attempt - 1)) in
    min delay strategy.max_delay
end
```

### 2. Add Retry State Management
```ocaml
(* Add to src/helpers.ml *)
module RetryState = struct
  type t = {
    job_id: string;
    attempt: int;
    last_attempt: float;
    next_retry: float;
  }

  let retry_states = Hashtbl.create 1000

  let should_retry ~job_id ~now =
    match Hashtbl.find_opt retry_states job_id with
    | Some state when now >= state.next_retry -> true
    | Some _ -> false
    | None -> true
end
```

### 3. Update CI Retry Logic
```ocaml
(* Modify src/actions.ml *)
let retry_ci_job ~bot_info ~job_info ~retry_strategy =
  let job_id = job_info.build_id in
  let now = Unix.time () in

  if RetryState.should_retry ~job_id ~now then
    let attempt = RetryState.get_attempt job_id in
    if attempt < retry_strategy.max_retries then
      let delay = RetryStrategy.calculate_delay ~attempt ~strategy:retry_strategy in
      Lwt_unix.sleep delay >>= fun () ->
      retry_job_implementation ~bot_info ~job_info
    else
      Lwt_io.printl (f "Max retries exceeded for job %s" job_id)
  else
    Lwt.return_unit
```

## Testing Strategy

### Unit Tests
```ocaml
let test_backoff_calculation () =
  let strategy = RetryStrategy.default in
  let delay1 = RetryStrategy.calculate_delay ~attempt:1 ~strategy in
  let delay2 = RetryStrategy.calculate_delay ~attempt:2 ~strategy in
  assert (delay2 > delay1)
```

### Integration Tests
- Test with simulated CI failures
- Verify backoff behavior
- Test retry state cleanup

## Dependencies

- May benefit from Issue #323 (better logging)

## Related Issues

- Issue #339: Status check race condition (similar retry needs)
- Issue #295: Label management (CI state handling)

## Configuration Options

```toml
[ci]
retry_strategy = {
  max_retries = 5
  base_delay = 1.0
  max_delay = 300.0
  backoff_multiplier = 2.0
}
enable_retry_logging = true
cleanup_old_retry_states = true
```

## Monitoring

- Track retry attempt frequency
- Monitor backoff effectiveness
- Alert on high retry rates
