---
name: seedream
description: Generate and edit images (Seedream 4.5 text-to-image / edit, Seedream 5-lite text/image-to-image) through RunAPI.ai using the @runapi.ai/seedream Node/TypeScript SDK. Use when the user asks to add Seedream image generation, or writes against @runapi.ai/seedream. Triggers on "seedream", "即梦", "image generation", "生成图片", "@runapi.ai/seedream".
documentation: https://runapi.ai/models/seedream
provider_page: https://runapi.ai/providers/bytedance
catalog: https://runapi.ai/models
---
# @runapi.ai/seedream — RunAPI.ai Seedream image generation

Build Node / TypeScript integrations that generate and edit images through RunAPI.ai.

## Setup

Requires **Node 18+** (global `fetch`).

```bash
npm install @runapi.ai/seedream
```

Set your API key in the environment:

```dotenv
# .env
RUNAPI_API_KEY=runapi_xxx   # get one at https://runapi.ai/settings/api_keys
```

```ts
import { SeedreamClient } from '@runapi.ai/seedream';

// The SDK reads RUNAPI_API_KEY from the environment automatically.
const client = new SeedreamClient();
```

Pass `{ apiKey }` explicitly if you manage secrets differently. `baseUrl` defaults to `https://runapi.ai`; override only for local development.

## Core recipe — text to image

```ts
const result = await client.textToImage.run({
  model: 'seedream-4.5-text-to-image',
  prompt: 'A cinematic portrait of a traveler in the rain',
  aspect_ratio: '16:9',
  quality: 'basic',
});

const url = result.images[0].url;
```

`run()` creates the task, auto-polls, and resolves only when the task completes — `images[0].url` is guaranteed on the resolved value. On failure it throws `TaskFailedError`; on polling timeout it throws `TaskTimeoutError`. Use `run()` for scripts and short-lived processes. For request handlers, split it:

```ts
const { id } = await client.textToImage.create({
  model: 'seedream-4.5-text-to-image',
  prompt: '...',
  aspect_ratio: '1:1',
  quality: 'basic',
});
// return 202 immediately; fetch later:
const status = await client.textToImage.get(id);
if (status.status === 'completed') { /* ... */ }
```

Do not hold a web worker open waiting on `run()`. Split + webhook is the production pattern.

`run()` polls every 2 s for up to 15 min by default. Tune when needed:

```ts
await client.textToImage.run(params, { maxWaitMs: 5 * 60_000, pollIntervalMs: 2_000 });
```

If `TaskTimeoutError` fires, the task is still running server-side — resume with `textToImage.get(id)` or finish via webhook.

## Edit and image-to-image

Provide one or more `image_urls` with the matching model:

```ts
// 4.5 edit — in-place editImage guided by a prompt
await client.textToImage.run({
  model: 'seedream-4.5-edit',
  prompt: 'Change the background to a sunset beach',
  image_urls: ['https://cdn.example.com/source.jpg'],
  aspect_ratio: '16:9',
  quality: 'high',
});

// 5-lite image-to-image — style/content transfer from a reference
await client.textToImage.run({
  model: 'seedream-5-lite-image-to-image',
  prompt: 'In the style of a traditional ink painting',
  image_urls: ['https://cdn.example.com/portrait.jpg'],
  aspect_ratio: '3:4',
  quality: 'basic',
});
```

## Models

| `model` | Use case |
|---|---|
| `seedream-4.5-text-to-image` | High-quality text-to-image. |
| `seedream-4.5-edit` | Prompt-guided editImage on one or more source images. |
| `seedream-5-lite-text-to-image` | Faster, cheaper text-to-image. |
| `seedream-5-lite-image-to-image` | Faster image-to-image transfer. |

`aspect_ratio` values: `1:1`, `4:3`, `3:4`, `16:9`, `9:16`, `2:3`, `3:2`, `21:9`. `quality`: `basic` or `high`.

Exact credit costs per model are shown at https://runapi.ai/pricing and in the dashboard — do not hardcode prices in application code.

## Callbacks (webhooks)

Pass `callback_url` on `create()` (or any `run()` call) and RunAPI will POST the final payload to you:

