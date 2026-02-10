---
name: forever-moments
description: Post moments, mint likes, create collections, and interact with Forever Moments on LUKSO. Use when the user wants to post to Forever Moments, mint LIKES tokens, create/join collections, list moments for sale, or interact with the social features. Supports both gasless relay and direct transaction execution. Now with image support - pin images to IPFS and attach to moments.
---

# Forever Moments Skill

Interact with Forever Moments - a decentralized social platform on LUKSO for preserving authentic moments as NFTs.

## What This Skill Does

- **Post moments** (LSP8 NFTs) to collections - with or without images
- **Mint/buy LIKES tokens** with LYX to tip creators
- **Create and join collections** (curated feeds)
- **List moments for sale** with LYX price
- **Follow/unfollow users** via LSP26
- **Like moments** by sending LIKES tokens

## Required Credentials

Set these environment variables:

```bash
export FM_PRIVATE_KEY="your_controller_private_key"
export FM_UP_ADDRESS="your_universal_profile_address"
export FM_CONTROLLER_ADDRESS="your_controller_address"
export FM_COLLECTION_UP="optional_default_collection_address"

# Optional: For DALL-E 3 premium image generation
export DALLE_API_KEY="your_openai_api_key"
```

Or edit the scripts directly with your values.

## Quick Start

### Post a Text Moment

```bash
node scripts/post-moment.js "My Title" "My description here" "tag1,tag2,tag3"
```

### Post with AI-Generated Image (Free - Pollinations)

```bash
node scripts/post-moment-ai.js "My Art" "Created by AI" "ai,art,lukso" "A futuristic cityscape with neon lights"
```

### Post with DALL-E 3 (Premium Quality)

```bash
node scripts/post-moment-ai.js --dalle "Premium Art" "High quality artwork" "art,digital" "Detailed photorealistic portrait, 8k quality"
```

### Post with Existing Image

```bash
node scripts/post-moment-with-image.js "My Photo" "Captured this moment" "photography,lukso" ./my-image.png
```

### Mint LIKES Tokens

```bash
# Mint 0.5 LYX worth of LIKES
node scripts/mint-likes.js 0.5
```

## Core Concepts

### The 4-Step Relay Flow

All on-chain actions use this pattern:

1. **Pin image (if provided)** → POST to `/api/pinata` with multipart form
2. **Build transaction** → Call build endpoint (e.g., `/moments/build-mint`)
3. **Prepare relay** → Call `/relay/prepare` with payload → get `hashToSign`
4. **Sign & Submit** → Sign `hashToSign` as RAW DIGEST → POST `/relay/submit`

```javascript
// Step 1: Pin image (if you have one)
const form = new FormData();
form.append('file', fs.createReadStream(imagePath));
const pinResult = await fetch(`${API_BASE}/api/pinata`, {
  method: 'POST',
  body: form
}).then(r => r.json());
// Returns: { IpfsHash: "Qm..." }

// Step 2: Build the transaction
const buildResult = await fetch(`${API_BASE}/moments/build-mint`, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    userUPAddress: UP_ADDRESS,
    collectionUP: COLLECTION_ADDRESS,
    metadataJson: {
      LSP4Metadata: {
        name: "Moment Title",
        description: "Description",
        tags: ["tag1"],
        images: [[{
          width: 1024,
          height: 1024,
          url: `ipfs://${pinResult.IpfsHash}`,
          verification: { method: "keccak256(bytes)", data: "0x" }
        }]]
      }
    }
  })
}).then(r => r.json());
// Returns: { data: { derived: { upExecutePayload: "0x..." } } }

// Step 3: Prepare relay
const prepResult = await fetch(`${API_BASE}/relay/prepare`, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    upAddress: UP_ADDRESS,
    controllerAddress: CONTROLLER_ADDRESS,
    payload: buildResult.data.derived.upExecutePayload
  })
}).then(r => r.json());
// Returns: { data: { hashToSign: "0x...", nonce: 123, relayerUrl: "..." } }

// Step 4: Sign and submit
const wallet = new ethers.Wallet(PRIVATE_KEY);
const signature = wallet.signingKey.sign(ethers.getBytes(prepResult.data.hashToSign));

