
# Saros SDK Integration Guide  
Unlock DeFi with Saros—AMM, DLMM, Staking, and Farming on Solana

***

## Table of Contents  
1. Quick Start Guide  
2. Environment Setup  
3. SDK Selection & Feature Comparison  
4. Integration Tutorials  
5. Working Code Examples  
6. API Reference  
7. Troubleshooting & FAQ  
8. Advanced Use Cases  
9. Visual Aids  

***

## 1. Quick Start Guide

**Supported SDKs**

- @saros-finance/sdk (TypeScript; AMM, Stake, Farm)
- @saros-finance/dlmm-sdk (TypeScript; DLMM)
- saros-dlmm-sdk-rs (Rust; DLMM)

**Install**

```
npm install @saros-finance/sdk @saros-finance/dlmm-sdk
```

```
cargo add saros-dlmm-sdk-rs
```

***

## 2. Environment Setup

- Solana CLI: [Install Guide](https://docs.solana.com/cli/install-solana-cli-tools)
- Wallet: Phantom/Solflare (Devnet or Mainnet)
- Set Network

```typescript
import { Connection } from "@solana/web3.js";
const connection = new Connection("https://api.devnet.solana.com");
// Mainnet: "https://api.mainnet-beta.solana.com"
```

***

## 3. SDK Selection & Feature Comparison

| Feature         | @saros-finance/sdk | @saros-finance/dlmm-sdk | saros-dlmm-sdk-rs |
|-----------------|:------------------:|:-----------------------:|:-----------------:|
| Language        | TypeScript         | TypeScript              | Rust              |
| AMM/Swap        | ✅                 | ❌                      | ❌                |
| DLMM Pools      | ✅                 | ✅                      | ✅                |
| Staking         | ✅                 | ❌                      | ❌                |
| Farming         | ✅                 | ❌                      | ❌                |

*Tip: Use @saros-finance/sdk for general DeFi apps, DLMM SDKs for advanced pool logic, and Rust for anchor-based/Solana-native projects.*

***

## 4. Integration Tutorials

### Tutorial 1: Token Swap

```typescript
import { SarosSDK, SwapParams } from '@saros-finance/sdk';

const saros = new SarosSDK({ connection, wallet });

const swapParams: SwapParams = {
  inputMint: "So11111111111111111111111111111111111111112", // SOL
  outputMint: "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v", // USDC
  amount: 1000000000, // 1 SOL
  slippage: 1 // %
};

async function executeSwap() {
  try {
    const transaction = await saros.swap(swapParams);
    const signature = await connection.sendTransaction(transaction);
    console.log("Swap success:", signature);
  } catch (error) {
    // Error handling below
  }
}
```

***

### Tutorial 2: Liquidity Pool Management

```typescript
import { DLMM } from '@saros-finance/dlmm-sdk';

const dlmm = new DLMM({ connection, wallet });

const poolParams = {
  tokenA: "So11111111111111111111111111111111111111112",
  tokenB: "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
  initialPrice: 100,
  feeRate: 0.003
};

async function createPool() {
  const tx = await dlmm.createPool(poolParams);
  return await connection.sendTransaction(tx);
}
```

***

### Tutorial 3: Staking Operations

```typescript
const stakeParams = {
  poolAddress: "YOUR_STAKE_POOL_ADDRESS",
  amount: 1000000000, // 1 SOL
  duration: 30 // days
};

async function stakeTokens() {
  const tx = await saros.stake(stakeParams);
  return await connection.sendTransaction(tx);
}

// Claim rewards
async function claimRewards() {
  const rewards = await saros.getStakeRewards(wallet.publicKey);
  if (rewards > 0) {
    const tx = await saros.claimRewards();
    return await connection.sendTransaction(tx);
  }
}
```

***

## 5. Working Code Examples

### Swap Component Example (React + TypeScript)

```typescript
import React, { useState } from "react";
import { SarosSDK } from "@saros-finance/sdk";
// ...full swap logic from Tutorial 1...
```

***

### DLMM Pool Creation (TypeScript)

```typescript
// See pool management tutorial above
```

***

### Rust DLMM Example

```rust
use saros_dlmm_sdk_rs::{DlmmPool, PoolManager};

let pool_manager = PoolManager::new("https://api.devnet.solana.com")?;
let tx = pool_manager.create_position(&pool_pubkey, 10, 20, 1_000_000).await?;
```

***

## 6. API Reference (TypeScript overview)

- SarosSDK
    - swap(params): Transaction
    - createPool(params): Transaction
    - addLiquidity(params): Transaction
    - removeLiquidity(params): Transaction
    - stake(params): Transaction
    - unstake(params): Transaction
    - claimRewards(): Transaction
    - getAllPools(): Pool[]
    - getUserStakes(pubkey): Stake[]

- DLMM SDK
    - createPool(params): Transaction
    - addLiquidity(params): Transaction
    - getPoolInfo(address): PoolInfo

See: [Saros Docs](https://docs.saros.xyz/integration) for full details.

***

## 7. Troubleshooting & FAQ

**Common Problems**
- Insufficient Balance: Check wallet and input amount.
- Slippage Errors: Increase tolerance.
- Transaction Fails: Check Solana program logs.
- SDK Version Errors: Update dependencies.

**FAQ**
- Which network for testing? Devnet recommended; use mainnet for production.
- Compatible with React? Yes.
- Support? [Saros Dev Station Telegram](https://t.me/+DLLPYFzvTzJmNTJh)

***

## 8. Advanced Use Cases

- Batch Transactions
```typescript
await saros.batchExecute([
  { type: 'swap', params: swapParams }, 
  { type: 'addLiquidity', params: liquidityParams }
]);
```

- Live Price Monitoring
```typescript
const priceStream = saros.subscribeToPriceUpdates(poolAddress);
priceStream.on('update', (price) => updateUI(price));
```

***
