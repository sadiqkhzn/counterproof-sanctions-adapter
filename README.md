# Sanctions screening evaluation adapter

A Telegraph evaluation adapter for the `SANCTIONS_SCREENING_MATCH` intent family. It scores how well a miner's answer
screens the name it was asked about.

## Status

**Canonical for `SANCTIONS_SCREENING_MATCH` from 9 Nov 2026, 18:16 UTC, to 12 Nov 2026, 23:56 UTC** (the family's first Canonical; replaced by another adapter on 12 Nov).
- Telegraph WASM registration 279, tx `0x8314221dfff152714fef2a648827fc377428dc7ffc1f9435f854baaa36dfc266` (Ethereum Sepolia).
- Promoted as the first Canonical for the family at **72.41% on 29 hidden evaluation-pool pairs** (floor 70%).

## Package

| | |
|---|---|
| File | `SANCTIONS_SCREENING_MATCH.safetensors` (6,776,916 bytes, lora-v1) |
| keccak256 (registered hash) | `dfc6517c28ad384b09cc507310623f17fb6633fc645b2f5e534867ffa7e37165` |
| sha256 | `6def9c25c98f70d04ed9a4da5dd7808ae290d3273acdab7d23946f2841384385` |
| Class / intent prefix | `judgment` / `SANCTIONS_SCREENING_MATCH` |
| Foundation | `modernbert-base-lora16-v1` (`0a49b325…f8e6`) |
| Tokenizer | `67bc76f8…c456` (the node's pinned ModernBERT `tokenizer.json`) |

## Verify it

Check the file against the registered hash, then load it with Telegraph's adapter kit (`telegraph-wasm-baseline`):

```
go run ./cmd/adapterexec --package SANCTIONS_SCREENING_MATCH.safetensors --listen 127.0.0.1:8080
```

Evaluation details and known limits have been provided to the Telegraph Hackathon Season II judges.

Built for Telegraph Hackathon Season II by the Counterproof team.
