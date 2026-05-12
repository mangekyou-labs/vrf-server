# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Kamui is a Verifiable Random Function (VRF) system using ECVRF cryptography on Solana. VRF oracle monitors Solana for requests, generates verifiable proofs, and fulfills them on-chain.

## Monorepo Structure

```
kamui-program/           Solana Anchor programs
├── programs/
│   ├── kamui-vrf/           Core VRF program
│   └── kamui-vrf-consumer/  Example consumer program
mangekyou -> packages/mangekyou/mangekyou/     ECVRF crypto library (symlink)
mangekyou-cli -> packages/mangekyou/mangekyou-cli/
mangekyou-derive -> packages/mangekyou/mangekyou-derive/
```

## Build Commands

### Solana Programs
```bash
cd kamui-program
anchor build                    # Build programs
anchor test                     # Run tests
anchor test --provider.cluster localnet
```

### Mangekyou (cryptographic library)
```bash
cd mangekyou
cargo build
cargo test
cargo bench
cargo run --bin ecvrf-cli keygen
cargo run --bin ecvrf-cli prove --input <hex> --secret-key <key>
cargo run --bin ecvrf-cli verify --output <hex> --proof <hex> --public-key <key> --input <hex>
```

## Program Addresses

| Program | Localnet | Devnet |
|---------|----------|--------|
| kamui_vrf | 4zxDQnSVK6XPTERb8kY8b7EQsHWbwrRFfaDunF9Ryjg1 | 8xBh8yMwNJG42fcmj1MvZRrSV3w8NwH3J9c3xPjF5Vgd |
| kamui_vrf_consumer | 4qqRVYJAeBynm2yTydBkTJ9wVay3CrUfZ7gf9chtWS5Y | DH3mVRXoyCUmmDmcf9a7AkEU99UEomvg2jq4X2boga6e |

## Architecture

**VRF Flow:**
1. User creates randomness request via subscription account
2. VRF oracle monitors Solana, generates ECVRF proof
3. Proof stored on-chain, consumer program retrieves result

**Key files:**
- `kamui-program/programs/kamui-vrf/src/lib.rs` - Core VRF instruction handlers
- `kamui-program/programs/kamui-vrf/src/processor.rs` - VRF logic
- `mangekyou/src/kamui_vrf.rs` - ECVRF proof generation
- `kamui-program/programs/kamui-vrf/src/state.rs` - State accounts (Subscription, Request, VRFResult)

## Dependencies

- **Anchor**: v0.31.1
- **Solana**: v3.1.13 (toolchain), CLI matches
- **Rust**: 1.86.0 (host), 1.89.0 (SBF build)