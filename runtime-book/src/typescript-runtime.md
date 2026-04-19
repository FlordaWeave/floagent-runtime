# TypeScript Runtime

The public runtime surface comes from:

```ts
import * as flo from "flo:runtime";
```

The module exports these top-level helpers:

- `sleep`
- `time`
- `vault`
- `state`
- `task`
- `callTool`
- `browser`

## JSON-Oriented API Design

Runtime values are JSON-shaped. `FloJsonValue` includes:

- `null`
- `boolean`
- `number`
- `string`
- arrays of JSON values
- objects whose values are JSON values

This affects:

- tool inputs and outputs
- `flo.state`
- child-task input and output
- browser `evaluate` args and values

## Global Helpers

The runtime provides:

- `fetch(...)`
- `URL`
- `URLSearchParams`

That means many integration-oriented tools can work without additional libraries.

## Task Context

Use `flo.task.getContext<T>()` to read durable task context:

```ts
type Context = {
  resume_payload?: {
    batch_id?: string;
  };
  custom?: { value: number };
};

const context = await flo.task.getContext<Context>();
```

The context may include resume data after a suspension flow. When a task resumes, the runtime re-enters the script from its entrypoint instead of restoring the JavaScript stack.

## Time and Sleep

Use `flo.sleep(ms)` to pause a script without blocking the runtime thread.

Use `flo.time.formatUnixTimestamp(...)` when you need stable date formatting:

```ts
const formatted = flo.time.formatUnixTimestamp(1_700_000_000, "YYYY-MM-DD HH:mm:ss", "UTC");
```

## Local Shim Notes

The Node preload shim supports:

- `flo.sleep(...)`
- `flo.time.formatUnixTimestamp(...)`
- `flo.vault.get(...)` with `FLO_MOCKS_FILE`
- `flo.task.getContext(...)`
- `flo.task.emitEvent(...)`
- browser helpers when `FLO_LOCAL_BROWSER=1`

Other runtime-bound APIs intentionally fail in the local shim so tests do not accidentally depend on unsupported local behavior.

Next: [Nested Tool Calls](nested-tool-calls.md)
