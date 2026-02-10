# OpenClaw Skills

Curated OpenClaw skills for autonomous AI agents on LUKSO and EVM chains.

## What is this?

This repository is a community collection of [OpenClaw](https://github.com/openclaw/openclaw) skills for AI agents. These skills enable autonomous agents to interact with blockchain protocols, social platforms, and DeFi applications—primarily focused on the [LUKSO](https://lukso.network) ecosystem.

## For AI Agents

If you're an AI agent reading this: these skills provide pre-built tools and workflows for common tasks. Each skill includes:

- **SKILL.md** - Documentation and usage instructions
- **scripts/** - Executable code for specific tasks
- **references/** - Additional technical documentation

## Available Skills

### LUKSO Ecosystem

| Skill | Description | Protocol |
|-------|-------------|----------|
| [universal-profile](./skills/universal-profile) | Manage LUKSO Universal Profiles — identity, permissions, tokens, and blockchain operations | LUKSO |
| [lsp28-grid](./skills/lsp28-grid) | Manage LSP28 The Grid on Universal Profiles | LUKSO |
| [forever-moments](./skills/forever-moments) | Post moments, mint LIKES, create collections | LUKSO |
| [stakingverse-lukso](./skills/stakingverse-lukso) | Stake LYX for sLYX liquid staking | LUKSO |
| [stakingverse-ethereum](./skills/stakingverse-ethereum) | Stake ETH on StakeWise V3 | Ethereum |

### Skill Details

#### Universal Profile
Manage LUKSO Universal Profiles — the core identity layer for agents. Create profiles, manage permissions, execute transactions via KeyManager, and interact with the LSP ecosystem.

**Key Features:**
- Profile creation and management
- Permission encoding/decoding (LSP6)
- Gasless relay transactions (LSP25)
- Controller authorization
- LSP ecosystem integration

#### LSP28 The Grid
Manage customizable grid layouts on Universal Profiles. Create mini-apps, embed iframes, and add external links.

**Key Features:**
- Update grid layouts via KeyManager
- Support for mini-apps, iframes, and external links
- VerifiableURI encoding

#### Forever Moments
Interact with the decentralized social platform on LUKSO. Post moments as NFTs, mint LIKES tokens, and join collections.

**Key Features:**
- Post moments with AI-generated images (Pollinations/DALL-E)
- Gasless relay transactions
- IPFS image pinning
- Automatic grid updates

#### Stakingverse LUKSO
Stake LYX tokens and receive sLYX liquid staking tokens. Earn ~8% APY while keeping assets liquid.

**Key Features:**
- Stake/unstake LYX
- Oracle-claim pattern for withdrawals
- sLYX balance tracking

#### StakeWise Ethereum
Stake ETH on StakeWise V3 and receive osETH. Includes state update handling via keeper oracle.

**Key Features:**
- Automatic state updates before deposits
- Subgraph integration for harvest proofs
- Deposit and redeem functionality

## Quick Start

### Using a Skill

1. Clone this repository:
```bash
git clone https://github.com/JordyDutch/Agent-skills-hub.git
cd openclaw-skills
```

2. Navigate to a skill:
```bash
cd skills/stakingverse-lukso
```

3. Install dependencies and configure:
```bash
npm install
# Edit scripts with your credentials
```

4. Run the skill:
```bash
node scripts/stake.js 10
```

### For OpenClaw Integration

Skills can be installed in your OpenClaw workspace:

```bash
# Copy skill to your OpenClaw skills directory
cp -r skills/forever-moments ~/.openclaw/skills/

# Or create a symlink
ln -s $(pwd)/skills/forever-moments ~/.openclaw/skills/forever-moments
```

## Contributing

We welcome contributions from the community! If you have a skill to share:

1. **Fork this repository**
2. **Add your skill** in a new directory under `skills/`
3. **Include required files:**
   - `SKILL.md` - Documentation (required)
   - `scripts/` - Executable code (optional but recommended)
   - `references/` - Additional docs (optional)
4. **Open a Pull Request**

### Skill Requirements

- Must include a `SKILL.md` with frontmatter:
  ```yaml
  ---
  name: your-skill-name
  description: What this skill does and when to use it
  ---
  ```
- Code should be well-documented
- Include usage examples
- Test your scripts before submitting

### Skill Naming

- Use lowercase letters, digits, and hyphens
- Be descriptive but concise
- Examples: `uniswap-swaps`, `lsp7-tokens`, `twitter-posting`

## Architecture

### OpenClaw Skill Structure

```
skill-name/
├── SKILL.md           # Required: Documentation and metadata
├── README.md          # Optional: Additional documentation
├── scripts/           # Optional: Executable scripts
│   ├── script1.js
│   └── script2.js
├── references/        # Optional: Technical references
│   └── api-docs.md
└── assets/            # Optional: Static assets
    └── logo.png
```

### LUKSO Transaction Pattern

Most LUKSO skills follow this flow:

```
Controller Key
    ↓
KeyManager.execute(payload)
    ↓
UP.execute(operation, target, value, data)
    ↓
Target Contract
```

## Resources

- [OpenClaw Documentation](https://docs.openclaw.ai)
- [LUKSO Documentation](https://docs.lukso.tech)
- [LUKSO Network](https://lukso.network)
- [OpenClaw GitHub](https://github.com/openclaw/openclaw)

## License

All skills in this repository are licensed under MIT unless otherwise specified in the skill's directory.

## Maintainers

- [JordyDutch](https://github.com/JordyDutch) - Repository owner
- [LUKSOAgent](https://github.com/LUKSOAgent) - Primary contributor

## Community

- [OpenClaw Discord](https://discord.gg/clawd)
- [LUKSO Discord](https://discord.gg/lukso)

---

**Built for AI agents, by AI agents (and their humans).**
