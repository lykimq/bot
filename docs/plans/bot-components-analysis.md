# Bot Components Migration & OCaml 5 Migration Analysis

## Part 1: What Can Move to `bot-components` After Restructure

After completing the Phase 2 restructure (as outlined in `milestone-brief.md`), the `src/` directory will be modularized. The next step is to identify what code should move to `bot-components/` to focus on bot modularization and make it easier for users to create new bots.

### Current State Analysis

**Current `bot-components/` structure:**
- `Bot_info.ml` - Bot configuration types
- `GitHub_*.ml` - GitHub API interactions (GraphQL queries, mutations, subscriptions)
- `GitLab_*.ml` - GitLab API interactions (GraphQL queries, mutations, subscriptions)
- `GraphQL_query.ml` - Low-level GraphQL client
- `Utils.ml` - General utilities
- `GitHub_app.ml` - GitHub App authentication

**Current `src/` structure (before restructure):**
- `bot.ml` - Main webhook handler (664 lines)
- `actions.ml` - All feature implementations (3223 lines)
- `helpers.ml` - Utility functions (185 lines)
- `config.ml` - TOML configuration parsing
- `git_utils.ml` - Git operations (221 lines)
- `github_installations.ml` - Installation token management (80 lines)

**After Phase 2 restructure (`src/` will have):**
- `bot.ml` - Pure webhook routing (<200 lines)
- `feature_registry.ml` - Dynamic feature dispatch
- `features/*` - 10 feature modules (ci_reporting, doc_urls, benchmarks, etc.)
- `utils/*` - 4 utility modules (string_utils, branch_utils, comment_utils, http_utils)
- `errors.ml` - Structured error types
- `logger.ml` - Logging infrastructure
- `rate_limiter.ml` - API rate limiting
- `config.ml` - Extended TOML configuration
- `git_utils.ml` - Git operations (kept as-is)
- `github_installations.ml` - Installation token management (kept as-is)

### What Should Move to `bot-components/`

The goal is to move all **bot framework code** (not specific bot implementation) to `bot-components/`, making it easy for users to create new bots by:
1. Using the framework components
2. Implementing only their specific features
3. Configuring which features to enable

#### 1. Core Bot Infrastructure (High Priority)

**Move these to `bot-components/`:**

##### `src/git_utils.ml` → `bot-components/Git_utils.ml`
**Rationale:**
- Git operations are generic bot functionality
- Used across multiple features
- No bot-specific logic, just git command wrappers
- Makes it easy for new bots to interact with git repositories

**What it provides:**
- `execute_cmd` - Execute shell commands with error handling
- `git_fetch`, `git_push`, `git_delete` - Git operation wrappers
- `git_make_ancestor` - Merge commit creation
- Repository initialization and management

**Dependencies:**
- Uses `Bot_components.Bot_info.t` (already in bot-components)
- Uses `Lwt` (consistent with other bot-components)
- Uses `Helpers.string_match` - this function could move to `bot-components/Utils.ml`

##### `src/github_installations.ml` → `bot-components/GitHub_installations.ml`
**Rationale:**
- GitHub App installation token management is framework functionality
- Needed by any bot using GitHub Apps
- Already uses `Bot_components` types
- Generic pattern that any GitHub App bot needs

**What it provides:**
- Installation token caching with expiration
- Token refresh logic
- Action execution with installation tokens

**Note:** Already tightly integrated with `Bot_components.GitHub_app`, natural fit.

##### `src/utils/http_utils.ml` (from `helpers.ml`) → `bot-components/Http_utils.ml`
**Rationale:**
- HTTP download utilities are generic
- Not specific to coqbot functionality
- Useful for any bot that needs to download files
- Already uses `Lwt` and `Cohttp` (consistent with bot-components)

**What it provides:**
- `download_cps`, `download`, `download_to` - HTTP download functions
- `copy_stream` - Stream copying utilities
- Redirect handling

