# howlongtobeat API

[![Latest Build Status](https://api.travis-ci.org/ckatzorke/howlongtobeat.svg?branch=master)](https://travis-ci.org/ckatzorke/howlongtobeat)
[![npm version](https://badge.fury.io/js/howlongtobeat.svg)](https://badge.fury.io/js/howlongtobeat)
[![codecov](https://codecov.io/gh/ckatzorke/howlongtobeat/branch/master/graph/badge.svg)](https://codecov.io/gh/ckatzorke/howlongtobeat)

## Version

- Current version: 1.9.0
- Upgrade: 1.8.x -> 1.9.0
- Validation status: 17/17 tests passing (unit + integration)

## About

[HowLongToBeat](https://howlongtobeat.com/) provides game completion-time estimates based on community data.

This package is a lightweight, unofficial API wrapper for:

- game search
- game detail lookup

Please support the original service here: [https://howlongtobeat.com/donate.php](https://howlongtobeat.com/donate.php)

## Install

```bash
npm install howlongtobeat --save
```

## Quick Start

### JavaScript (CommonJS)

```javascript
const { HowLongToBeatService } = require('howlongtobeat');

const hltbService = new HowLongToBeatService();

hltbService.search('Nioh').then((results) => {
  console.log(results);
});
```

### TypeScript

```typescript
import { HowLongToBeatService, HowLongToBeatEntry } from 'howlongtobeat';

const hltbService = new HowLongToBeatService();
const results: HowLongToBeatEntry[] = await hltbService.search('Nioh');
console.log(results);
```

## API

### search(query: string, signal?: AbortSignal)

Returns `Promise<HowLongToBeatEntry[]>`.

```typescript
const results = await hltbService.search('dark souls III');
```

### detail(gameId: string, signal?: AbortSignal)

Returns `Promise<HowLongToBeatEntry>`.

```typescript
const game = await hltbService.detail('2224');
```

`gameId` must be numeric. Non-numeric IDs are rejected early for safety.

### Request cancellation

Both `search()` and `detail()` support `AbortSignal`.

```typescript
const controller = new AbortController();
const pending = hltbService.search('persona 4 golden', controller.signal);
controller.abort();

try {
  await pending;
} catch (error) {
  console.error('Request canceled:', error);
}
```

## Response Model

`HowLongToBeatEntry` fields:

- `id: string`
- `name: string`
- `description: string`
- `platforms: string[]`
- `playableOn: string[]` (deprecated alias of `platforms`, kept for compatibility)
- `imageUrl: string`
- `timeLabels: string[][]`
- `gameplayMain: number`
- `gameplayMainExtra: number`
- `gameplayCompletionist: number`
- `similarity: number`
- `searchTerm: string`

Example result:

```javascript
{
  id: '2224',
  name: 'Dark Souls',
  imageUrl: 'https://howlongtobeat.com/games/darksouls.jpg',
  platforms: ['PC', 'PlayStation 3', 'Xbox 360'],
  timeLabels: [
    ['gameplayMain', 'Main Story'],
    ['gameplayMainExtra', 'Main + Sides'],
    ['gameplayComplete', 'Completionist']
  ],
  gameplayMain: 42,
  gameplayMainExtra: 65,
  gameplayCompletionist: 105,
  similarity: 1,
  searchTerm: 'Dark Souls'
}
```

## Version 1.9.0 Upgrade Notes

The 1.9.0 update aligns the library with the current HowLongToBeat request flow.

- Updated search flow to use `/api/find/init` + `/api/find`
- Added required auth and honeypot headers/fields for search requests
- Added search token caching and automatic one-time token refresh on HTTP 403
- Updated detail URL handling to the current `/game?id=` route
- Added numeric `gameId` validation to prevent URL injection / SSRF-style inputs
- Added `AbortSignal` support to `search()` and `detail()`
- Kept backward compatibility for `playableOn` while exposing `platforms`
- Confirmed compatibility with both old fixture HTML and live HLTB responses

## Testing

```bash
npm test
npm run integrationtest
```

Latest verified status in this update:

- 17/17 passing for the full test suite
- 8/8 passing for integration tests

## Notes

- This is an unofficial wrapper and may break if HowLongToBeat changes site markup or request contracts.
- Search results and durations are community-driven and can change over time.

## License

WTFPL
