# Everstake Landing Client

Ultra-low latency Solana transaction submission with multi-path routing.

Everstake Landing is a low-latency transaction relay that supports two submission modes:
- **RPC**: Submit signed Solana transactions via a standard JSON-RPC interface.
- **QUIC**: Submit Solana transactions via a direct, low-latency QUIC connection.

**Multi-path routing:** Your transaction is submitted across all pathways (QUIC, Jito, Harmonic, Rakurai, etc.) simultaneously, maximizing the chance it lands on-chain.

This repository documents both modes and provides runnable examples for each.

---

## Priority inclusion

Tipping controls how your transaction is routed and prioritized for landing on-chain:

- **With a tip**: Adding a tip instruction (a `SystemProgram::transfer` to one of the [Tip Payment Accounts](RESOURCES.md)) prioritizes your transaction for delivery to current and upcoming leaders, increasing its landing rate.
- **Without a tip(prepaid subscription customers)**: Your transaction is still forwarded, but routed via SWQoS only (single path), with no multi-path fanout and no priority boost.


See [Min Lamports](RESOURCES.md) for the minimum tip amount and [Tip Payment Accounts](RESOURCES.md) for the destination Pubkeys.

---

## RPC Path

### What is it?
The Everstake Landing RPC endpoint is a proxy for sending signed Solana transactions. It speaks standard Solana JSON-RPC and forwards your transactions to current and future leaders for reliable delivery.

**Key points:**
- No API key required.
- Your transaction must include a “transfer” instruction with [Min Lamports](RESOURCES.md) to one of these Pubkeys: [Tip Payment Accounts](RESOURCES.md). Without this instruction, your transaction will be dropped.
- Please do not add the tip address to the AddressLookupTable.
- The endpoint for the Everstake Landing RPC is listed in [RPC Endpoints](RESOURCES.md).
- **No preflight checks**: Everstake Landing does not perform preflight simulation — transactions are forwarded as-is. Ensure your transaction is valid before submitting.
- **Default rate limit**: 10 transactions per second (TPS) per client.
- **Memo (optional)**: You can attach a memo to your transaction to include an invoice number, order ID, or any custom reference. Memos are permanently recorded on-chain and visible in transaction logs and explorers, making it easy to reconcile payments with your internal systems. See [Payment with Memo](https://solana.com/docs/payments/send-payments/payment-with-memo) and the [rpc.rs](src/bin/rpc.rs) example for details.

**Connection & Performance:**
- Both HTTP/1.1 and HTTP/2 use **persistent connections** by default — the TCP connection stays open and is reused for subsequent requests, eliminating handshake overhead.
- Our endpoints support **HTTP/2**, which provides multiplexing (multiple requests over a single connection) and header compression for better performance.
- For lowest latency over HTTP, use `http://` with HTTP/2 prior knowledge (h2c). See [rpc.rs](src/bin/rpc.rs) for an example.
- HTTPS endpoints also support HTTP/2 (negotiated via ALPN) with minimal encryption overhead.

### How it works
1. You build and sign a Solana transaction in your client.
2. You include a tip instruction (a simple `SystemProgram::transfer`) paying lamports to a designated Everstake Landing tip account [Tip Payment Accounts](RESOURCES.md).
3. You submit the transaction via a standard `sendTransaction` call to the Everstake Landing RPC endpoint [RPC Endpoints](RESOURCES.md).
4. Everstake Landing forwards the transaction for fast, reliable delivery.

### Quick start: Transaction example (Rust)
This repo includes a minimal Rust binary (`rpc.rs`) that:
- Loads your keypair.
- Builds a transaction that tips the Everstake Landing tip account and transfers 1000 lamports to yourself.
- Sends the transaction via an RPC URL.

**Run the RPC example:**
```bash
cargo run --bin rpc
```

**Expected output:**
- `Transaction with signature: "your tx signature" was sent successfully`

**Important:**
- For production use, you must set the real Everstake Landing RPC endpoint and the real tip Pubkey in the example code.

---

## QUIC Path

### What is it?
The Everstake Landing QUIC endpoint offers a direct, low-latency path for submitting signed Solana transactions. It uses the QUIC protocol to stream transactions directly to Everstake Landing relay, which then forward them to network leaders. This path is ideal for latency-sensitive applications.

**Key points:**
- No API key required.
- The QUIC path provides lower latency compared to RPC.
- You must authorize your pubkey to connect to Everstake Landing via Quic.
- **Tipping (optional):** Add a tip to prioritize your transaction and speed up its landing — see [Priority inclusion](#priority-inclusion).
- **Keep-Alive:** The connection has a keep-alive interval of 10 seconds.

**Connection Rate Limit:** Each authorized client is limited to 8 QUIC connections per minute. Please ensure your integration respects this limit to avoid connection errors or temporary blocks.

### How it works
1. You build and sign a Solana transaction in your client.
2. You establish a QUIC connection to an [Everstake Landing QUIC](RESOURCES.md) endpoint after we whitelist your pubkey. 
3. You send the raw, serialized transaction over the QUIC stream.
4. Everstake Landing forwards the transaction for fast, reliable delivery.

### Quick start: Transaction example (Rust)
This repo includes a minimal Rust binary (`quic.rs`) that demonstrates the QUIC path:
- Loads your keypair.
- Builds a simple transaction.
- Sends the transaction via a QUIC connection.

**Run the QUIC example:**
```bash
cargo run --bin quic
```

**Important:**
- The RPC node must include a special flag to send all transactions to a single leader instead of following the leader schedule:
  --rpc-send-transaction-tpu-peer `SocketAddr`, where `SocketAddr` - [Everstake Landing QUIC endpoint](RESOURCES.md)
  For RPC version 2.3 and above the --use-connection-cache flag must also be specified, since the new TPU client is not compatible with the Landing connection.
- For production use, you must configure the correct Everstake Landing QUIC endpoint in the example code.
- You must authorize your pubkey to connect to Everstake Landing via Quic. 
