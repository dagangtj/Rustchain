# RustChain API Reference

Complete API documentation for RustChain mainnet node at `https://50.28.86.131`

## Base URL

```
https://50.28.86.131
```

**Note:** All endpoints use HTTPS with self-signed certificates. Use `-k` flag with curl to skip certificate verification.

---

## Health & Status Endpoints

### GET /health

Health check endpoint for node status monitoring.

**Request:**
```bash
curl -sk https://50.28.86.131/health
```

**Response:**
```json
{
  "backup_age_hours": 17.527028133736717,
  "db_rw": true,
  "ok": true,
  "tip_age_slots": 0,
  "uptime_s": 411823,
  "version": "2.2.1-rip200"
}
```

**Response Fields:**
- `ok` (boolean): Overall health status
- `db_rw` (boolean): Database read/write status
- `uptime_s` (integer): Node uptime in seconds
- `version` (string): Node software version
- `tip_age_slots` (integer): Age of chain tip in slots
- `backup_age_hours` (float): Hours since last backup

---

### GET /ready

Readiness probe for load balancers and orchestration systems.

**Request:**
```bash
curl -sk https://50.28.86.131/ready
```

**Response:**
```json
{
  "ready": true,
  "db_ok": true,
  "version": "2.2.1-rip200"
}
```

---

## Network Statistics

### GET /api/stats

Get comprehensive network statistics including miners, balances, and chain state.

**Request:**
```bash
curl -sk https://50.28.86.131/api/stats
```

**Response:**
```json
{
  "block_time": 600,
  "chain_id": "rustchain-mainnet-v2",
  "epoch": 86,
  "features": [
    "RIP-0005",
    "RIP-0008",
    "RIP-0009",
    "RIP-0142",
    "RIP-0143",
    "RIP-0144"
  ],
  "pending_withdrawals": 0,
  "security": [
    "no_mock_sigs",
    "mandatory_admin_key",
    "replay_protection",
    "validated_json"
  ],
  "total_balance": 217414.407415,
  "total_miners": 326692,
  "version": "2.2.1-security-hardened"
}
```

**Response Fields:**
- `chain_id` (string): Network identifier
- `version` (string): Protocol version
- `epoch` (integer): Current epoch number
- `block_time` (integer): Target block time in seconds
- `total_miners` (integer): Total registered miners
- `total_balance` (float): Total RTC in circulation
- `pending_withdrawals` (integer): Number of pending withdrawal requests
- `features` (array): Active RustChain Improvement Proposals (RIPs)
- `security` (array): Enabled security features

---

## Miner Information

### GET /api/miners

List all active miners with their Proof of Antiquity (PoA) details.

**Query Parameters:**
- None (public endpoint returns limited data)
- Admin authentication required for full details

**Request:**
```bash
curl -sk https://50.28.86.131/api/miners
```

**Response:**
```json
[
  {
    "antiquity_multiplier": 1.0,
    "device_arch": "modern",
    "device_family": "x86",
    "entropy_score": 0.0,
    "first_attest": null,
    "hardware_type": "x86-64 (Modern)",
    "last_attest": 1772138931,
    "miner": "boozelee"
  },
  {
    "antiquity_multiplier": 2.0,
    "device_arch": "power8",
    "device_family": "PowerPC",
    "entropy_score": 0.0,
    "first_attest": null,
    "hardware_type": "PowerPC (Vintage)",
    "last_attest": 1772138358,
    "miner": "power8-s824-sophia"
  }
]
```

**Response Fields:**
- `miner` (string): Unique miner identifier
- `hardware_type` (string): Hardware classification
- `device_arch` (string): CPU architecture
- `device_family` (string): Device family (x86, ARM, PowerPC, etc.)
- `antiquity_multiplier` (float): Reward multiplier for vintage hardware (1.0-2.0)
- `last_attest` (integer): Unix timestamp of last attestation
- `first_attest` (integer|null): Unix timestamp of first attestation
- `entropy_score` (float): Hardware entropy score

---

### GET /api/badge/:miner_id

Get badge information for a specific miner.

**Path Parameters:**
- `miner_id` (string): Miner identifier

**Request:**
```bash
curl -sk https://50.28.86.131/api/badge/boozelee
```

**Response:**
```json
{
  "miner_id": "boozelee",
  "badges": [],
  "hardware_type": "x86-64 (Modern)",
  "antiquity_multiplier": 1.0
}
```

---

### GET /api/miner/:miner_id/attestations

Get attestation history for a specific miner.

**Path Parameters:**
- `miner_id` (string): Miner identifier

**Request:**
```bash
curl -sk https://50.28.86.131/api/miner/boozelee/attestations
```

**Response:**
```json
{
  "miner_id": "boozelee",
  "attestations": [
    {
      "timestamp": 1772138931,
      "hardware_type": "x86-64 (Modern)",
      "device_arch": "modern",
      "valid": true
    }
  ]
}
```

---

## Node Information

### GET /api/nodes

List all registered attestation nodes in the network.

**Request:**
```bash
curl -sk https://50.28.86.131/api/nodes
```

