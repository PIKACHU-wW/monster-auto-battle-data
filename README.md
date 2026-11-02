# Monster Auto Battle public game data

Official website: https://www.pikachuw.site/
JSON endpoint: https://pikachu-ww.github.io/monster-auto-battle-data/v1/snapshot.json

This repository distributes public game data, not the website or payment ledger.
GitHub Pages serves JSON for VRChat string loading on a trusted github.io host.

## Current state
Bootstrap only. Live payment reconciliation, identity binding, entitlement
delivery, ranking settlement and automatic publication are not enabled.
The initial snapshot is NOT a valid empty entitlement list.

## Consumer contract (schemaVersion 1)
- Require supported schemaVersion, state == "ready", and verified identityScheme
  before applying a future live snapshot. A revision is a non-negative integer.
- Read the whole snapshot before replacing any current state. Do not apply
  only the fields that parsed successfully. Never interpret a download, parse,
  validation failure or not_initialized state as revocation.
- Retain the last valid snapshot in the current session on failure and retry
  with backoff. Show freshness using publishedAt/dataAsOf (UTC ISO-8601).
- Reject older revisions; equal revisions are unchanged. Publication errors
  must not generate a replacement empty list.
- ranking.entries, supporters and entitlements share one snapshot revision.
  Player-entry schema and identity verification must be finalized before state
  can become ready. Do not match payment identities by unverified display name.
- Early Supporter is for any eligible purchase/blessing during testing, with
  no recipient limit; the earned title remains after testing.
- Only the final top 50 receive the permanent red Dragon-Blessed title and
  all VIP benefits, future content and maps. Current rank does not grant this.
- settlement.winners is the frozen final result, not the live top 50.
- Do not merge USD and CNY numerically. currencyPolicy remains null until
  an explicit cross-currency ranking policy has been approved.
- Future public rows contain only reviewed display/identity keys, game item
  flags, title flags and public ranking values. No emails, payment account
  names, platform transaction IDs, webhook bodies or credentials.

## Publication
The server remains authoritative. It must generate an allowlisted snapshot
only from verified ledger records, validate it, then atomically update this
single JSON file. GitHub failure must not block payment ingestion. This
bootstrap script intentionally cannot publish live records or reset a live
snapshot. GitHub history is public: never commit private data, even briefly.

## Files
- v1/snapshot.json: one request for a coherent snapshot.
- .nojekyll: serve data files directly.
- index.html: redirects people to the official website; no website mirror.