##### `src/utils/branch_utils.ml` (from `helpers.ml`) → `bot-components/Branch_utils.ml`
**Rationale:**
- Branch name parsing and manipulation is generic
- `pr_from_branch` is a common pattern for many bots
- No coqbot-specific logic

**What it provides:**
- `pr_from_branch` - Parse PR number from branch name
- Branch name utilities

#### 2. Configuration System (Medium Priority)

##### `src/config.ml` → Partially move to `bot-components/Config.ml`
**Rationale:**
- Configuration parsing framework should be in bot-components
- Bot-specific configuration (repository mappings, features) should stay in `src/`
- Split into generic TOML utilities vs. bot-specific config

**Proposed Structure:**
- `bot-components/Config.ml`:
  - Generic TOML parsing utilities
  - Server configuration (port, domain)
  - Bot identity configuration (name, email)
  - GitHub/GitLab instance configuration parsing
  - Secret loading from environment or config
- `src/config.ml` (simplified):
  - Repository mappings
  - Feature configuration
  - Bot-specific settings

#### 3. Error Handling & Logging (Medium Priority)

##### `src/errors.ml` → `bot-components/Error.ml`
**Rationale:**
- Structured error types are framework infrastructure
- Any bot needs error handling
- Can be extended by bot-specific error types

**What it provides:**
- Base error types (`APIError`, `GitError`, `ConfigError`, `WebhookError`, `RateLimitError`)
- Error context and structured information
- Framework for bot-specific error extensions

**Note:** Bot-specific error types can still be defined in `src/` and extend the base types.

##### `src/logger.ml` → `bot-components/Logger.ml`
**Rationale:**
- Logging infrastructure is framework functionality
- Structured logging with context is useful for any bot
- Secret masking is a security best practice

**What it provides:**
- Log levels (Debug, Info, Warn, Error)
- Structured logging with key-value fields
- Secret masking/redaction
- Log context management

#### 4. Rate Limiting (Medium Priority)

##### `src/rate_limiter.ml` → `bot-components/Rate_limiter.ml`
**Rationale:**
- API rate limiting is a general concern for any bot
- GitHub API rate limits apply to all bots
- Can be extended for other services (GitLab, etc.)

**What it provides:**
- Rate limit tracking
- Exponential backoff
- Proactive rate limit management
- Header parsing (`X-RateLimit-Remaining`, `X-RateLimit-Reset`)

#### 5. Webhook Routing Framework (Low Priority - Future Consideration)

##### Core routing patterns → `bot-components/Webhook_router.ml` (new)
**Rationale:**
- After `bot.ml` is simplified to routing only, the routing patterns could be extracted
- Many bots follow similar webhook routing patterns
- Could provide a framework for dynamic routing

**What it could provide:**
- Webhook event parsing
- Route registration
- Event-to-handler dispatch
- Validation utilities

**Note:** This is more advanced and could be considered after the initial migration.

### What Should Stay in `src/`

#### Bot-Specific Features (`src/features/*`)
- All feature modules (ci_reporting, doc_urls, benchmarks, ci_minimizer, bug_minimizer, etc.)
- These are specific to coqbot's functionality
- Users creating new bots would implement their own features

#### Bot-Specific Utilities (`src/utils/*`)
- `string_utils.ml` - If it contains coqbot-specific string manipulation
- `comment_utils.ml` - If it contains coqbot-specific comment formatting

#### Bot-Specific Configuration
- Repository mappings specific to coqbot
- Feature configuration specific to coqbot's features

#### Main Bot Application
- `bot.ml` - The main entry point for coqbot
- `feature_registry.ml` - Bot-specific feature registration

### Migration Strategy

**Phase 1: Direct Moves (Low Risk)**
1. Move `git_utils.ml` → `bot-components/Git_utils.ml`
2. Move `github_installations.ml` → `bot-components/GitHub_installations.ml`
3. Extract HTTP utilities from `helpers.ml` → `bot-components/Http_utils.ml`
4. Extract branch utilities → `bot-components/Branch_utils.ml`

