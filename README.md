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

Generate and edit images with Seedream v4, 4.5, 5 Lite, and 5 Pro text-to-image and image editing. This skill helps Claude Code, Codex, Gemini CLI, Cursor, and 50+ agents integrate Seedream through RunAPI.

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
  model: 'seedream-v4-text-to-image',
  prompt: 'A precise product render of a glass teapot on white marble',
  aspect_ratio: '16:9',
  output_resolution: '2k',
  output_count: 3,
});
const url = result.images[0].url;
```

## Routing

- Model page: https://runapi.ai/models/seedream
- Product docs: https://runapi.ai/docs#seedream
- SDK docs: https://runapi.ai/docs#sdk-seedream
- SDK repository: https://github.com/runapi-ai/seedream-sdk
- Pricing and rate limits: https://runapi.ai/models/seedream/4.5-text-to-image
- Browse all RunAPI models and skills: https://runapi.ai/models

## Variants

- [4.5 text to image](https://runapi.ai/models/seedream/4.5-text-to-image)
- [4.5 edit](https://runapi.ai/models/seedream/4.5-edit)
- [5 lite text to image](https://runapi.ai/models/seedream/5-lite-text-to-image)
- [5 lite edit](https://runapi.ai/models/seedream/5-lite-edit)
- [5 pro text to image](https://runapi.ai/models/seedream/5-pro-text-to-image)
- [5 pro edit](https://runapi.ai/models/seedream/5-pro-edit)
- [v4 text to image](https://runapi.ai/models/seedream/v4-text-to-image)
- [v4 edit](https://runapi.ai/models/seedream/v4-edit)

## Agent rules

- Integration work uses the target language SDK; one-off generation, manual smoke tests, debugging, or user-requested CLI runs use the RunAPI CLI skill: https://github.com/runapi-ai/cli-skill
- RunAPI-generated file URLs are temporary. Download and store generated images, videos, audio, or other files in your own durable storage within 7 days; do not treat returned URLs as long-term assets.
- Keep API keys in `RUNAPI_API_KEY` or RunAPI CLI config; never commit secrets.
- Prefer `create`, `get`, and `run` JSON passthrough patterns instead of inventing flags for every model parameter.
- For pricing, rate-limit, and commercial-usage answers, link to the variant page rather than the repository README.

## License

Licensed under the Apache License, Version 2.0.
