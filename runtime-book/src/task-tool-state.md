# Task Tool State

Use `flo.task.getToolState(...)` and `flo.task.putToolState(...)` for lightweight, tool-partitioned state scoped to the current task.

Unlike `flo.state`, these helpers do not require a manifest state binding.

## Read Current Tool State

```ts
const checkpoint = await flo.task.getToolState<{
  page?: number;
  cursor?: string;
}>({
  key: "sync_checkpoint",
});
```

You can also read another tool's convenience state in the same task by passing `tool_id`.

## Write Current Tool State

```ts
await flo.task.putToolState({
  key: "sync_checkpoint",
  value: {
    page: 2,
    cursor: "abc123",
  },
  ttl_seconds: 3600,
});
```

`putToolState(...)` supports:

- `key`
- `value`
- optional `ttl_seconds`
- optional `if_revision`

It returns the same write-result structure used by `flo.state.put(...)`.

## Best Use Cases

Use tool state for:

- resume checkpoints
- small per-tool task notes
- retry-safe progress markers
- child-batch bookkeeping

This is especially useful before `flo.task.waitForBatch(...)`, because resumed scripts restart from the entrypoint instead of restoring the JavaScript stack.

## Choosing Between `flo.state` And Tool State

Prefer `flo.task.getToolState(...)` / `putToolState(...)` when:

- the data is specific to the current task
- the data belongs to one tool implementation
- you do not need a reusable manifest-declared binding

Prefer `flo.state` when:

- data should outlive the task
- data needs explicit profile, session, task, or shared binding semantics
- multiple scripts should share a named binding contract

Next: [Browser Automation](browser-automation.md)