**Response:**
```json
[
  {
    "node_id": "node-001",
    "url": "https://node1.rustchain.network",
    "status": "active",
    "last_seen": 1772138931
  }
]
```

**Note:** Full node URLs are redacted for non-admin requests for security.

---

## Wallet & Balance Endpoints

### GET /api/balances

Get wallet balances for all miners (paginated).

**Query Parameters:**
- `limit` (integer, optional): Number of results (default: 2000, max: 5000)

**Request:**
```bash
curl -sk "https://50.28.86.131/api/balances?limit=10"
```

**Response:**
```json
{
  "ok": true,
  "balances": [
    {
      "miner": "boozelee",
      "amount_i64": 1500000000,
      "amount": 1.5
    }
  ],
  "count": 10
}
```

**Response Fields:**
- `ok` (boolean): Request success status
- `balances` (array): List of miner balances
  - `miner` (string): Miner identifier
  - `amount_i64` (integer): Balance in micro-RTC (1 RTC = 1,000,000,000 µRTC)
  - `amount` (float): Balance in RTC
- `count` (integer): Number of results returned

---

### GET /wallet/balance

Get balance for a specific miner (admin only).

**Query Parameters:**
- `miner_id` (string, required): Miner identifier

**Headers:**
- `X-Admin-Key` or `X-API-Key`: Admin authentication key

**Request:**
```bash
curl -sk -H "X-Admin-Key: YOUR_ADMIN_KEY" \
  "https://50.28.86.131/wallet/balance?miner_id=boozelee"
```

**Response:**
```json
{
  "ok": true,
  "miner_id": "boozelee",
  "balance": 1.5,
  "balance_i64": 1500000000
}
```

---

### GET /wallet/balances/all

Get all wallet balances with full details (admin only).

**Headers:**
- `X-Admin-Key` or `X-API-Key`: Admin authentication key

**Request:**
```bash
curl -sk -H "X-Admin-Key: YOUR_ADMIN_KEY" \
  https://50.28.86.131/wallet/balances/all
```

**Response:**
```json
{
  "ok": true,
  "balances": [
    {
      "miner": "boozelee",
      "amount": 1.5,
      "amount_i64": 1500000000,
      "last_updated": 1772138931
    }
  ],
  "total_balance": 217414.407415,
  "total_miners": 326692
}
```

---

### GET /wallet/ledger

Get transaction ledger for a miner (admin only).

**Query Parameters:**
- `miner_id` (string, required): Miner identifier
- `limit` (integer, optional): Number of transactions (default: 100)

**Headers:**
- `X-Admin-Key` or `X-API-Key`: Admin authentication key

**Request:**
```bash
curl -sk -H "X-Admin-Key: YOUR_ADMIN_KEY" \
  "https://50.28.86.131/wallet/ledger?miner_id=boozelee&limit=10"
```

**Response:**
```json
{
  "ok": true,
  "miner_id": "boozelee",
  "transactions": [
    {
      "timestamp": 1772138931,
      "type": "reward",
      "amount": 0.5,
      "balance_after": 1.5
    }
  ]
}
```

---

## Epoch & Mining

### GET /epoch

Get current epoch information.

**Request:**
```bash
curl -sk https://50.28.86.131/epoch
```

**Response:**
```json
{
  "epoch": 86,
  "slot": 51600,
  "block_time": 600,
  "epoch_start_slot": 51000,
  "epoch_end_slot": 52000
}
```

**Response Fields:**
- `epoch` (integer): Current epoch number
- `slot` (integer): Current slot number
- `block_time` (integer): Block time in seconds
- `epoch_start_slot` (integer): First slot of current epoch
- `epoch_end_slot` (integer): Last slot of current epoch

---

### POST /epoch/enroll

Enroll in the current epoch for mining eligibility.

**Request Body:**
```json
{
  "miner_id": "your-miner-id",
  "signature": "hex-encoded-signature",
  "pubkey": "hex-encoded-public-key"
}
```

**Request:**
```bash
curl -sk -X POST https://50.28.86.131/epoch/enroll \
  -H "Content-Type: application/json" \
  -d '{
    "miner_id": "your-miner-id",
    "signature": "...",
    "pubkey": "..."
  }'
```

**Response:**
```json
{
  "ok": true,
  "epoch": 86,
  "enrolled": true,
  "miner_id": "your-miner-id"
}
```

---

### GET /lottery/eligibility

Check round-robin lottery eligibility (RIP-200).

**Query Parameters:**
- `miner_id` (string, required): Miner identifier

**Request:**
```bash
curl -sk "https://50.28.86.131/lottery/eligibility?miner_id=boozelee"
```

**Response:**
```json
{
  "ok": true,
  "miner_id": "boozelee",
  "eligible": true,
  "next_slot": 51650,
  "probability": 0.003
}
```

---

## Attestation Endpoints

### GET /attest/challenge

Request a hardware attestation challenge.

**Request:**
```bash
curl -sk https://50.28.86.131/attest/challenge
```

