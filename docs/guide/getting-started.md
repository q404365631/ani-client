# Getting Started

This guide will walk you through installing and using `ani-client` to build your first AniList-integrated application.

## Installation

```bash
# npm
npm install ani-client

# pnpm
pnpm add ani-client

# yarn
yarn add ani-client
```

## Basic Setup

Create a new file and import the client:

```ts
import { AniListClient, MediaType } from "ani-client";

// Create a client instance (no API key needed for public queries)
const client = new AniListClient();
```

## Your First Query

### Fetch a Single Anime

```ts
const anime = await client.getMedia(1); // Cowboy Bebop
console.log(anime.title.romaji);  // "Cowboy Bebop"
console.log(anime.title.english); // "Cowboy Bebop"
console.log(anime.episodes);      // 26
console.log(anime.averageScore);  // 86
```

### Search for Anime

```ts
const results = await client.searchMedia({
  query: "Attack on Titan",
  type: MediaType.ANIME,
  perPage: 5,
});

for (const anime of results.media) {
  console.log(`${anime.title.romaji} - Score: ${anime.averageScore}`);
}
```

### Get a User's Profile

```ts
const user = await client.getUser("gonzyui");
console.log(user.name);
console.log(user.about);
```

### Get a Character

```ts
const character = await client.getCharacter(40); // Luffy
console.log(character.name.full);
console.log(character.description);
```

## Common Patterns

### Fetching with Relationships

Get an anime with its characters:

```ts
const anime = await client.getMedia(1);
const characters = await client.getMediaCharacters(1, {
  perPage: 10,
  voiceActors: true,
});

for (const edge of characters.edges) {
  console.log(`${edge.node.name.full} - ${edge.role}`);
  if (voiceors) {
   Act for (const va of edge.voiceActors) {
      console.log(`  VA: ${va.name.full}`);
    }
  }
}
```

### Batch Fetching

Fetch multiple anime at once:

```ts
const anime = await client.getMediaBatch([1, 5, 6, 20, 21]);
for (const a of anime) {
  console.log(a.title.romaji);
}
```

### Pagination

Iterate through all results automatically:

```ts
let count = 0;
for await (const anime of client.paginate(
  (page) => client.searchMedia({ query: "Action", type: MediaType.ANIME, page, perPage: 50 }),
  3, // max 3 pages
)) {
  count++;
  console.log(`${count}. ${anime.title.romaji}`);
}
```

## Error Handling

Always wrap API calls in try-catch:

```ts
import { AniListError } from "ani-client";

try {
  const anime = await client.getMedia(999999999);
} catch (e) {
  if (e instanceof AniListError) {
    console.error(`API Error ${e.status}: ${e.message}`);
    // e.errors contains raw GraphQL errors
  } else {
    console.error("Unexpected error:", e);
  }
}
```

## Next Steps

- [Caching Guide](caching.md) - Learn to configure LRU and Redis caching
- [Rate Limiting](rate-limiting.md) - Understand AniList rate limits and custom strategies
- [Batch Queries](batch-queries.md) - Fetch multiple items efficiently
- [Pagination](pagination.md) - Auto-paginate through large result sets
- [Event Hooks](event-hooks.md) - Intercept requests, responses, and cache events
- [Error Handling](error-handling.md) - Detailed error handling patterns
- [Cookbook](cookbook.md) - Common recipes and patterns
- [API Reference](../reference/api.md) - Complete API documentation
