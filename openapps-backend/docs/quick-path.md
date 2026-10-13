# Quick path

The five most common builds, one target each. Open exactly the page named; pull the [cheat sheet](policy-cheat-sheet.md) for syntax and stop there unless the task outgrows it.

| Building | Open exactly | You will also need |
|---|---|---|
| CRUD app with owners (todos, notes, profiles) | [access patterns](access-patterns.md#owner-only) | [cheat sheet](policy-cheat-sheet.md) |
| Social/content app (posts, comments, votes, feeds) | [worked examples](policy-examples.md) | [trending feeds](trending-feeds.md) if ranked - that `windowSum` pattern is offchain-only; an onchain feed counts vote documents in a subcollection and ranks client-side |
| Marketplace or orders with money caps | [marketplace example](examples/marketplace.md) | [invariants](invariants.md) for the caps |
| Escrow, vault, or any onchain funds custody | [escrow example](../../openapps-onchain/docs/examples/escrow.md) | [custody and PDAs](../../openapps-onchain/docs/custody-and-pdas.md) |
| Token launch or trading | [onchain examples index](../../openapps-onchain/docs/examples.md) | [plugin catalog](../../openapps-onchain/docs/plugins.md) for exact signatures |

Anything else: the full [openapps-backend router](../SKILL.md#reference-router) or the [openapps-onchain router](../../openapps-onchain/SKILL.md#reference-router) maps every task to one page.