**Phase 2: Split & Move (Medium Risk)**
1. Split `config.ml` into generic (bot-components) and specific (src)
2. Move `errors.ml` → `bot-components/Error.ml`
3. Move `logger.ml` → `bot-components/Logger.ml`
4. Move `rate_limiter.ml` → `bot-components/Rate_limiter.ml`

**Phase 3: Framework Extraction (Future)**
1. Extract webhook routing patterns
2. Create plugin system for features

### Benefits of This Migration

**For Users Creating New Bots:**
1. All framework code available in `bot-components/`
2. Clear separation between framework and bot-specific code
3. Reusable components (git, HTTP, logging, errors, rate limiting)
4. Consistent patterns and API design
5. Easy to extend with new features

**For Maintenance:**
1. Framework code separated from bot-specific code
2. Easier to test framework components independently
3. Versioning of framework separate from bot implementation
4. Clearer documentation boundaries

### Dependencies to Consider

**Potential Circular Dependencies:**
- `git_utils.ml` uses `Bot_components.Bot_info.t` (already satisfied)
- `github_installations.ml` uses `Bot_components.GitHub_app` (already satisfied)
- Features in `src/features/*` will use moved utilities - need to update paths
- `bot.ml` will use both `bot-components/` and `src/features/*`

**Library Dependencies:**
- All moved code uses `Lwt` - consistent with existing `bot-components/`
- All moved code uses `Cohttp` - already in `bot-components` dependencies
- Git utilities use Unix process functions - standard library

---

## Part 2: OCaml 5 Migration Analysis (LWT → Effects)

### Current State

**LWT Usage in the Codebase:**
- Used extensively throughout `src/` and `bot-components/`
- Primary concurrency mechanism for:
  - HTTP requests (Cohttp_lwt)
  - Git operations (Lwt_process, Lwt_io)
  - GraphQL queries (async operations)
  - Webhook handling (async callbacks)
  - All I/O operations

**Key LWT Patterns:**
- Monadic bind (`>>=`, `>|=`)
- Syntax extensions (`let*`, `let+`)
- `Lwt.async` for fire-and-forget operations
- `Lwt_main.run` as the entry point
- `Lwt_io` for I/O operations
- `Lwt_process` for process execution
- `Lwt_pool` for resource pooling

### OCaml 5 Effects Overview

OCaml 5 introduces **effect handlers** which enable direct-style concurrency without monads. The main alternative to LWT in OCaml 5 is **Eio** (Effects-based I/O).

**Key Concepts:**
- **Effects**: Non-local control flow without monads
- **Direct-style**: No monadic bind operators needed
- **Eio**: Standard library for effect-based I/O
- **Fiber-based concurrency**: Similar to async/await in other languages

### Migration Complexity Analysis

#### High Complexity Areas

**1. Core I/O Operations (High Complexity)**
- **Current**: `Lwt_io`, `Cohttp_lwt`, `Lwt_process`
- **Migration**: Replace with `Eio` equivalents (`Eio.Std.Flow`, `Eio.Net`, `Eio.Process`)
- **Complexity**: High - requires rewriting all I/O operations
- **Effort**: ~2-4 weeks of focused work
- **Risk**: Medium-High - I/O is critical and widespread

**2. HTTP Client/Server (High Complexity)**
- **Current**: `Cohttp_lwt_unix` for HTTP server and client
- **Migration**: 
  - Option A: Use `Eio_http` (if/when available) or raw `Eio.Net`
  - Option B: Wrap LWT HTTP in Eio effects (transitional approach)
- **Complexity**: Very High - HTTP is core to bot functionality
- **Effort**: ~3-6 weeks
- **Risk**: High - webhook handling is critical path

