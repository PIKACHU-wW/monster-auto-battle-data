# Monster Auto Battle public data

Public distribution repository for game entitlement and ranking data.

## Current status

Repository initialization only. Live payments, game entitlement delivery,
ranking settlement, and automated publication are not enabled yet.
No live entitlement files are published: missing data must not be interpreted
as a command to revoke existing entitlements.

## Data boundaries

- Publish only reviewed public game data.
- Never commit payment tokens, payer emails, raw orders, or private ledgers.
- Keep scene and wand ownership separate.
- Keep USD and CNY separate until a conversion policy is approved.
- Test fixtures must never be published as production entitlements.

Payment processing and the private ledger run outside this repository.
