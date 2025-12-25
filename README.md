# FF1-CLI

A small Node.js CLI for building DP1 playlists from NFT collections.

FF1-CLI turns a simple prompt into a DP-1–conformant playlist you can preview on an FF1. The model orchestrates; deterministic tools do the real work (schema validation, indexing, JSON‑LD). If something comes back invalid, validation rejects it and we loop until it’s right.

Note: Publishing to an open registry is on the roadmap.

## Quick Start

**Set your LLM API key first (default Grok):** `export GROK_API_KEY='xai-your-api-key-here'`

```bash
npm install
npm run dev -- config init
npm run dev chat
```

## Documentation

- Getting started, config, and usage: `./docs/README.md`
- Function calling architecture: `./docs/FUNCTION_CALLING.md`
- Examples: `./docs/EXAMPLES.md`

## Scripts

```bash
npm run dev            # Run CLI in dev (tsx)
npm run build          # Build TypeScript
npm run lint:fix       # Lint + fix
```

## License

MIT

## Common Issues & Fixes (2025-12 Updates)

### 1. Grok Model Deprecation
**Error:** `The model grok-beta was deprecated...`
**Fix:** Update `config.json` to use a newer model version (e.g., `grok-3`).
```json
"models": {
  "grok": {
    "model": "grok-3",
    ...
  }
}
```

### 2. Runtime Module Loading Error
**Error:** `Intent parser failed: validateAddresses is not a function`
**Cause:** Dynamic `import()` calls failing in certain runtime contexts (especially within `src/utilities/functions.js`).
**Fix:** Replaced dynamic imports with standard `require()` calls to ensure reliable module resolution.

### 3. Playlist Verification & Signing
**Error:** `Invalid playlist data: signature: Required`
**Cause:** The `dp1-js` library enforces DP-1 specification compliance, which requires playlists to be signed, even for local use.
**Fix:** 
- You must configure a valid Ed25519 private key in `config.json`.
- **Important:** The key must be in **PKCS#8** format (base64 encoded). Simple random seeds will fail with `Invalid keyData`.
- Use `crypto.generateKeyPair` (Node.js) to generate a compatible key.
