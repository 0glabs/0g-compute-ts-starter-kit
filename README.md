# 0G Compute TypeScript SDK Starter Kit

This is a starter kit for interacting with the 0G Compute Network, which provides decentralized AI services. This kit demonstrates how to use the 0G Compute TypeScript SDK to access AI services securely on the network.

## Repository Branches

### Main Branch
REST API implementation using Express framework with Swagger documentation.
```bash
git checkout main
```

- Features:
  - RESTful endpoints for AI services
  - Swagger UI for API testing and documentation
  - TypeScript implementation for type safety
  - Automatic ledger initialization at startup

### CLI Branch (Current)
CLI implementation using Node.js and Commander.
```bash
git checkout cli-version
```

- Features:
  - CLI implementation for AI services
  - TypeScript implementation for type safety


## Features

- Command-line interface with options for AI service operations
- Initialize wallet with private key and connect to the network
- Create accounts and deposit funds
- Check account balance
- List available AI services
- Query AI services with proper authentication
- Automatic payment processing with fallback fee option
- Configuration options for different network environments

## Prerequisites

- Node.js (v14 or higher)
- npm or yarn
- An Ethereum wallet with a private key
- Some testnet ETH for the 0G Compute Network testnet

## Installation

1. Clone the repository:
```bash
git clone https://github.com/yourusername/0g-compute-starter-kit.git
cd 0g-compute-starter-kit
```

2. Install dependencies:
```bash
npm install
# or
yarn install
```

3. Create a `.env` file with your private key (optional, can also be passed as a flag):
```
PRIVATE_KEY=your_private_key_here
```

4. Build the project:
```bash
npm run build
# or
yarn build
```

## Core Components

This starter kit uses the 0G Compute Network SDK to interact with decentralized AI services:

```typescript
import { ethers } from 'ethers';
import { createZGComputeNetworkBroker } from '@0glabs/0g-serving-broker-sdk';
```

## Account Setup Process

### 1. Initialize Wallet and Broker

```typescript
// Initialize wallet with provider
const wallet = new ethers.Wallet(privateKey, provider);

// Create broker with wallet
const broker = await createZGComputeNetworkBroker(wallet);
```

This sets up:
- Ethereum wallet for transaction signing
- Connection to the 0G Compute Network through the broker

### 2. Deposit Funds
```typescript
try {
  const tx = await broker.inference.depositFee(amount);
  // Transaction complete - tx.hash contains the transaction hash
} catch (error) {
  // Handle deposit error
}
```

### 3. Add Funds to Ledger
```typescript
try {
  const tx = await broker.inference.addFundsToLedger(amount);
  // Transaction complete - tx.hash contains the transaction hash
} catch (error) {
  // Handle ledger funding error
}
```

### 4. Balance Check
```typescript
try {
  const balance = await broker.inference.getBalance();
  // Use balance value
} catch (error) {
  // Handle balance check error
}
```

## Service Query Process

### 1. Service Discovery
```typescript
try {
  const services = await broker.inference.listServices();
  // Process services array
  // Each service has: model, provider, serviceType, url properties
} catch (error) {
  // Handle service listing error
}
```

### 2. Manual Fee Settlement
```typescript
try {
  await broker.inference.settleFee(providerAddress, fee);
  // Fee settled successfully
} catch (error) {
  // Handle fee settlement error
}
```

### 3. Service Query
```typescript
try {
  // Get service metadata
  const { endpoint, model } = await broker.inference.getServiceMetadata(providerAddress);
  
  // Get authentication headers
  const headers = await broker.inference.getRequestHeaders(providerAddress, query);
  
  // Send request to provider
  const response = await fetch(endpoint, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      ...headers
    },
    body: JSON.stringify({ prompt: query })
  });
  
  if (!response.ok) {
    throw new Error(`HTTP error! Status: ${response.status}`);
  }
  
  const result = await response.json();
  
  // Process fee payment
  await broker.inference.processResponse(providerAddress, response.headers);
  
  return result;
  
} catch (error) {
  // Handle fallback fee if nonce-related error
  if (fallbackFee && error.message.includes('nonce')) {
    await broker.inference.settleFee(providerAddress, fallbackFee);
  }
  // Handle other errors
}
```

## Usage

The CLI supports various command options:

```bash
npm start -- [options] [command]
```

### Global Options

| Option | Description | Example |
|--------|-------------|---------|
| `-k`, `--key` | Ethereum private key (required) | `-k 0x123...` |
| `-h`, `--help` | Display help for command | `-h` |
| `-V`, `--version` | Output the version number | `-V` |

### Commands

| Command | Description | Example |
|---------|-------------|---------|
| `list` | List available AI services | `list` |
| `deposit <amount>` | Deposit funds to your account | `deposit 0.1` |
| `balance` | Check your current balance | `balance` |
| `query <providerAddress>` | Query an AI service | `query 0x123...` |
| `settle <providerAddress> <amount>` | Manually settle a fee | `settle 0x123... 0.01` |
| `config` | Show current configuration | `config` |

### Query Options

| Option | Description | Example |
|--------|-------------|---------|
| `-q`, `--queryText <text>` | Query text to send to the model | `-q "Hello AI"` |
| `-f`, `--fallbackFee <amount>` | Fallback fee if automatic processing fails | `-f 0.01` |

### Example Commands

List available services:
```bash
npm start -- -k YOUR_PRIVATE_KEY list
# Output:
# Available Services:
# Provider: 0xabc... - Model: GPT-4 Compatible
# Provider: 0xdef... - Model: Llama 2 Compatible
```

Deposit funds:
```bash
npm start -- -k YOUR_PRIVATE_KEY deposit 0.1
# Output:
# Funds deposited successfully
```


Query an AI service:
```bash
npm start -- -k YOUR_PRIVATE_KEY query 0x123...
# Output:
# Query sent successfully
```

Manually settle a fee:
```bash
npm start -- -k YOUR_PRIVATE_KEY settle 0x123... 0.01
# Output:
# Fee settled successfully
```

Check balance:
```bash
npm start -- -k YOUR_PRIVATE_KEY balance
```

Contribute to the project:
1. Fork the repository
2. Create a new branch
3. Make your changes and commit them
4. Push your changes to your fork
5. Create a pull request