```ts
await client.textToImage.create({
  model: 'seedream-4.5-text-to-image',
  prompt: '...',
  aspect_ratio: '1:1',
  quality: 'basic',
  callback_url: 'https://your.app/webhooks/runapi/seedream',
});
```

Payload shape:

```ts
{ id: string; status: 'completed' | 'failed'; images?: { url: string }[]; error?: string }
```

**Always verify the signature before trusting the body.** RunAPI signs every callback with your account's Callback Secret (rotate at `/accounts/callback_secret`). Headers:

- `X-Callback-Id` — UUID, store to make handler idempotent
- `X-Callback-Timestamp` — unix seconds, reject if `|now - ts| > 300`
- `X-Callback-Signature` — base64 HMAC-SHA256 over `` `${id}.${ts}.${rawBody}` `` using the base64-decoded secret

```ts
import crypto from 'node:crypto';

function verify(raw: string, id: string, ts: string, sig: string, secret: string) {
  const key = Buffer.from(secret, 'base64');
  const mac = crypto.createHmac('sha256', key)
    .update(`${id}.${ts}.${raw}`)
    .digest('base64');
  return crypto.timingSafeEqual(Buffer.from(mac), Buffer.from(sig));
}
```

Reply `2xx` within 10s; any non-2xx triggers retries.

## Errors

All errors are re-exported from `@runapi.ai/core`. Always `instanceof` — never string-match messages.

| Error | Status | Action |
|---|---|---|
| `AuthenticationError` | 401 | abort; surface "reconnect your API key" |
| `InsufficientCreditsError` | 402 | prompt user to top up at runapi.ai/billing |
| `ValidationError` | 400 / 422 | fix params; do not retry |
| `RateLimitError` | 429 | sleep `err.retryAfterMs`, then retry |
| `ServiceUnavailableError` | 503 / 455 | retry with backoff; transient service issue |
| `TaskFailedError` | — | show `err.details` to user; do not auto-retry |
| `TaskTimeoutError` | — | re-poll with `textToImage.get(id)` |

```ts
import { InsufficientCreditsError, TaskFailedError } from '@runapi.ai/seedream';

try {
  await client.textToImage.run({
    model: 'seedream-4.5-text-to-image',
    prompt: '...',
    aspect_ratio: '1:1',
    quality: 'basic',
  });
} catch (err) {
  if (err instanceof InsufficientCreditsError) { /* surface top-up CTA */ }
  else if (err instanceof TaskFailedError)       { /* show err.details */ }
  else throw err;
}
```

## Gotchas

- `model`, `prompt`, `aspect_ratio`, and `quality` are all required. `aspect_ratio` is not optional even for square output.
- Edit and image-to-image models require `image_urls` (an array, even for a single image).
- `nsfw_checker` only applies to `seedream-5-lite-*` models.
- `callback_url` must be reachable from the public internet. `localhost` / `127.0.0.1` URLs will never fire — use a tunnel (cloudflared, ngrok, tailscale funnel) when developing locally.

## Dig deeper

Package README (full API surface, all params): `node_modules/@runapi.ai/seedream/README.md`. Types: `@runapi.ai/seedream/dist/types.d.ts`. Product docs: https://runapi.ai/docs.

## RunAPI public routing

seedream api public links use the API-379 catalog route map. The main seedream api page is https://runapi.ai/models/seedream. SDK docs live at https://runapi.ai/docs#sdk-seedream and product docs live at https://runapi.ai/docs#seedream.

Pricing, rate limits, and commercial usage for seedream api should point to the most specific variant page:
- [4.5 text to image](https://runapi.ai/models/seedream/4.5-text-to-image)
- [4.5 edit](https://runapi.ai/models/seedream/4.5-edit)
- [5 lite text to image](https://runapi.ai/models/seedream/5-lite-text-to-image)
- [5 lite image to image](https://runapi.ai/models/seedream/5-lite-image-to-image)

Compare Seedream with other Bytedance models at https://runapi.ai/providers/bytedance. Browse every RunAPI model and skill at https://runapi.ai/models. SDK repository: https://github.com/runapi-ai/seedream-sdk. Skill repository: https://github.com/runapi-ai/seedream.
