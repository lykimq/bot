# Issue #334: Duplicate Issue Closures

**Priority**: :rotating_light: Critical
**Difficulty**: Easy

## Problem Description

Issues are sometimes closed multiple times (e.g., by both PR merge and commit merge), triggering the bot's milestone changing procedure multiple times. This creates noise and potential inconsistencies.

## Current Implementation Analysis

### Location in Code
- **File**: `src/bot.ml` lines 252-396 (GitHub webhook handling)
- **Function**: `pull_request_closed_action` and related milestone functions
- **Issue**: No deduplication of webhook events

### Current Flow
1. Issue closed by PR merge → Webhook → Bot processes milestone
2. Issue closed by commit merge → Webhook → Bot processes milestone again
3. Duplicate milestone changes → Confusion

## Proposed Solution

### 1. Webhook Event Deduplication
```ocaml
(* Add to src/helpers.ml *)
module EventCache = struct
  type t = (string * string * string, float) Hashtbl.t  (* owner, repo, issue_id -> timestamp *)
  let cache = Hashtbl.create 1000
  let ttl = 300.0  (* 5 minutes *)

  let is_duplicate ~owner ~repo ~issue_id =
    let key = (owner, repo, issue_id) in
    let now = Unix.time () in
    match Hashtbl.find_opt cache key with
    | Some timestamp when now -. timestamp < ttl -> true
    | _ ->
        Hashtbl.replace cache key now;
        false
end
```

### 2. Add Deduplication to Webhook Handlers
```ocaml
(* Modify src/bot.ml *)
let handle_issue_closed ~owner ~repo ~issue_id action =
  if EventCache.is_duplicate ~owner ~repo ~issue_id then
    Lwt_io.printl (f "Skipping duplicate issue closure: %s/%s#%s" owner repo issue_id)
  else
    (* Process milestone change normally *)
    process_milestone_change ~owner ~repo ~issue_id
```

### 3. Add Logging for Duplicate Detection
```ocaml
let log_duplicate_event ~event_type ~owner ~repo ~issue_id =
  Helpers.safe_printl
    (f "Duplicate %s event detected for %s/%s#%s - skipping"
       event_type owner repo issue_id)
```

## Implementation Steps

### Step 1: Create Event Cache
1. Add `EventCache` module to `helpers.ml`
2. Implement deduplication logic
3. Add TTL-based cleanup

### Step 2: Integrate with Webhook Handlers
1. Add deduplication to issue closure handlers
2. Add logging for duplicate events
3. Test with duplicate webhooks

## Why This Approach?

### :white_check_mark: Advantages
- **Simple**: Easy to understand and implement
- **Efficient**: In-memory cache with TTL
- **Non-Breaking**: No API changes required
- **Configurable**: TTL can be adjusted

### :x: Alternative Approaches Considered
- **Database Storage**: Overkill for this use case
- **GitHub API Checks**: Adds latency and API calls
- **Event ID Tracking**: GitHub doesn't provide unique event IDs

## Testing Strategy

### Unit Tests
```ocaml
let test_duplicate_detection () =
  let cache = EventCache.create () in
  assert (not (EventCache.is_duplicate cache "owner" "repo" "123"));
  assert (EventCache.is_duplicate cache "owner" "repo" "123")
```

### Integration Tests
- Simulate duplicate webhook events
- Verify only first event is processed
- Test TTL expiration

## Success Criteria

- [ ] Duplicate issue closures are detected and skipped
- [ ] First closure event is processed normally
- [ ] Duplicate events are logged for monitoring
- [ ] TTL prevents indefinite cache growth
- [ ] No performance impact on normal operations

## Dependencies

- None (can be implemented immediately)
- May benefit from Issue #323 (better logging)

## Related Issues

- Issue #323: Secret data masking (for logging)
- Issue #4: Unstructured error handling (better error reporting)

## Configuration Options

```toml
[bot]
duplicate_event_ttl = 300  # seconds
enable_duplicate_detection = true
log_duplicate_events = true
```

## Monitoring

- Track duplicate event frequency
- Monitor cache hit/miss ratios
- Alert on high duplicate rates (potential webhook issues)
