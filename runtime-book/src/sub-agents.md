# Sub Agents

Use child-task helpers when a script needs durable parallel work for specialized subtasks.

## Spawn Children

Create a child batch with `flo.task.spawnChildren(...)`:

```ts
const spawned = await flo.task.spawnChildren({
  children: [
    {
      worker_kind: "extractor",
      title: "Extract invoice fields",
      objective: "Extract invoice number and total from the document",
      input: { document_id: input.document_id },
    },
    {
      worker_kind: "classifier",
      title: "Classify invoice type",
      objective: "Classify the invoice into the supported categories",
      input: { document_id: input.document_id },
    },
  ],
});
```

Each child defines:

- `worker_kind`
- `title`
- `objective`
- `input`

## Wait For Completion

Use `flo.task.waitForBatch(...)` when the parent should suspend until all child tasks are terminal:

```ts
await flo.task.putToolState({
  key: "batch_checkpoint",
  value: { batch_id: spawned.batch.batch_id },
});

const results = await flo.task.waitForBatch({
  batch_id: spawned.batch.batch_id,
});
```

Important: after suspension, the runtime re-enters the script from the entrypoint. Persist the progress you need before waiting.

## Read Results Without Suspending

Use `flo.task.getBatchResults(...)` only when the batch is already terminal:

```ts
const results = await flo.task.getBatchResults({
  batch_id: batchId,
});
```

If the batch is still pending, this call fails non-retryably.

## Result Shape

Each child result includes:

- `child_task_id`
- `worker_kind`
- `status`
- `output`
- optional `error`
- optional `completed_at`

## Authoring Guidance

- Use child tasks for durable parallelism, not for lightweight local branching.
- Persist checkpoints with [Task Tool State](task-tool-state.md) before waiting.
- Keep child inputs compact and JSON-serializable.
- Handle partial failures explicitly; one child can fail while others succeed.

Next: [Other APIs](other-apis.md)
