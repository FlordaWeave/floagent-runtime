# Other APIs

This chapter collects the rest of the public author-facing runtime helpers.

## `flo.sleep(ms)`

Pause the script asynchronously:

```ts
await flo.sleep(500);
```

Use it sparingly for pacing, polling, or short waits between retries.

## `flo.time.formatUnixTimestamp(...)`

Format a Unix timestamp using the runtime helper:

```ts
const text = flo.time.formatUnixTimestamp(
  1_700_000_000,
  "YYYY-MM-DD HH:mm:ss",
  "UTC",
);
```

## `flo.task.getContext<T>()`

Read durable task context, including resume payloads:

```ts
const context = await flo.task.getContext<{
  resume_payload?: { batch_id?: string };
  custom?: { value: number };
}>();
```

## `flo.task.limits`

Inspect runtime task-orchestration limits:

```ts
const maxChildren = flo.task.limits.maxSpawnChildren;
```

Use this value to chunk child-task work before calling `spawnChildren(...)`.

## Built-In Tools Through `flo.callTool(...)`

`flo.d.ts` includes typed support for built-ins such as:

- `read_text_file`
- `write_text_file`
- `read_dir`
- `zip`
- `unzip`
- `csv_*`
- `excel_*`
- `media_fetch`
- `media_push_vfs`
- `media_push_base64`
- `send_notification`
- `send_media_attachment`
- `read_skill_resource`
- `import_skill_asset`

When a built-in already matches the file or media operation you need, prefer it over reimplementing the same behavior in script code.

## Skill Resources And Assets

Two built-ins are especially useful from authored skills:

- `read_skill_resource` reads a selected skill resource or imports it into VFS
- `import_skill_asset` copies a selected skill asset into VFS

Both are invoked through `flo.callTool(...)`.

## Final Notes

- Favor JSON-serializable inputs and outputs.
- Keep secrets in the vault, not in manifests or state.
- Use task tool state for resume checkpoints.
- Use the manifest as the contract for what your tool needs.

For exact TypeScript signatures, refer to [`flo.d.ts`](https://github.com/FlordaWeave/floagent/blob/main/flo.d.ts).
