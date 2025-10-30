# Bot Refactoring Milestone

Goal: Transform the bot into a modular, testable, and maintainable system (keep all existing functionality).

## Checklist

**Phase 0: Quick Fixes**
- [ ] [#250](https://github.com/coq/bot/issues/250) - "bench native" should be "bench native=on"
- [ ] [#125](https://github.com/coq/bot/issues/125) - JSON parsing failure on PR close webhook

**Phase 1: Foundation Improvements**

a. Error Handling

The bot currently uses untyped errors (`failwith` exceptions and general strings), with some places using `Lwt.async`. There's no structured context in errors. We should implement structured error types with context. Related issues: [#264](https://github.com/coq/bot/issues/264) (Structured error types), [#223](https://github.com/coq/bot/issues/223) (replace `result` with `Result.t`).

Checklist:
- [ ] `src/errors.ml`
- [ ] All the issues mentioned resolved

b. Logging System

We need a proper logging system with multiple log levels (debug, info, warn, error) and structured logging with context. Related issues: [#227](https://github.com/coq/bot/issues/227) (logs lack context), [#323](https://github.com/coq/bot/issues/323) (secret leak in logs), [#280](https://github.com/coq/bot/issues/280) (git output overflows log sinks), [#275](https://github.com/coq/bot/issues/275) (log GraphQL warning).

Checklist:
- [ ] `src/logger.ml`
- [ ] All the issues mentioned resolved

**Phase 2: Infrastructure & Modularization**

a. Implement new features:
- [ ] Implement the rate limiting
- [ ] Extend TOML configuration
- [ ] Add unit Testing (Alcotest)
- [ ] Deploy opam package, issue [#201](https://github.com/coq/bot/issues/201)

b. Restructure `src` folder before moving things to `bot-components`:
- [ ] `src/actions.ml` -> `src/features`
- [ ] `src/helpers.ml` -> `src/utils/*`
- [ ] Simplify `src/bot.ml`
- [ ] Related issues:
  - [ ] [#193](https://github.com/coq/bot/issues/193) - Doubled target indication
  - [ ] [#194](https://github.com/coq/bot/issues/194) - Target display clarity
  - [ ] [#195](https://github.com/coq/bot/issues/195) - Pluralization in job reporting
  - [ ] [#335](https://github.com/coq/bot/issues/335) - Feature/module extraction

c. Moving to `bot-components`
- [ ] `git_utils.ml/i`
- [ ] `github_installations.ml/i`
- [ ] HTTP utilities in `helpers.ml` (`bot-components/Http_utils.ml/i`)
- [ ] Branch utilities (`bot-components/Branch_utils.ml/i`)
- [ ] Error handling (`bot-components/Error.ml/i`)
- [ ] Logging infrastructure (`bot-components/Logger.ml/i`)
- [ ] Rate limiting (`bot-components/Rate_limiter.ml/i`)
- [ ] Generic parsing config in `config.ml` (keep bot-specific config in `src`)

What will stay in `src`:
- Bot-specific features (`src/features/*`) - coqbot functionality, configuration, utilities, and the main bot application `bot.ml`

d. OCaml 5 Migration

LWT is currently used throughout the codebase for HTTP requests (Cohttp_lwt), Git operations (Lwt_process, Lwt_io), GraphQL queries, webhook handling, and all I/O operations.

OCaml 5 introduces effect handlers that enable direct-style concurrency without monads. The main alternative to LWT is **Eio** (Effect-based I/O), which provides non-local control flow, direct-style code (no monadic bind operators), and fiber-based concurrency similar to async/await.

What would need to be migrated:

- **Core I/O operations**: Replace `Lwt_io`, `Cohttp_lwt`, `Lwt_process` with Eio equivalents: [`Eio.Flow`](https://ocaml.org/p/eio/latest/doc/Eio/Flow/index.html), [`Eio.Net`](https://ocaml.org/p/eio/latest/doc/Eio/Net/index.html), [`Eio.Process`](https://ocaml.org/p/eio/latest/doc/Eio/Process/index.html). This is high complexity and medium-high risk since I/O is critical and widespread.

- **HTTP Client/Server**: Currently using `Cohttp_lwt_unix`. Migration options include switching to Eio-compatible libraries like [Piaf](https://opam.ocaml.org/packages/piaf/) or [cohttp-eio](https://discuss.ocaml.org/t/how-to-send-http-s-request-in-eio/15833), or using raw [`Eio.Net`](https://ocaml.org/p/eio/latest/doc/Eio/Net/index.html)/wrapping LWT in Eio effects. Very high complexity and high risk since HTTP is core and webhook handling is critical.

- **Process Execution**: Currently `Lwt_process` for git commands. Could use [`Eio.Process`](https://ocaml.org/p/eio/latest/doc/Eio/Process/index.html). Medium-high complexity since the API is different, medium risk.

- **GraphQL client**: Currently using `Cohttp_lwt` for HTTP requests. Would migrate to use Eio-compatible HTTP libraries like Piaf or cohttp-eio, similar to the HTTP Client/Server migration above. Medium complexity since it depends on the HTTP layer migration, with the GraphQL query logic itself requiring minimal changes.

- **Async Operations**: Currently using `Lwt.async` for fire-and-forget operations (e.g., background logging, non-blocking label updates). Would migrate to Eio's fiber-based concurrency using [`Fiber.fork`](https://ocaml.org/p/eio/latest/doc/eio/Eio/Fiber/index.html#val-fork) or [`Fiber.first`](https://ocaml.org/p/eio/latest/doc/eio/Eio/Fiber/index.html#val-first) for similar fire-and-forget patterns. Medium complexity since the API is different but conceptually similar.

- **Resource Pooling**: Currently `Lwt_pool`, would replace with [Eio pools](https://ocaml.org/p/eio/latest/doc/Eio/Pool/index.html). Medium complexity since the API is different but conceptually similar.

Advantages:
- Performance: Effect handlers are more efficient than monadic bind chains, better scheduling, lower memory overhead
- Code clarity: Direct style (no `>>=`, `>|=`), more readable, better stack traces
- By migrating, we can contribute to the community's understanding of LWT to OCaml 5 migration patterns, helping other projects learn from our experience

Challenges:
- High migration effort: Nearly every file needs modification, requires extensive testing
- Library ecosystem: Eio is newer with potential gaps, not all libraries have Eio versions yet, Cohttp may need alternatives, less documentation/examples than LWT, learn different concurrency patterns

Migration strategies:

1. **Full migration** (good for long-term)
   - Set up OCaml 5 development environment and create test suite
   - Migrate core infrastructure first: `errors.ml`, `logger.ml` (if they use LWT), `git_utils.ml` to Eio
   - Then HTTP layer: HTTP utilities, then client/server to Eio
   - Test webhook handling throughout migration
   - Migrate GraphQL client after HTTP layer is stable
   - Migrate feature modules one by one, test each independently

2. **Hybrid approach** (lower risk, incremental)
   - Run LWT and Eio side-by-side
   - Keep LWT for HTTP server (core webhook handling) initially
   - Use Eio for new features and utilities
   - Migrate utilities gradually over time
   - Eventually migrate HTTP layer when ready
   - Pros: Lower risk, can migrate one module at a time, easier rollback
   - Cons: More complexity (two concurrency systems), longer overall migration

3. **Wait and see**
   - Wait for better Eio ecosystem maturity
   - Wait for HTTP library alternatives to mature: Eio-compatible HTTP libraries exist ([Piaf](https://opam.ocaml.org/packages/piaf/), [cohttp-eio](https://discuss.ocaml.org/t/how-to-send-http-s-request-in-eio/15833)), but they need to mature in terms of stability, feature completeness, and production readiness
   - Wait for clearer migration tooling and compatibility interfaces: [Ocsigen's migration project](https://discuss.ocaml.org/t/ann-ocsigen-migrating-to-effect-based-concurrency/16327) (funded by [NLnet Foundation](https://nlnet.nl)) is developing automated tools and compatibility interfaces to assist with LWT to Eio transitions
   - Reference community resources like the [ICFP 2023 tutorial on porting LWT to OCaml 5 and Eio](https://icfp23.sigplan.org/details/icfp-2023-tutorials/4/Porting-Lwt-applications-to-OCaml-5-and-Eio) ([GitHub repository](https://github.com/ocaml-multicore/icfp-2023-eio-tutorial))
   - Other projects making this transition include [Irmin](https://discuss.ocaml.org/t/effects-with-lwt-a-dead-end-for-now/15002), [OCaml-GRPC](https://ocaml.org/p/grpc-eio/0.2.0), and [OCaml CI's solver service](https://discuss.ocaml.org/t/eio-digest-1-september-2023/12968), providing more migration examples and patterns
