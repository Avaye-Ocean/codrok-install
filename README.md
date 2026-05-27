# codrok-install — Bootstrap Script

**Public.** Single-command bootstrap for the codrok AI coding platform.

## Usage

```bash
bash <(curl -fsSL https://raw.githubusercontent.com/Avaye-Ocean/codrok-install/main/install.sh)
```

The script will:
1. Prompt for your `CODROK_API_KEY` (or use the env var)
2. Verify the Ed25519 signature, expiry, and org binding
3. Install Claude Code CLI if missing
4. Clone the codrok platform to `~/.codrok/`
5. Install all commands to `~/.claude/commands/`
6. Create a deployment repo `{your-org}-codrok`
7. Register the installation

After install, open Claude Code in any repo and run:

```
/codrok setup
```

## Requirements

- A `CODROK_API_KEY` issued by Avaye (one per GitHub org)
- GitHub CLI (`gh`) authenticated — for cloning the private source repo

## How Verification Works

The script embeds the codrok Ed25519 **public key**. It can verify that a key was signed by the matching private key (held in the private `codrok-algo` repo), but it cannot generate new keys. This means:

- The install script is safe to host publicly
- Only Avaye can issue valid license keys
- Tampered or forged keys are rejected

## Key Format

```
codrok_v1.<base64url(payload)>.<base64url(signature)>

payload = {"org":"...","iat":"...","exp":"...","kid":"...","v":1}
```

## Files

| File | Purpose |
|---|---|
| `install.sh` | The bootstrap script — the only file in this repo |

## Security

- The embedded public key is safe to publish — it only verifies, never signs
- The embedded GitHub App credentials grant write access to `codrok-tracker` **only**
- Installation tokens expire in 1 hour and are generated fresh on each API call
- If the App's private key is compromised, rotate it in GitHub App settings — no changes needed on installed machines
