# Task State

Use the `flo.task.*State(...)` helpers for lightweight state scoped to the current task.

Unlike `flo.state`, these helpers do not require a manifest state binding.

## Shared Task State

Use `flo.task.getState(...)` and `flo.task.putState(...)` when every tool in the current task should see the same value.

### Read Shared Task State

```ts
const checkpoint = await flo.task.getState<{
  page?: number;
  cursor?: string;
}>({
  key: "sync_checkpoint",
});
```

### Write Shared Task State

```ts
await flo.task.putState({
  key: "sync_checkpoint",
  value: {
    page: 2,
    cursor: "abc123",
  },
  ttl_seconds: 3600,
});
```

`putState(...)` supports:

- `key`
- `value`
- optional `ttl_seconds`
- optional `if_revision`

It returns the same write-result structure used by `flo.state.put(...)`.

Use shared task state for:

- cross-tool coordination inside one task
- shared progress markers
- durable checkpoints that later tools should reuse

## Tool-Partitioned Task State

Use `flo.task.getToolState(...)` and `flo.task.putToolState(...)` when the data belongs to one tool implementation.

### Read Current Tool State

```ts
const checkpoint = await flo.task.getToolState<{
  page?: number;
  cursor?: string;
}>({
  key: "sync_checkpoint",
});
```

You can also read another tool's convenience state in the same task by passing `tool_id`.

### Write Current Tool State

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

Use tool-partitioned task state for:

- per-tool resume checkpoints
- small per-tool task notes
- retry-safe progress markers owned by one tool

## Choosing Between `flo.state`, Shared Task State, And Tool State

Prefer `flo.task.getState(...)` / `putState(...)` when:

- the data is specific to the current task
- multiple tools in the same task should share it
- you do not need a reusable manifest-declared binding

Prefer `flo.task.getToolState(...)` / `putToolState(...)` when:

- the data belongs to one tool implementation
- you want task scoping but not cross-tool visibility

Prefer `flo.state` when:

- data should outlive the task
- data needs explicit profile, session, task, or shared binding semantics
- multiple scripts should share a named binding contract across runs

This is especially useful before `flo.task.waitForBatch(...)`, because resumed scripts restart from the entrypoint instead of restoring the JavaScript stack.

Next: [Browser Automation](browser-automation.md)
