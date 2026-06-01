---
name: seedream
description: Generate and edit images with Seedream through RunAPI. Use when the user asks an agent to create, edit, or transform images with Seedream. Default to the RunAPI CLI for one-off generation; use SDKs only when the user is integrating RunAPI into an app or backend.
documentation: https://runapi.ai/models/seedream.md
provider_page: https://runapi.ai/providers/bytedance.md
catalog: https://runapi.ai/models.md
metadata:
  openclaw:
    homepage: https://runapi.ai/models/seedream
    requires:
      bins:
      - runapi
    install:
    - kind: brew
      formula: runapi-ai/tap/runapi
      bins:
      - runapi
    envVars:
    - name: RUNAPI_API_KEY
      required: false
      description: Optional RunAPI API key; agents should prefer environment auth or saved CLI config. Browser login is interactive fallback only.
---

# Seedream on RunAPI

Generate and edit images with Seedream through RunAPI. The default path for one-off agent tasks is the `runapi` CLI; SDKs are for application integration.

## Routing decision

- One-off generation, editing, or transformation for the user → use the **CLI path** with the `runapi` binary.
- Building an app, backend, worker, library, or production codebase → use the **SDK integration path**.

## CLI path

The `runapi` binary is the runtime dependency. Run `runapi auth status` first. For agents and headless runs, prefer `RUNAPI_API_KEY` or import it into saved config with `printf '%s' "$RUNAPI_API_KEY" | runapi auth import-token --token -`. Use `runapi login` only when the user explicitly wants interactive browser auth.

Inspect the available commands and request fields with CLI help:

```shell
runapi seedream --help
runapi seedream text-to-image --help
runapi seedream edit-image --help
```

Run a one-off task (synchronous — polls until the task completes):

```shell
runapi seedream text-to-image --input-file request.json
runapi seedream edit-image --input-file request.json
```

Submit asynchronously and poll separately:

```shell
runapi seedream text-to-image --async --input-file request.json
runapi seedream edit-image --async --input-file request.json
runapi wait <task-id> --service seedream --action text-to-image
```

Available commands: `text-to-image`, `edit-image`.

Common request shapes:

```json
{
  "model": "seedream-v4-text-to-image",
  "prompt": "A precise product render of a glass teapot on white marble",
  "aspect_ratio": "16:9",
  "output_resolution": "2k",
  "output_count": 3
}
```

```json
{
  "model": "seedream-v4-edit",
  "prompt": "Place the logo on a blue outdoor cap",
  "source_image_urls": ["https://cdn.runapi.ai/public/samples/image.jpg"],
  "aspect_ratio": "1:1",
  "output_resolution": "4k"
}
```

## SDK integration path

When integrating Seedream into an app, backend, worker, or library — not for one-off tasks — use a RunAPI SDK package:

- JavaScript / TypeScript: `@runapi.ai/seedream`
- Ruby: `runapi-seedream`
- Go: `github.com/runapi-ai/seedream-sdk/go`

## References

- Model overview, pricing, and rate limits: https://runapi.ai/models/seedream.md
- Full model catalog: https://runapi.ai/models.md

## Variants

- [4.5 text to image](https://runapi.ai/models/seedream/4.5-text-to-image.md)
- [4.5 edit](https://runapi.ai/models/seedream/4.5-edit.md)
- [5 lite text to image](https://runapi.ai/models/seedream/5-lite-text-to-image.md)
- [5 lite edit](https://runapi.ai/models/seedream/5-lite-edit.md)
- [v4 text to image](https://runapi.ai/models/seedream/v4-text-to-image.md)
- [v4 edit](https://runapi.ai/models/seedream/v4-edit.md)
