# Debug Events

Use `flo.task.emitEvent(...)` to append structured events to the current task timeline.

Example:

```ts
await flo.task.emitEvent({
  event_type: "orders.sync.started",
  title: "Sync started",
  message: "Fetching latest order batch",
  level: "info",
  payload: {
    shop_id: "acme",
    page: 1,
  },
});
```

## Recommended Event Shape

- `event_type`: stable machine-readable identifier
- `title`: short human-readable label
- `message`: one-line progress update
- `level`: `info`, `warning`, or `error`
- `payload`: extra structured debug context

## When To Emit Events

Emit events at boundaries that help explain tool behavior:

- external API request started
- expensive step completed
- retry branch taken
- validation failure
- child batch created or resumed

Avoid emitting noisy events for every trivial line of execution.

## Local Testing

In the local Node preload shim, `flo.task.emitEvent(...)` writes the request to `console.log`. That makes it useful for smoke tests without requiring the full runtime.

Next: [Vault](vault.md)
