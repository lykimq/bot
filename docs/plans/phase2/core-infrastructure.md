# :control_knobs: Core Infrastructure

> Create dynamic feature dispatch and simplify core bot logic

**Related Issues**: #223 (Replace Result with Result.t), #248 (Ideas for reducing CI runner load), #52 (Use GraphQL for GitLab API calls)

## :building_construction: Current Problem

Core bot files are overloaded with mixed responsibilities:
- **`bot.ml` (664 lines)**: Webhook routing + hardcoded business logic
- **`actions.ml` (3223 lines)**: All features mixed together
- **`helpers.ml` (185 lines)**: Mixed utilities
- **No Dynamic Dispatch**: Features hardcoded in routing logic

## :gear: Solution Components

### Create Feature Registry (`src/feature_registry.ml`)
**Purpose**: Dynamic feature dispatch based on configuration
**Features**:
- Register features by name
- Route webhook events to appropriate features
- Enable/disable features per repository
- Feature dependency management

### Simplify Bot Logic (`src/bot.ml`)
**Purpose**: Pure webhook routing without business logic
**Changes**:
- Remove hardcoded repository patterns
- Remove feature-specific logic
- Focus only on webhook parsing and routing
- Target: <200 lines (from 664 lines)

### Remove Monolithic Files
**Purpose**: Eliminate large, mixed-responsibility files
**Removals**:
- **`src/actions.ml`** - Split into 10 feature modules
- **`src/helpers.ml`** - Split into 4 utility modules
- **`src/actions.mli`** - Split into corresponding `.mli` files
- **`src/helpers.mli`** - Split into corresponding `.mli` files

### Implement Dynamic Dispatch
**Purpose**: Configuration-driven feature routing
**Flow**:
1. Webhook received by `bot.ml`
2. Parse webhook event type and repository
3. Look up enabled features for repository
4. Dispatch to appropriate feature modules
5. Feature modules handle specific logic