**Response:**
```json
{
  "challenge": "a1b2c3d4e5f6...",
  "nonce": "hex-encoded-nonce",
  "expires": 1772139531
}
```

**Response Fields:**
- `challenge` (string): Challenge string to sign
- `nonce` (string): Unique nonce for this challenge
- `expires` (integer): Unix timestamp when challenge expires

---

### POST /attest/submit

Submit hardware attestation with fingerprint validation.

**Request Body:**
```json
{
  "miner_id": "your-miner-id",
  "nonce": "challenge-nonce",
  "signature": "signed-challenge",
  "hardware_info": {
    "cpu": "Intel Core i7",
    "arch": "x86_64",
    "family": "modern"
  }
}
```

**Request:**
```bash
curl -sk -X POST https://50.28.86.131/attest/submit \
  -H "Content-Type: application/json" \
  -d '{
    "miner_id": "your-miner-id",
    "nonce": "...",
    "signature": "...",
    "hardware_info": {...}
  }'
```

**Response:**
```json
{
  "ok": true,
  "miner_id": "your-miner-id",
  "hardware_type": "x86-64 (Modern)",
  "antiquity_multiplier": 1.0,
  "attested": true
}
```

---

## Rewards & Settlement

### POST /rewards/settle

Settle rewards for the current epoch (admin only).

**Headers:**
- `X-Admin-Key` or `X-API-Key`: Admin authentication key

**Request:**
```bash
curl -sk -X POST https://50.28.86.131/rewards/settle \
  -H "X-Admin-Key: YOUR_ADMIN_KEY" \
  -H "Content-Type: application/json"
```

**Response:**
```json
{
  "ok": true,
  "epoch": 86,
  "rewards_distributed": 1000.0,
  "miners_rewarded": 150
}
```

---

### GET /rewards/epoch/:epoch

Get reward distribution for a specific epoch.

**Path Parameters:**
- `epoch` (integer): Epoch number

**Request:**
```bash
curl -sk https://50.28.86.131/rewards/epoch/86
```

**Response:**
```json
{
  "ok": true,
  "epoch": 86,
  "total_rewards": 1000.0,
  "distributions": [
    {
      "miner": "boozelee",
      "amount": 6.67,
      "antiquity_multiplier": 1.0
    }
  ]
}
```

---

## Hunter Badges

### GET /api/hunters/badges

Get Hall of Hunters badge information.

**Query Parameters:**
- `refresh` (boolean, optional): Force cache refresh (values: 1, true, yes)

**Request:**
```bash
curl -sk "https://50.28.86.131/api/hunters/badges?refresh=1"
```

**Response:**
```json
{
  "badges": [
    {
      "hunter_id": "hunter-001",
      "badge_type": "gold",
      "earned_date": 1772138931
    }
  ]
}
```

---

## OpenAPI Specification

### GET /openapi.json

Get the OpenAPI 3.0.3 specification for this API.

**Request:**
```bash
curl -sk https://50.28.86.131/openapi.json
```

**Response:**
OpenAPI 3.0.3 JSON specification document.

---

## Error Responses

All endpoints may return error responses in the following format:

```json
{
  "ok": false,
  "error": "Error description",
  "code": "ERROR_CODE"
}
```

### Common HTTP Status Codes

- `200 OK`: Request successful
- `400 Bad Request`: Invalid parameters or request body
- `401 Unauthorized`: Missing or invalid authentication
- `404 Not Found`: Endpoint or resource not found
- `410 Gone`: Deprecated endpoint (e.g., v1 mining API)
- `500 Internal Server Error`: Server-side error
- `504 Gateway Timeout`: Request timeout

---

## Authentication

Admin endpoints require authentication via headers:

```bash
-H "X-Admin-Key: YOUR_ADMIN_KEY"
# or
-H "X-API-Key: YOUR_ADMIN_KEY"
```

Admin key is configured via `RC_ADMIN_KEY` environment variable on the node.

---

## Rate Limiting

No explicit rate limiting is documented. Use reasonable request rates to avoid being blocked.

---

## Deprecated Endpoints

### POST /api/mine (v1)

**Status:** 410 Gone

The v1 mining API has been removed. Use the v2 epoch enrollment system:
- `POST /epoch/enroll` for registration
- VRF ticket submission on port 8099

**Response:**
```json
{
  "error": "API v1 removed",
  "use": "POST /epoch/enroll and VRF ticket submission on :8099",
  "version": "v2.2.1"
}
```

---

## Additional Resources

- **Whitepaper:** See `/docs/WHITEPAPER.md`
- **Protocol Spec:** See `/docs/PROTOCOL.md`
- **RIPs:** See `/rips/` directory
- **Installation:** See `/INSTALL.md`

---

## Support

For issues and questions:
- GitHub Issues: https://github.com/Scottcjn/Rustchain/issues
- Documentation: https://github.com/Scottcjn/Rustchain/tree/main/docs

---

**Last Updated:** 2026-02-27  
**API Version:** 2.2.1-rip200  
**Chain ID:** rustchain-mainnet-v2
