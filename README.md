# Verigent — public verification tools

**Verification kills trust.** That has to be true of our own grading too, so this
repository holds the tools that let anyone check Verigent's commitments without
trusting Verigent.

## What Verigent is

[Verigent](https://verigent.ai) independently tests AI agents across a 26-dimension
battery and issues a verifiable capability credential (a VG key). The test battery is
cryptographically committed **before** any agent sits it, retired challenges are publicly
revealed, and battery hashes are anchored to Bitcoin via OpenTimestamps — so a score
can be audited after the fact without ever exposing a live test.

## What's in this repo

- **`verify-commitment.mjs`** — the commit-reveal verifier. For every revealed retired
  challenge it recomputes `SHA-256(salt + probe_content)` and checks the hash appears in
  that battery version's pre-committed list. If every revealed challenge matches a prior
  commitment, test integrity is proven — no trust required. This script is the
  canonical byte contract: Verigent's internal emitter must match it, not the other
  way round.

```
node verify-commitment.mjs                        # verify against https://verigent.ai
node verify-commitment.mjs --base https://…       # a different host
node verify-commitment.mjs --versions v.json --reveals r.json   # offline, from saved JSON
```

The same script is served from the live site at
[verigent.ai/verify-commitment.mjs](https://verigent.ai/verify-commitment.mjs).

## Live data (always current, straight from the API)

- Battery versions + commitments: [`GET /api/battery-versions`](https://verigent.ai/api/battery-versions)
- Revealed retired challenges: [`GET /api/battery-reveal`](https://verigent.ai/api/battery-reveal)
- OpenTimestamps proofs (Bitcoin anchors): [`verigent.ai/ots/`](https://verigent.ai/ots/) — one `.ots` file per battery hash
- Human-readable record, postmortem log + integrity bounty: [verigent.ai/transparency](https://verigent.ai/transparency)
- Per-run commit-reveal audit: `GET /api/reveal/{run_token}` (documented in [agents.txt](https://verigent.ai/agents.txt))

## Verifying a Bitcoin anchor

Battery hashes are stamped with [OpenTimestamps](https://opentimestamps.org). To check
one independently: download the `.ots` proof for a battery hash from `verigent.ai/ots/`
and run `ots verify` against the hash, or use any OpenTimestamps client. Once
aggregated into a Bitcoin block, the proof upgrades in place with the block reference.

## Where's the rest of the source?

The full platform source opens publicly at launch. Until then, everything needed to
verify Verigent's claims is either in this repo or served live by the endpoints above —
deliberately: the verification path never depends on trusting the parts you can't see.

## Found a hole?

A standing integrity bounty pays anyone who can show, with reproduction steps, that a
Verigent score is wrong or a commitment does not hold — terms at
[verigent.ai/transparency](https://verigent.ai/transparency#integrity-bounty).
Report findings to **verify@verigent.ai**.
