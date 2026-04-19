# Browser Automation

Use `flo.browser` to drive a host-managed browser session from a script tool.

## Available Helpers

- `flo.browser.run(...)`
- `flo.browser.startRequestCapture(...)`
- `flo.browser.collectCapturedRequests(...)`
- `flo.browser.stopRequestCapture(...)`
- `flo.browser.exportState(...)`
- `flo.browser.importState(...)`

## Supported Commands

`flo.browser.run(...)` accepts these command types:

- `goto`
- `reload`
- `fill`
- `click`
- `press`
- `select`
- `wait_for`
- `extract`
- `evaluate`
- `screenshot`

Example:

```ts
await flo.browser.run({
  type: "goto",
  url: input.url,
  wait_until: "networkidle",
  timeout_ms: 30_000,
});

const shot = await flo.browser.run({
  type: "screenshot",
  full_page: true,
});
```

## Required Checks

Browser helpers accept optional session checks:

```ts
await flo.browser.run(
  { type: "click", selector: "button[type=submit]" },
  {
    required_checks: [
      {
        kind: "selector_present",
        value: ".dashboard",
        timeout_ms: 10_000,
      },
    ],
  },
);
```

Supported check kinds are:

- `url_not_matches`
- `selector_present`
- `selector_absent`

## Request Capture

Use request capture when you need to observe API traffic triggered by page actions.

```ts
const capture = await flo.browser.startRequestCapture([
  {
    url_regex: "https://example.com/orders/.*/api",
    resource_types: ["fetch"],
  },
]);

await flo.browser.run({
  type: "goto",
  url: input.page_url,
  wait_until: "networkidle",
});

const collected = await flo.browser.collectCapturedRequests(capture.capture_id, {
  timeout_ms: 10_000,
});
```

## Storage State

Use storage state helpers when your flow must resume after a handoff or action-needed pause:

- `exportState(...)` saves cookies and origin storage
- `importState(...)` restores a previously exported state

## Authoring Constraints

- Browser helpers only work when the runtime is executing a real tool invocation with task/session context.
- The local Node preload shim requires `FLO_LOCAL_BROWSER=1`.
- Keep browser steps short and deterministic.
- Use request capture for network observations instead of scraping low-level browser internals.

Next: [Sub Agents](sub-agents.md)
