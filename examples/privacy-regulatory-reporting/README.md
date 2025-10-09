# Privacy-Preserving Regulatory Reporting Example

> Complete example of a privacy-preserving compliance reporting system using `@fhevm-template/sdk`

## Overview

This example demonstrates a production-ready dApp for confidential regulatory reporting where:

- Financial institutions submit **encrypted** transaction reports
- Regulators can verify reports **without seeing raw data**
- Selective decryption for authorized analysts only
- Role-based access control with entity authorization

## Features

- ✅ **Fully Homomorphic Encryption** - Computations on encrypted data
- ✅ **SDK Integration** - Uses `@fhevm-template/sdk` for encryption/decryption
- ✅ **Role-Based Access** - Owner, Regulator, Authorized Entities
- ✅ **Time-Bound Reporting** - Configurable reporting periods
- ✅ **Enterprise Security** - DoS protection, rate limiting

## Smart Contract

### Encrypted Data Types

```solidity
struct ConfidentialReport {
    euint64 encryptedAmount;           // Transaction amount
    euint32 encryptedTransactionCount; // Number of transactions
    euint8 encryptedRiskScore;        // Risk assessment (0-100)
    address submitter;
    uint256 timestamp;
    bool verified;
}
```

### Key Functions

- `submitConfidentialReport()` - Submit encrypted report
- `verifyReport()` - Regulator verifies submission
- `grantDecryptionAccess()` - Grant analyst access
- `authorizeEntity()` - Authorize reporting entity

## Using the SDK

### Installation

```bash
npm install
```

### Deployment

```bash
# Copy environment template
cp .env.example .env
# Edit .env with your values

# Compile contracts
npm run compile

# Deploy to Sepolia
npm run deploy

# Verify on Etherscan
npm run verify
```

### SDK Integration Example

```typescript
import { createFhevmClient, encryptUint64 } from '@fhevm-template/sdk';
import { ethers } from 'ethers';

// Create SDK client
const provider = new ethers.JsonRpcProvider(process.env.SEPOLIA_RPC_URL);
const signer = new ethers.Wallet(process.env.PRIVATE_KEY, provider);
const fhevmClient = await createFhevmClient({ provider, signer });

// Encrypt sensitive data
const amount = await fhevmClient.encrypt(1000000, 'uint64');
const txCount = await fhevmClient.encrypt(50, 'uint32');
const riskScore = await fhevmClient.encrypt(25, 'uint8');

// Submit to contract
const contract = new ethers.Contract(contractAddress, abi, signer);
const tx = await contract.submitConfidentialReport(
  amount.data,
  txCount.data,
  riskScore.data,
  periodId
);
await tx.wait();

// Later: Decrypt with authorization
const decrypted = await fhevmClient.userDecrypt(contractAddress, handle);
console.log('Amount:', decrypted.value);
```

## Directory Structure

```
privacy-regulatory-reporting/
├── contracts/
│   └── PrivacyRegulatoryReporting.sol  # Main contract
├── scripts/
│   ├── deploy.js      # Deployment script
│   ├── verify.js      # Etherscan verification
│   └── interact.js    # Contract interaction
├── test/
│   └── PrivacyRegulatoryReporting.test.js
├── hardhat.config.js
├── .env.example
└── README.md
```

## Testing

```bash
npm test
```

## Deployment Information

After deployment, contract details are saved to `deployments/sepolia.json`:

```json
{
  "network": "sepolia",
  "chainId": "11155111",
  "contractAddress": "0x...",
  "deployer": "0x...",
  "regulator": "0x...",
  "deploymentDate": "2025-01-15T10:00:00.000Z",
  "transactionHash": "0x...",
  "etherscanUrl": "https://sepolia.etherscan.io/address/0x..."
}
```

## SDK Benefits

This example shows how `@fhevm-template/sdk` simplifies FHE integration:

1. **Simple Encryption** - `await encrypt(value, type)` instead of manual TFHE calls
2. **Type Safety** - Full TypeScript support with type checking
3. **Error Handling** - Comprehensive error classes and messages
4. **EIP-712 Signing** - Built-in support for user decryption
5. **Framework Agnostic** - Works with any JavaScript environment

## Privacy Model

### What's Encrypted (Private)

- Transaction amounts
- Transaction counts
- Risk scores
- Aggregate computations

### What's Public

- Report existence
- Submitter address
- Timestamp
- Verification status

## Learn More

- [Main README](../../README.md) - Full SDK documentation
- [Next.js Example](../nextjs-privacy-dashboard/) - Frontend integration
- [Zama Docs](https://docs.zama.ai/fhevm) - fhEVM documentation

## License

MIT
