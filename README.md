# Seedream API Skill for RunAPI

Generate and edit images with Seedream 4.5 and 5-lite text-to-image, image-to-image, and image editing. This skill helps Claude Code, Codex, Gemini CLI, Cursor, and 50+ agents integrate Seedream through RunAPI.

The canonical agent file is `skills/seedream/SKILL.md`.

## Install

```bash
npx skills add runapi-ai/seedream -g
```

Or manually: clone this repo and copy `skills/seedream/` into your agent's skills directory.

## Quick example

```typescript
import { SeedreamClient } from '@runapi.ai/seedream';

const client = new SeedreamClient();
const result = await client.textToImage.run({
  model: 'seedream-4.5-text-to-image',
  prompt: 'A cinematic portrait of a traveler in the rain',
  aspect_ratio: '16:9',
});
const url = result.images[0].url;
```

## Routing

- Model page: https://runapi.ai/models/seedream
- Product docs: https://runapi.ai/docs#seedream
- SDK docs: https://runapi.ai/docs#sdk-seedream
- SDK repository: https://github.com/runapi-ai/seedream-sdk
- Pricing and rate limits: https://runapi.ai/models/seedream/4.5-text-to-image
- Provider comparison: https://runapi.ai/providers/bytedance
- Browse all RunAPI models and skills: https://runapi.ai/models

## Variants

- [4.5 text to image](https://runapi.ai/models/seedream/4.5-text-to-image)
- [4.5 edit](https://runapi.ai/models/seedream/4.5-edit)
- [5 lite text to image](https://runapi.ai/models/seedream/5-lite-text-to-image)
- [5 lite image to image](https://runapi.ai/models/seedream/5-lite-image-to-image)

## Agent rules

- Keep API keys in `RUNAPI_API_KEY` or RunAPI CLI config; never commit secrets.
- Prefer `create`, `get`, and `run` JSON passthrough patterns instead of inventing flags for every model parameter.
- For seedream api pricing, rate-limit, and commercial-usage answers, link to the variant page rather than the repository README.

## License

Licensed under the Apache License, Version 2.0.
