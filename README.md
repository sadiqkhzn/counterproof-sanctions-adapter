# Sanctions screening evaluation adapter

A Telegraph evaluation adapter for the `SANCTIONS_SCREENING_MATCH` intent family. It scores how well a miner's
answer screens the name it was asked about, rewarding correct listed/not-listed verdicts and correct list,
programme and identifier detail over confident but wrong answers.

## Package

| | |
|---|---|
| File | `SANCTIONS_SCREENING_MATCH.safetensors` (6,776,916 bytes, lora-v1) |
| keccak256 (registered hash) | `d5c72f052a0b53fcd09e3f590d523388b707ea3ed58dbbeedbab8ec4a273dbcc` |
| sha256 | `0fff78eceb8b4004cbd370054f85aee84d128ae85a7a947bec6c757741edd414` |
| Class / intent prefix | `hybrid` / `SANCTIONS_SCREENING_MATCH` |
| Foundation | `modernbert-base-lora16-v1` (`0a49b325…f8e6`) |
| Tokenizer | `67bc76f8…c456` (the node's pinned ModernBERT `tokenizer.json`) |

## Verify it

Check the file against the registered hash, then load it with Telegraph's adapter kit (`telegraph-wasm-baseline`):

```
go run ./cmd/adapterexec --package SANCTIONS_SCREENING_MATCH.safetensors --listen 127.0.0.1:8080
```

Evaluation details and known limits have been provided to the Telegraph Hackathon Season II judges.

Built for Telegraph Hackathon Season II by the Counterproof team.
