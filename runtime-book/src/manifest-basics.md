# Manifest Basics

Flo discovers tools and skills by file name:

- `**/*.tool.yaml` defines one tool
- `**/*.skill.yaml` defines one skill

`SKILL.md` discovery is not supported.

## Tool Manifest Shape

Every tool manifest must include:

- `name`
- `description`
- `input_schema`
- `execution`

Optional fields include:

- `timeout_ms`
- `retry_policy`
- `vault`
- `state`

Example:

```yaml
name: capture_example
description: Open a page and return the current URL.
input_schema:
  type: object
  properties:
    url:
      type: string
  required: [url]
  additionalProperties: false
execution:
  type: script
  script_file: scripts/capture_example.mts
  entrypoint: run
timeout_ms: 30000
```

## Script Execution

For `execution.type = script`, define exactly one of:

- `script`: inline JavaScript or TypeScript
- `script_file`: a relative path under the manifest directory

Use `entrypoint` to select the exported function the runtime should call.

## Skill Manifest Shape

Every skill manifest must include:

- `skill_id`
- `name`
- `description`

Each skill must define exactly one instruction source:

- `instruction`
- `instruction_file`

Optional fields include:

- `version`
- `tools`
- `tool_definitions`
- `requires_skills`

Example:

```yaml
skill_id: browser_examples
name: Browser Examples
description: Browser-based tools for authenticated workflows.
tools:
  - read_text_file
tool_definitions:
  - name: capture_example
    description: Open a page and return the current URL.
    input_schema:
      type: object
      properties:
        url:
          type: string
      required: [url]
    execution:
      type: script
      script_file: scripts/capture_example.mts
      entrypoint: run
instruction_file: instructions.md
```

## State and Vault Declarations

Use `state` when your script needs durable non-secret data:

```yaml
state:
  - name: session_counter
    key_prefix: counter.session.
    scope_kind: session
  - name: shared_counter
    key_prefix: counter.shared.
    scope_kind: shared
    scope_id: service
```

Use `vault` when your script needs secrets:

```yaml
vault:
  - key: api_token
    scope_kinds: [profile, shared]
```

The runtime still requires the script to fetch secrets explicitly through `flo.vault.get(...)`.

## Import Rules

The script runtime supports:

- local static ESM imports
- relative `.mjs`, `.mts`, and related local module paths

The runtime rejects:

- bare specifiers
- package-style imports
- dynamic `import()`
- `..` traversal for author-facing asset imports

Next: [TypeScript Runtime](typescript-runtime.md)
