# Universal Profile Skill

Manage LUKSO Universal Profiles — identity, permissions, tokens, and blockchain operations via direct or gasless relay transactions.

## Features

- **Profile Management** — Query profile info, configure UP addresses
- **Key Management** — Generate controller keypairs, encode/decode permissions
- **Token Operations** — Query LSP7/LSP8 assets, transfer tokens
- **Gasless Relay** — Execute transactions via LUKSO's relay service (no gas needed)
- **Authorization** — Generate auth URLs with permission presets

## Quick Start

```bash
npm install
up status                    # Check config and connectivity
up profile info <address>    # View profile details
up key generate --save       # Generate a controller key
```

## Permission Presets

| Preset | Risk | Use Case |
|---|---|---|
| `read-only` | 🟢 | Query data only |
| `token-operator` | 🟡 | Transfer tokens |
| `nft-trader` | 🟡 | Trade NFTs |
| `profile-manager` | 🟡 | Update profile metadata |
| `defi-trader` | 🟠 | DeFi interactions |
| `full-access` | 🔴 | Everything |

## Author

Created by [frozeman](https://github.com/frozeman) (Fabian Vogelsteller)
