# floagent-runtime

Generated runtime assets published from [`FlordaWeave/floagent`](https://github.com/FlordaWeave/floagent).

This repository is a release snapshot of the Flo runtime authoring surface for local development. It is force-updated from tagged releases in the main Flo agent repository.

## Contents

- `flo.d.ts`: generated TypeScript declarations for `import * as flo from "flo:runtime"`
- `tsconfig.json`: minimal TypeScript config for runtime authoring against the published declarations
- `flo_hooks.mts`: local Node import hook for script testing
- `runtime-book/`: the public authoring guide for manifests and `flo:runtime`
- `skills/builtin/`: built-in skill manifests shipped with the runtime
- `workers/playwright-worker/`: bundled Playwright worker source used by the local browser shim

## Read The Book

For setup, manifest guidance, `flo:runtime` APIs, and detailed `flo_hooks.mts` testing instructions, start with the runtime book:

- [`runtime-book/src/README.md`](https://github.com/FlordaWeave/floagent/blob/main/runtime-book/src/README.md)

The book is the source of truth for public author-facing documentation. This README stays intentionally brief.

## Intended Use

Use this repository when you want the current published runtime contract without cloning the full Flo agent monorepo.