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
- Dragon’s Kin is for any eligible purchase/blessing during testing, with
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
### Read-only world leaderboard contract
Current roster source: supporters, not ranking.entries. Ready snapshots require
schemaVersion=1, state="ready", increasing integer revision and UTC dataAsOf.
Each supporter has unique playerKey, displayName and positive integer
amountUsdMinor, sorted descending. The server resolves ties by earlier totals.
During not_settled, the first 50 supporters display Dragon-Blessed; all later
supporters display purple Dragon's Kin. After finalized, settlement.winners
freezes the final winner set (at most 50 unique supporter keys).
These are display titles, NEVER permanent VIP entitlement grants.

For world head-card identity, require identityScheme=
"verified_vrc_display_name_v1" and an individually reviewed vrcDisplayName on
that supporter row. Names must be unique and match the current VRChat display
name exactly. Do not promote payment remarks or parsed_unverified identities.
A missing verified name allows roster display but no world title assignment.
World and web refresh about once per minute, retaining valid data on errors.
Live payment-to-verified-publication automation is still not connected;
the public bootstrap remains not_initialized and contains no real players.
Permanent VIP delivery and the separate memorial panel are future work.

### Server publisher
The server remains authoritative. It must generate an allowlisted snapshot
only from verified ledger records, validate it, then atomically update this
single JSON file. GitHub failure must not block payment ingestion. This
bootstrap script intentionally cannot publish live records or reset a live
snapshot. GitHub history is public: never commit private data, even briefly.

## Files
- v1/snapshot.json: one request for a coherent snapshot.
- .nojekyll: serve data files directly.
- index.html: redirects people to the official website; no website mirror.