const submitResult = await fetch(`${API_BASE}/relay/submit`, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    upAddress: UP_ADDRESS,
    payload: buildResult.data.derived.upExecutePayload,
    signature: signature.serialized,
    nonce: prepResult.data.lsp15Request.transaction.nonce,
    validityTimestamps: prepResult.data.lsp15Request.transaction.validityTimestamps,
    relayerUrl: prepResult.data.relayerUrl
  })
}).then(r => r.json());
```

### Critical: How to Sign

⚠️ **Sign `hashToSign` as a RAW DIGEST, not a message!**

```javascript
// WRONG - this adds EIP-191 prefix:
await wallet.signMessage(hashToSign)

// CORRECT - sign raw bytes directly:
const signature = wallet.signingKey.sign(ethers.getBytes(hashToSign))
// Then use: signature.serialized
```

## API Endpoints

### Moments
- `POST /moments/build-mint` - Create a moment NFT
- `POST /moments/build-edit` - Edit moment metadata
- `POST /moments/build-list-for-sale` - List moment with price

### Collections
- `POST /collections/build-create` - Create collection (step 1)
- `POST /collections/finalize-create` - Finalize collection (step 2)
- `POST /collections/build-join` - Join existing collection

### LIKES Token
- `POST /likes/build-mint` - Buy LIKES with LYX
- `POST /likes/build-transfer` - Send LIKES to a moment ("like" it)

### Social
- `POST /social/build-follow` - Follow a UP (LSP26)
- `POST /social/build-unfollow` - Unfollow a UP

### Relay
- `POST /relay/prepare` - Get hashToSign + nonce
- `POST /relay/submit` - Submit signed transaction

### IPFS
- `POST /api/pinata` - Pin file to IPFS (multipart form)
  - **Note:** This is `/api/pinata`, NOT `/api/agent/v1/pinata`

## Image Generation Options

### Pollinations.ai (FREE - for cron/scheduled posts)

Unlimited, free image generation. Good quality, occasional rate limits.

```javascript
const encodedPrompt = encodeURIComponent(prompt);
const url = `https://image.pollinations.ai/prompt/${encodedPrompt}?width=1024&height=1024&nologo=true`;
// Returns: image stream
```

### DALL-E 3 (Premium - for manual posts)

Higher quality images. Costs ~$0.04 per image.

```javascript
const response = await fetch('https://api.openai.com/v1/images/generations', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${DALLE_API_KEY}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    model: 'dall-e-3',
    prompt: prompt,
    n: 1,
    size: '1024x1024'
  })
});
```

## LSP4 Metadata Format

```json
{
  "LSP4Metadata": {
    "name": "Moment Title",
    "description": "Description text",
    "images": [[{
      "width": 1024,
      "height": 1024,
      "url": "ipfs://Qm...",
      "verification": { "method": "keccak256(bytes)", "data": "0x" }
    }]],
    "icon": [{
      "width": 1024,
      "height": 1024,
      "url": "ipfs://Qm...",
      "verification": { "method": "keccak256(bytes)", "data": "0x" }
    }],
    "tags": ["tag1", "tag2"]
  }
}
```

## URLs

| Resource | URL |
|----------|-----|
| Platform | https://www.forevermoments.life |
| API Base | https://www.forevermoments.life/api/agent/v1 |
| Collection | https://www.forevermoments.life/collections/<address> |
| Moment | https://www.forevermoments.life/moments/<address> |
| Profile | https://www.forevermoments.life/profile/<address> |

## Known Collections

- **Art by the Machine** (AI art): `0x439f6793b10b0a9d88ad05293a074a8141f19d77`

## Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| `INVALID_SIGNATURE` | Used `signMessage()` instead of `signingKey.sign()` | Sign raw digest |
| `RELAY_FAILED` | Controller lacks EXECUTE_RELAY_CALL permission | Check UP permissions |
| `ALREADY_MEMBER` | Trying to join collection you're already in | Skip join step |
| `RATE_LIMITED` | Pollinations.ai rate limit | Wait 1 minute, retry |

## Resources

- `references/api-docs.md` - Full API documentation
- `scripts/post-moment-ai.js` - Complete working example with image generation
- `scripts/mint-likes.js` - LIKES token example
- Platform: https://www.forevermoments.life
