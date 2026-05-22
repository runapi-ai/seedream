<p align="center">
  <a href="https://github.com/runapi-ai/seedream">
    <h3 align="center">Seedream API Skill for RunAPI</h3>
  </a>
</p>

<p align="center">
  Install this agent skill, inspect Seedream fields, then run jobs through the RunAPI CLI.
</p>

<p align="center">
  <a href="https://runapi.ai/models/seedream"><strong>Model Reference</strong></a> · <a href="https://github.com/runapi-ai/cli"><strong>CLI</strong></a> · <a href="https://github.com/runapi-ai/seedream-sdk"><strong>SDK</strong></a>
</p>

<div align="center">

[![skills.sh](https://www.skills.sh/b/runapi-ai/seedream)](https://www.skills.sh/runapi-ai/seedream/seedream)
[![ClawHub](https://img.shields.io/badge/ClawHub-runapi--seedream-111827)](https://clawhub.ai/runapi-ai/runapi-seedream)
[![License](https://img.shields.io/github/license/runapi-ai/seedream)](https://github.com/runapi-ai/seedream/blob/main/LICENSE)

</div>
<br/>

Generate and edit images with Seedream 4.5 and 5-lite text-to-image, image-to-image, and image editing. This skill helps Claude Code, Codex, Gemini CLI, Cursor, and 50+ agents integrate Seedream through RunAPI.

The canonical agent file is `skills/seedream/SKILL.md`.

## Install

```bash
npx skills add runapi-ai/seedream -g
```

Or paste this prompt to your AI agent:

```text
Install the seedream skill for me:

1. Clone https://github.com/runapi-ai/seedream
2. Copy the skills/seedream/ directory into your
   user-level skills directory (e.g. ~/.claude/skills/
   for Claude Code, ~/.codex/skills/ for Codex).
3. Verify that SKILL.md is present.
4. Confirm the install path when done.
```

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
