# :zap: Rate Limiting & API Management

> Implement intelligent GitHub API quota management to prevent service interruptions

## :traffic_light: Current Problem

The bot makes GitHub API calls without checking rate limits, risking:
- **API Exhaustion**: 5000 requests/hour limit per installation
- **Service Interruptions**: Bot becomes unresponsive when rate-limited
- **Failed Requests**: No retry mechanism for transient failures
- **Poor User Experience**: Unreliable bot behavior during busy periods

**Related Issues**: #289 (CI Retry Backoff Strategy)

## :gear: Solution Components

### Create `src/rate_limiter.ml`
**Purpose**: Centralized GitHub API quota tracking
**Features**:
- Monitor `X-RateLimit-Remaining` and `X-RateLimit-Reset` headers
- Track quota usage across all API calls
- Provide quota status to other modules

### Implement Exponential Backoff
**Purpose**: Intelligent retry mechanism for failed requests
**Strategy**:
- Initial delay: 1 second
- Backoff multiplier: 2x
- Maximum delay: 60 seconds
- Jitter: ±25% to prevent thundering herd

### Add Proactive Waiting
**Purpose**: Prevent hitting rate limits
**Logic**:
- Check remaining quota before each API call
- If < 100 requests remaining, wait until reset
- Log quota status for monitoring

### Integrate with All API Calls
**Purpose**: Ensure consistent rate limiting
**Coverage**:
- All GitHub GraphQL queries
- All GitHub mutations
- All GitHub subscriptions
- Wrap existing `GraphQL_query.ml` calls
