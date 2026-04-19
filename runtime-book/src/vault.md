# Vault

Use the vault for secrets. Do not put raw secrets in manifests.

## Declaring Vault Access

Declare the keys your tool may use in the manifest:

```yaml
vault:
  - key: lx_token
    scope_kinds: [shared]
  - key: profile_api_key
    scope_kinds: [profile]
```

This declaration does not inject secret values into your script. It declares which keys and scope kinds the tool expects to use.

## Supported Scopes

The public runtime request shapes support:

- `profile`
- `shared`

Profile scope uses only the secret key:

```ts
const token = await flo.vault.get({
  scope: "profile",
  key: "profile_api_key",
});
```

Shared scope also requires `scope_id`:

```ts
const sharedToken = await flo.vault.get({
  scope: "shared",
  scope_id: "shared",
  key: "lx_token",
});
```

## Authoring Guidance

- Use vault for credentials, API tokens, session secrets, and other secret material.
- Keep non-secret durable data in [State](state.md), not in the vault.
- Treat returned secret strings as sensitive. Do not place them in normal tool outputs or debug payloads.

## Local Testing With Mocked Secrets

The local preload shim supports `flo.vault.get(...)` via `FLO_MOCKS_FILE`.

Example shape:

```json
{
  "vault": {
    "profile": {
      "profile_api_key": "alice-profile-token"
    },
    "shared": {
      "shared": {
        "lx_token": "shared-api-token"
      }
    }
  }
}
```

Then run:

```bash
FLO_MOCKS_FILE=./vault_mocks.json node --import=./flo_hooks.mts ./script.mts
```

Next: [State](state.md)
