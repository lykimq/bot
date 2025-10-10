# :zap: Phase 0.1: Critical Fixes

**Navigation:** [:house: Home](README.md) | [:arrow_left: Previous: Phase 0 Overview](03-phase0-overview.md) | [Next: Configuration Refactoring :arrow_right:](05-phase0-conf-refact.md)

---

## Table of Contents
- [Rate Limiting & Backoff](#rate-limiting--backoff)
- [Structured Error Handling](#structured-error-handling)
- [Logging Infrastructure](#logging-infrastructure)

---

## :traffic_light: Rate Limiting & Backoff

### Problem & Impact

The GitHub API imposes a 5000 requests/hour limit per installation for authenticated requests. The bot's current implementation, particularly in `src/bot.ml` and its use of `GraphQL_query.ml` for API interactions, makes direct calls to the GitHub API without a centralized rate-limiting mechanism. This absence of proactive API usage tracking and effective retry mechanisms exposes the bot to significant risks in a real-life application with moderate to high activity. Without proper rate limit management, the bot will continue to send requests until GitHub's limit is reached, leading to:
- **Service Interruptions**: Operations fail abruptly when the rate limit is exceeded.
- **Delayed Responses**: Events are not processed promptly, causing backlogs.
- **Degraded User Experience**: Unreliable bot behavior due to frequent API failures.

### Solution

Create a `src/rate_limiter.ml` module that:
- **Track Quota**: Monitor GitHub API usage by extracting `X-RateLimit-Remaining` and `X-RateLimit-Reset` headers from responses.
- **Intelligent Retries**: Implement exponential backoff (e.g., 1s, 2s, 4s, 8s) to prevent API overload during retries and facilitate recovery from transient errors.
- **Proactive Waiting**: Pause API calls when the remaining quota is low (< 100) to prevent hitting limits, enhancing reliability and user experience by shifting from reactive failures to proactive management.
- **Status Logging**: Log rate limit status for effective monitoring and debugging of API usage.

**Implementation approach**:
```ocaml
type rate_limit_info = {
  remaining: int;
  reset_at: float;
}
(** [check_and_wait info] pauses execution if the GitHub API quota is low,
  based on the provided [info], to prevent hitting rate limits. *)
let check_and_wait : rate_limit_info -> unit Lwt.t

(** [update_from_headers headers] extracts rate limit information
  (remaining requests and reset time) from GitHub API reponse
  [headers] and updates the internal rate limiter state. *)
let update_from_headers : Cohttp.Header.t -> unit
```

### Success Criteria

- :white_check_mark: Zero rate limit violations in production
- :white_check_mark: All GitHub API calls wrapped with rate limiting
- :white_check_mark: Failed requests retry automatically with backoff
- :white_check_mark: Bot logs rate limit status (remaining/total)

---

## :mag: Structured Error Handling

### Problem

The current bot implementation suffers from several error handling deficiencies across `src/bot.ml`, `src/actions.ml`, and `src/config.ml`:

1.  **Untyped Errors**: Errors are predominantly untyped strings or `failwith` exceptions, leading to unstructured error information and potential program crashes. This includes `Result.t` with `string` errors, direct `failwith` calls, `Option.value_exn`, and `Sys.getenv_exn`.

2.  **Lack of Error Distinction**: There's no clear differentiation between recoverable errors (e.g., transient API timeouts) and fatal errors (e.g., bad configuration). All errors are generally treated as generic strings, hindering appropriate recovery strategies.

3.  **Silent Failures**: Extensive use of `Lwt.async` for webhook processing means internal task failures are often logged silently while the external webhook sender receives a `200 OK` response, obscuring actual processing issues.

4.  **Poor Debuggability**: Error messages lack structured context (e.g., request IDs, specific parameters, call stacks), making it difficult to diagnose and pinpoint root causes in production environments.

To address these, a robust error handling strategy is needed, involving custom, typed error types, consistent `Result.t` usage, structured logging, and explicit handling of asynchronous operation outcomes.

### Solution

This solution introduces a `src/errors.ml` module with a `bot_error` type and typed error variants. This addresses untyped errors by providing structured types, distinguishes recoverable (e.g., `APIError`, `RateLimitError`) from fatal (e.g., `ConfigError`) errors, and, when integrated with the logging infrastructure, helps prevent silent failures and improves debugging by providing rich, structured context for each error.

Create a `src/errors.ml` module with typed error variants:

```ocaml
type bot_error =
  | APIError of { service: string; endpoint: string; code: int; message: string }
  | GitError of { command: string; exit_code: int; stderr: string }
  | ConfigError of { field: string; message: string }
  | WebhookError of { event: string; message: string }
  | RateLimitError of { retry_after: float }

type 'a result = ('a, bot_error) Result.t
```

Include structured context in errors:
- Which service/endpoint failed
- HTTP status codes
- Git command output
- Webhook event type

### Success Criteria

- :white_check_mark: All errors are typed with specific variants
- :white_check_mark: Error logs include structured context (service, endpoint, codes)
- :white_check_mark: Minimize use of raw `failwith` or untyped `Error` in main code paths
- :white_check_mark: Easier debugging of production issues with context

---

## :memo: Logging Infrastructure

### Problem

- No structured logging system currently in place
- Debugging production issues is difficult without activity logs
- No visibility into bot operations (what actions were performed, when, why)
- No audit trail for compliance or troubleshooting
- Rate limiting and error handling need logging to be effective

### Solution

Create a `src/logger.ml` module with structured logging:

```ocaml
type log_level = Debug | Info | Warn | Error

type log_context = {
  service: string option;
  endpoint: string option;
  repository: string option;
  pr_number: int option;
  user: string option;
}

let log level message context =
  let timestamp = Unix.time () |> Unix.localtime in
  let context_str = string_of_context context in
  Printf.printf "[%s] %s: %s %s\n"
    (string_of_log_level level)
    (string_of_time timestamp)
    message
    context_str
```

**Logging integration points**:
- Rate limiter logs quota status
- Error handler logs structured errors with context
- Webhook processing logs incoming events
- Feature actions log their operations
- Configuration loading logs startup info

**Log levels**:
- `Debug` - Detailed flow information (webhook parsing, feature dispatch)
- `Info` - Important events (PR processed, CI triggered, features enabled)
- `Warn` - Recoverable issues (rate limit approaching, retry attempts)
- `Error` - Failures (API errors, git failures, config issues)

### Success Criteria

- :white_check_mark: All major operations logged with structured context
- :white_check_mark: Rate limiting status logged hourly
- :white_check_mark: All errors logged with full context
- :white_check_mark: Webhook events logged with repository/PR info
- :white_check_mark: Easy to debug production issues from logs

---

**Navigation:** [:house: Home](README.md) | [:arrow_left: Previous: Phase 0 Overview](03-phase0-overview.md) | [Next: Configuration Refactoring :arrow_right:](05-phase0-conf-refact.md)