**3. Process Execution (Medium-High Complexity)**
- **Current**: `Lwt_process` for git commands and scripts
- **Migration**: `Eio.Process` (different API, needs adaptation)
- **Complexity**: Medium-High
- **Effort**: ~1-2 weeks
- **Risk**: Medium - git operations are critical

**4. GraphQL Client (Medium Complexity)**
- **Current**: Uses `Cohttp_lwt` for GraphQL queries
- **Migration**: Replace HTTP layer with Eio
- **Complexity**: Medium
- **Effort**: ~1 week (depends on HTTP migration)

#### Medium Complexity Areas

**5. Async Operations (Medium Complexity)**
- **Current**: `Lwt.async` for fire-and-forget
- **Migration**: Eio fibers with `Fiber.fork` or `Fiber.first`
- **Complexity**: Medium
- **Effort**: ~3-5 days
- **Risk**: Low-Medium

**6. Resource Pooling (Medium Complexity)**
- **Current**: `Lwt_pool` for connection pooling
- **Migration**: Eio pools or custom implementation
- **Complexity**: Medium
- **Effort**: ~2-3 days
- **Risk**: Medium - affects performance

#### Low Complexity Areas

**7. Configuration & Parsing (Low Complexity)**
- **Current**: `config.ml` - mostly synchronous
- **Migration**: Minimal changes, if any
- **Complexity**: Low
- **Effort**: <1 day
- **Risk**: Low

**8. Error Handling (Low Complexity)**
- **Current**: `errors.ml` - synchronous error types
- **Migration**: Works with both LWT and Eio
- **Complexity**: Low
- **Effort**: Minimal
- **Risk**: Low

### Advantages of Migrating to OCaml 5 + Eio

#### 1. Performance Benefits
- **Reduced Overhead**: Effect handlers are more efficient than monadic bind chains
- **Better Scheduling**: Eio's scheduler can be more efficient than LWT's
- **Lower Memory**: Direct-style code can have lower allocation overhead
- **Real-world Impact**: Potentially 10-30% improvement in concurrent operations

#### 2. Code Clarity
- **Direct Style**: No more `>>=`, `>|=`, or monadic syntax
- **Readability**: Code reads more like synchronous code
- **Stack Traces**: Better stack traces (no monad transformer stacks)
- **Example Transformation**:
  ```ocaml
  (* LWT *)
  let* body = Client.get uri >>= fun (_, body) -> Body.to_string body in
  process body
  
  (* Eio *)
  let body = Eio_http.Client.get uri |> Body.to_string in
  process body
  ```

#### 3. Debugging
- **Simpler Stack Traces**: Effects don't obscure the call stack
- **Easier Testing**: Direct-style code is easier to unit test
- **Better Tooling**: OCaml 5 tooling works better with direct-style code

#### 4. Future-Proofing
- **OCaml 5 is the Future**: LWT may see less active development
- **Ecosystem Support**: New libraries target Eio/effects
- **Community Momentum**: Industry moving toward effects (e.g., Ocsigen project migrating)

#### 5. Type System Benefits
- **No Monad Transformers**: Simpler type signatures
- **Polymorphism**: Easier to abstract over effectful operations

### Disadvantages & Challenges

#### 1. Migration Effort (High)
- **Code Changes**: Nearly every file needs modification
- **Time Investment**: 3-6 months of development time
- **Testing**: Extensive testing required to ensure no regressions
- **Risk**: High during migration period

#### 2. Library Ecosystem (Medium-High Risk)
- **Eio Maturity**: Eio is newer than LWT, may have gaps
- **Library Support**: Not all libraries have Eio versions
- **Cohttp**: May need to find alternatives or write wrappers
- **Dependencies**: Some dependencies may not support Eio yet

#### 3. Learning Curve (Medium)
- **Team Knowledge**: Team needs to learn Eio and effects
- **Pattern Changes**: Different concurrency patterns
- **Documentation**: Less documentation/examples than LWT

