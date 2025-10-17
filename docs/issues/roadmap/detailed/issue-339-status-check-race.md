# Issue #339: Status Check Race Condition

**Priority**: :rotating_light: Critical
**Difficulty**: Easy-Medium

## Problem Description

The bot experiences race conditions when receiving multiple webhooks simultaneously, particularly when requesting access tokens in parallel. This can lead to SSL connection errors and failed status checks.

**Root Cause**: Multiple webhook handlers can request GitHub access tokens simultaneously, potentially invalidating each other.

## Current Implementation Analysis

### Location in Code
- **File**: `src/bot.ml` lines 216-221, 230-233
- **Function**: `action_as_github_app` calls
- **Issue**: No synchronization around token requests

### Current Flow
1. Webhook received → `action_as_github_app` called
2. Multiple webhooks → Multiple parallel token requests
3. Race condition → Token invalidation
4. SSL error → Failed status check

## Proposed Solution

### 1. Token Request Synchronization
```ocaml
(* Add to src/github_installations.ml *)
let token_mutex = Lwt_mutex.create ()

let get_installation_token_safe ~bot_info ~key ~app_id ~owner =
  Lwt_mutex.with_lock token_mutex (fun () ->
    get_installation_token ~bot_info ~key ~app_id ~owner
  )
```

### 2. Retry Mechanism with Exponential Backoff
```ocaml
let rec retry_with_backoff ~max_retries ~base_delay fn =
  fn () >>= function
  | Ok result -> Lwt.return result
  | Error e when max_retries > 0 ->
      let delay = base_delay * (2.0 ** (3.0 -. float max_retries)) in
      Lwt_unix.sleep delay >>= fun () ->
      retry_with_backoff ~max_retries:(max_retries - 1) ~base_delay fn
  | Error e -> Lwt.fail e
```

### 3. Webhook Queue System
```ocaml
(* Add to src/bot.ml *)
let webhook_queue = Queue.create ()
let webhook_mutex = Lwt_mutex.create ()

let process_webhook_queue () =
  Lwt_mutex.with_lock webhook_mutex (fun () ->
    if Queue.is_empty webhook_queue then Lwt.return_unit
    else
      let webhook = Queue.dequeue_exn webhook_queue in
      process_single_webhook webhook
  )
```

## Implementation Steps

### Step 1: Add Synchronization
1. Create mutex for token requests
2. Wrap `get_installation_token` calls
3. Test with concurrent webhooks

### Step 2: Add Retry Logic
1. Implement exponential backoff
2. Add retry for failed status checks
3. Add logging for retry attempts

### Step 3: Add Webhook Queue
1. Implement webhook queue system
2. Process webhooks sequentially
3. Add monitoring for queue depth

## Why This Approach?

### :white_check_mark: Advantages
- **Immediate Fix**: Addresses the race condition directly
- **Backward Compatible**: No breaking changes to API
- **Incremental**: Can be implemented step by step
- **Testable**: Easy to test with concurrent requests

### :x: Alternative Approaches Considered
- **Database Locking**: Overkill for this use case
- **Redis Queue**: Adds external dependency
- **Complete Rewrite**: Too risky for critical fix

## Testing Strategy

### Unit Tests
- Test token request synchronization
- Test retry mechanism with various failure scenarios
- Test webhook queue processing

### Integration Tests
- Simulate concurrent webhook requests
- Test with actual GitHub API rate limits
- Monitor for race conditions

### Load Testing
- Send 10+ concurrent webhooks
- Verify no token conflicts
- Measure response times

## Success Criteria

- [ ] No more SSL connection errors from race conditions
- [ ] All status checks succeed under concurrent load
- [ ] Token requests are properly synchronized
- [ ] Retry mechanism handles temporary failures
- [ ] Webhook queue prevents overwhelming the system

## Dependencies

- None (can be implemented immediately)
- May benefit from logging improvements (Issue #323)

## Related Issues

- Issue #323: Secret data masking (for better error logging)
- Issue #289: CI retry backoff (similar retry logic)
