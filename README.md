# Sanctions screening evaluation adapter

A Telegraph evaluation adapter for the `SANCTIONS_SCREENING_MATCH` intent family (today: `SANCTIONS_SCREENING_MATCH_GENERAL`).
It scores how well a miner's answer screens the name it was asked about. Its main job is to rank a real screening above
an answer that has nothing to do with the question. Under the GENERIC fallback adapter, a miner serving a static
dataset-metadata file scores about as high as a miner that actually screens the name.

## Package

| | |
|---|---|
| File | `SANCTIONS_SCREENING_MATCH.safetensors` (6,776,916 bytes, lora-v1) |
| keccak256 (registered hash) | `dfc6517c28ad384b09cc507310623f17fb6633fc645b2f5e534867ffa7e37165` |
| sha256 | `6def9c25c98f70d04ed9a4da5dd7808ae290d3273acdab7d23946f2841384385` |
| Class / intent prefix | `judgment` / `SANCTIONS_SCREENING_MATCH` |
| Foundation | `modernbert-base-lora16-v1` (`0a49b325…f8e6`) |
| Tokenizer | `67bc76f8…c456` (the node's pinned ModernBERT `tokenizer.json`) |
| LoRA | rank 8, alpha 32, on `Wqkv`, `Wo`, `Wi`, with a one-logit reward head |
| `max_tokens` | 160 |

The class is `judgment` because the network's intent catalog (`GET /engine/v1/intents/SANCTIONS_SCREENING_MATCH`) lists
the family as `judgment`. The kit's offline catalog falls back to `hybrid` for intents it cannot look up.

## Training data

Built with Telegraph's adapter kit (`float_runtime.train_adapter`) from 2,860 preference pairs. Each pair has a question
in the evaluation pool's style, a better answer and a worse answer.

- **Truth comes from the official lists.** OFAC SDN and consolidated, the UN Security Council list, the EU financial
  sanctions file and the UK sanctions list. Each subject was labelled `listed`, `unlisted` or `namesake` (same name,
  but a date of birth that contradicts the listing).
- **Worse answers:**
  - claims a match that isn't there;
  - ignores a contradicting date of birth;
  - cites an unrelated entry;
  - says a listed party is clear.
- **Answer shapes:** prose and JSON, balanced, so neither shape wins by itself.
- **Splits:** train, validation and test are split by subject, so no person or company appears in more than one.
- **Tokenizer:** training used the Hugging Face cache copy of ModernBERT's `tokenizer.json`. It differs from the pinned
  copy only in how the BPE merges are written. The vocabulary is the same, and the token ids are identical on all
  8,788 training and test texts and on the kit's 88 vectors.

## Results

All numbers come from the validators' own runtime: the kit's `adapterexec` on the released foundation and engine. That
is the scorer the network uses. A PyTorch re-implementation gives different logits; those numbers are not reported here.

| Test (held out, never trained on) | This adapter | GENERIC |
|---|---|---|
| Our evaluation-pool rows (112 attested pairs, subjects unseen in training) | **97.3%** | see below |
| Real network answers: genuine screening vs static-metadata miner (144 pairs, 6 static miners × 2 genuine miners × 12 questions) | **91.0%** | — |
| …when the genuine answer is zk-sanctions' | 100% | — |
| …when the genuine answer is Counterproof's | 81.9% | — |

Absolute scores under GENERIC through the same runtime, on the same 12 questions:

| Answer source | GENERIC score |
|---|---|
| static file (san-opensanc-stats) | 0.9992 |
| genuine screening (Counterproof) | 0.9967 |
| static file (san-os-au) | 0.9690 |
| genuine screening (zk-sanctions) | 0.9650 |

The 0.9992 for a static file that ignores the question matches its live leaderboard score (0.9993 in epoch 2048).

**Conflict of interest.** The author runs a sanctions miner (`counterproof-sanctions-screener`). The adapter does not
favour that miner's format: on the same questions it ranks zk-sanctions' genuine answers above the static files more
often than ours.

## Known limits

A text model can't know who is on a sanctions list. Where the only difference between two answers is whether a listing
claim is true, it does little better than chance:

- **Invented exact listing** (the worse answer says the subject is listed under its own exact name): 37% on a test set
  in other writing styles. This is the only number here measured with PyTorch rather than the validators' runtime.
- **Always "match"** (an answer that echoes the name back as listed): 25% on 12 real questions, with the answer citing no
  actual list entry.

Settling those claims needs a truth source, which a hybrid adapter could add. Elsewhere, answers saying "clear" to every
question were caught 83–100% of the time, and keyword-stuffed JSON (`"matched": true, "listed": true` with no name) was
caught in all 12 cases.

## Using it

Hosted at its pinned commit, so the bytes never change:

```
https://raw.githubusercontent.com/sadiqkhzn/counterproof-sanctions-adapter/<commit>/SANCTIONS_SCREENING_MATCH.safetensors
```

Check it yourself with the kit (`telegraph-wasm-baseline`):

```
go run ./cmd/adapterexec --package SANCTIONS_SCREENING_MATCH.safetensors --listen 127.0.0.1:8080
```
