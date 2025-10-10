# :art: Architecture Diagrams

**Navigation:** [:house: Home](README.md) | [:arrow_left: Previous: Project Structure](08a-project-structure.md) | [:arrow_up: Code Structure Analysis](08-phase1-code-structure-analysis.md) | [Next: Feature Identification :jigsaw:](08c-feature-identification.md)

---

## 1. Current Architecture (Before Modularization)

This diagram illustrates the monolithic nature of the current bot implementation, where `bot.ml` handles webhook routing and command parsing, and `actions.ml` contains most of the business logic, relying on `helpers.ml` for various utilities.

```mermaid
graph TD
    A[GitHub/GitLab Webhooks] --> B{src/bot.ml};
    B -- Routes Events --> C{src/actions.ml};
    C -- Calls Utilities --> D[src/helpers.ml];
    C -- Interacts with --> E[bot-components/ API Wrappers];
    B -- Parses Commands --> C;
    E -- GitHub/GitLab APIs --> F[External Services];

    subgraph src/
        B;
        C;
        D;
    end

    subgraph bot-components/
        E;
    end

    style B fill:lightgray,stroke:#333,stroke-width:2px,font-weight:bold,color:black
    style C fill:lightgray,stroke:#333,stroke-width:2px,font-weight:bold,color:black
    style D fill:lightgray,stroke:#333,stroke-width:2px,font-weight:bold,color:black
```

---

## 2. Proposed Architecture (After Modularization)

This diagram shows the planned modular architecture, where `bot.ml` is simplified to pure routing, `actions.ml` is broken down into feature-specific modules, `helpers.ml` into focused utility modules, and new foundational components are introduced.

```mermaid
graph TD
    A[GitHub/GitLab Webhooks] --> B{src/bot.ml};
    B -- Routes Events --> C{src/feature_registry.ml};
    C -- Dispatches to --> D[src/features/ Feature Modules];
    D -- Uses Utilities --> E[src/utils/ Utility Modules];
    D -- Interacts with --> F[bot-components/ API Wrappers];
    B -- Uses Config --> G[src/config.ml];
    D -- Uses Config --> G;
    D -- Uses Error Handling --> H[src/errors.ml];
    D -- Uses Logging --> I[src/logger.ml];
    D -- Uses Rate Limiting --> J[src/rate_limiter.ml];
    F -- GitHub/GitLab APIs --> K[External Services];

    subgraph src/
        B;
        C;
        D;
        E;
        G;
        H;
        I;
        J;
    end

    subgraph src/features/
        D;
    end

    subgraph src/utils/
        E;
    end

    subgraph bot-components/
        F;
    end

    style B fill:lightgray,stroke:#333,stroke-width:2px,font-weight:bold,color:black
    style C fill:lightgray,stroke:#333,stroke-width:2px,font-weight:bold,color:black
    style D fill:lightgray,stroke:#333,stroke-width:2px,font-weight:bold,color:black
    style E fill:lightgray,stroke:#333,stroke-width:2px,font-weight:bold,color:black
    style G fill:lightgray,stroke:#333,stroke-width:2px,font-weight:bold,color:black
    style H fill:lightgray,stroke:#333,stroke-width:2px,font-weight:bold,color:black
    style I fill:lightgray,stroke:#333,stroke-width:2px,font-weight:bold,color:black
    style J fill:lightgray,stroke:#333,stroke-width:2px,font-weight:bold,color:black
```

---

**Navigation:** [:house: Home](README.md) | [:arrow_left: Previous: Project Structure](08a-project-structure.md) | [:arrow_up: Code Structure Analysis](08-phase1-code-structure-analysis.md) | [Next: Feature Identification :jigsaw:](08c-feature-identification.md)