#### 4. Potential Compatibility Issues
- **Gradual Migration**: Can't easily migrate piece-by-piece
- **Testing**: Need comprehensive test suite before migration
- **Deployment**: May need to run both versions during transition

#### 5. Third-Party Dependencies
- **bot-components**: Also uses LWT - would need migration
- **GraphQL**: GraphQL client libraries may need updates
- **Cohttp**: Standard HTTP library uses LWT

### Migration Strategy

#### Option 1: Full Migration (Recommended for Long-term)

**Phases:**
1. **Preparation** (1-2 weeks)
   - Set up OCaml 5 development environment
   - Create comprehensive test suite
   - Document all async operations

2. **Core Infrastructure** (2-3 weeks)
   - Migrate `errors.ml`, `logger.ml` (if they use LWT)
   - Migrate `git_utils.ml` to Eio
   - Migrate HTTP utilities

3. **HTTP Layer** (3-4 weeks)
   - Migrate HTTP client/server to Eio
   - Test webhook handling thoroughly
   - Migrate GraphQL client

4. **Features** (2-3 weeks)
   - Migrate feature modules one by one
   - Test each feature independently

5. **Integration & Testing** (2-3 weeks)
   - End-to-end testing
   - Performance testing
   - Documentation updates

**Total Time**: 10-15 weeks (2.5-4 months)

#### Option 2: Gradual Migration (Hybrid Approach)

**Strategy**: Run LWT and Eio side-by-side, migrate incrementally

**Phases:**
1. Keep LWT for HTTP server (core webhook handling)
2. Use Eio for new features
3. Migrate utilities gradually
4. Eventually migrate HTTP layer

**Advantages:**
- Lower risk
- Can migrate one module at a time
- Easier to rollback

**Disadvantages:**
- More complexity (two concurrency systems)
- Longer overall migration time

#### Option 3: Defer Migration (Pragmatic)

**Wait for:**
- Better Eio ecosystem maturity
- More migration examples from other projects
- Cohttp/Eio alternatives to mature
- Clearer migration tooling

**Advantages:**
- Lower immediate risk
- Benefit from others' experiences
- Better tooling available

**Disadvantages:**
- Technical debt continues to grow
- Missing performance benefits
- May need to migrate eventually anyway

### Recommendation

**For This Project:**

**Short-term (Next 6 months)**: **Defer the migration**

**Rationale:**
1. The bot is actively used and stable - migration risk is high
2. Focus should be on modularization (Phase 2) first
3. Eio ecosystem is still maturing
4. LWT is battle-tested and works well
5. Migration can come after modularization is complete

**Medium-term (6-12 months)**: **Consider gradual migration**

**Conditions:**
1. After Phase 2 modularization is complete
2. If Eio ecosystem has matured (cohttp alternatives available)
3. If performance becomes a bottleneck
4. If maintenance burden of LWT becomes high

**Long-term (12+ months)**: **Plan for eventual migration**

**Planning:**
1. Design new code with migration in mind (clear async boundaries)
2. Abstract over concurrency where possible
3. Keep migration as a future roadmap item
4. Monitor Eio ecosystem development

### Alternative: Wait for Migration Tools

The web search results indicate that tools are being developed to automate LWT → Eio migration:
- **Ocsigen project**: Developing automated migration tools
- **NLnet Foundation**: Funding migration tooling development

**Recommendation**: Monitor these tools and consider migration when automated tooling becomes available (estimated 6-12 months).

### Conclusion

**For bot-components migration**: Focus on moving framework code (git_utils, github_installations, HTTP utilities, error handling, logging, rate limiting) to make it easy for users to create new bots.

**For OCaml 5 migration**: Defer for now, but plan for it. The migration is complex and high-risk. Better to complete modularization first, then evaluate migration when:
1. The codebase is more modular (easier to migrate piece-by-piece)
2. Eio ecosystem is more mature
3. Automated migration tools are available
4. There's a clear performance or maintenance benefit

