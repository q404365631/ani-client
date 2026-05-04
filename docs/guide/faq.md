# FAQ

Frequently asked questions about ani-client.

## General

### Do I need an API key?

No. ani-client uses AniList's public GraphQL API, which doesn't require authentication for read operations.

### What runtimes are supported?

- Node.js >= 20
- Bun >= 1.0
- Deno >= 1.28
- Any modern browser with fetch and AbortController

## Usage

### How do I handle rate limits?

The client is pre-configured to stay within AniList's 30 requests/minute limit. It automatically retries with exponential backoff.

### How do I cancel a request?

Use AbortSignal:

const controller = new AbortController();
const scoped = client.withSignal(controller.signal);
setTimeout(() => controller.abort(), 5000);

### How do I use Redis for caching?

import { AniListClient, RedisCache } from "ani-client";
import { createClient } from "redis";
const redis = createClient();
await redis.connect();
const client = new AniListClient({ cacheAdapter: new RedisCache(redis) });

## Errors

### What does AniListError contain?

All API errors throw AniListError with:
- message: Human-readable error
- status: HTTP status code (e.g., 404)
- errors: Raw GraphQL errors array

### I'm getting 429 Too Many Requests

Reduce maxRequests in rate limit config, enable caching, or use batch queries.

## Performance

### How does caching work?

All responses are cached in memory with a 5-minute TTL by default. LRU eviction with 200 entry capacity.

### What is stale-while-revalidate?

When a cached entry expires, the client serves the stale value while fetching fresh data in the background.
