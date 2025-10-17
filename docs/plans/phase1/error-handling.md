# :wrench: Error Handling & Logging

> Implement structured error handling and comprehensive logging for better debugging

## :x: Current Problem

The bot suffers from poor error handling:
- **Untyped Errors**: Generic strings and `failwith` exceptions
- **Silent Failures**: `Lwt.async` hides internal failures
- **Poor Debuggability**: No structured context in errors
- **No Recovery**: Can't distinguish recoverable vs fatal errors

**Related Issues**: #264 (Better error messages), #280 (Log verbosity), #227 (Store info in logs), #275 (Print warnings from GraphQL)

## :gear: Solution Components

### Create `src/errors.ml`
**Purpose**: Structured error types with context
**Error Variants**:
```ocaml
type bot_error =
  | APIError of { service: string; endpoint: string; code: int; message: string }
  | GitError of { command: string; exit_code: int; stderr: string }
  | ConfigError of { field: string; message: string }
  | WebhookError of { event: string; message: string }
  | RateLimitError of { retry_after: float }
```

### Create `src/logger.ml`
**Purpose**: Comprehensive logging system
**Features**:
- Structured logging with context
- Multiple log levels (Debug, Info, Warn, Error)
- Service-specific logging
- Secret masking integration

### Replace Generic Error Handling
**Purpose**: Use typed errors throughout codebase
**Changes**:
- Replace `failwith` with typed errors
- Replace `Result.t string` with `Result.t bot_error`
- Add error context to all operations

### Add Structured Context
**Purpose**: Rich error information for debugging
**Context Fields**:
- Service and endpoint information
- HTTP status codes
- Git command output
- Webhook event details
- User and repository context
