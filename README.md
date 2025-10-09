# EverSender Client (RPC)

EverSender is a low-latency transaction relay that supports two submission modes:
- RPC: Submit signed Solana transactions via JSON-RPC (this document).
- QUIC: Submit transactions via a QUIC-based path (not covered here).

This repository documents the RPC mode and provides a runnable example.

## What is it?

The EverSender RPC endpoint is a proxy for sending signed Solana transactions. It speaks standard Solana JSON-RPC and forwards your transactions to current and future leaders.

Key points:
 - No API key required.
 - Your transaction must include a “transfer” instruction with [Min Lamports](RESOURCES.md) to one of these Pubkeys: [Tip Payment Accounts](RESOURCES.md). Without this instruction your transaction will be skipped.
 - Endpoint for the EverSender RPC is [RPC Endpoints](RESOURCES.md).


## How it works (RPC path)
1. You build and sign a Solana transaction in your client.
2. You include a tip instruction (a simple `SystemProgram::transfer`) paying lamports to the designated EverSender tip account: [Tip Payment Accounts](RESOURCES.md).
3. You submit the transaction via `sendTransaction` to the EverSender RPC endpoint [RPC Endpoints](RESOURCES.md).
4. The EverSender forwards the transaction for fast, reliable delivery.

## Quick start: Transaction example (Rust)

This repo includes a minimal Rust binary that:
- Loads your keypair.
- Builds a transaction that tips the EverSender tip account and transfers 1000 lamport to yourself.
- Sends the transaction via an RPC URL.

Run:
```cargo run```

Expected output:
- Transaction with signature: "your tx signature" was sent successfully

Important:
- In production, you must set the real EverSender RPC endpoint and the real tip Pubkey.

