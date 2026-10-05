# Inventory reservation

A reservation is a time-limited claim on a specific quantity of a specific item, held on behalf of
one checkout. It is the only thing in the system that makes stock unavailable to someone else, and
understanding it is what makes the difference between availability numbers you can reason about and
availability numbers you merely hope are right.

The word matters because three similar-sounding things are easy to confuse. **Stock** is what
physically exists. A **reservation** is a claim against it. **Availability** is what remains after
subtracting live claims — and availability is always advisory, computed for display, never stored
as truth.

## Why claims and not decrements

The tempting model is to decrement stock when a shopper commits and increment it back if they
abandon. It is simpler by one concept, and we do not use it.

The key insight is that a decrement loses the reason. Once stock has moved from 10 to 9, nothing in
the system knows whether that unit shipped, is sitting in a checkout that will complete in thirty
seconds, or is stranded behind a checkout that died an hour ago. The three need completely different
handling, and the only way to tell them apart later is to have kept them apart in the first place.
A claim carries its own owner and its own expiry, so the stranded case is visible and repairable
rather than indistinguishable from a sale.

## Modus operandi

Availability is computed, not stored. When the catalog or the cart shows a number, that number is
stock minus the live claims against it at the moment of the request. It is correct when it is
computed and may be stale immediately afterward, and every surface that displays it treats it that
way.

A claim is created at one point only: the moment a shopper commits at checkout. The write is
conditional on sufficient availability remaining, so concurrent commitments for the last unit
resolve to exactly one winner. The loser is refused immediately, at the moment they acted, with a
reason they can act on.

From creation, a claim has a lifetime. It resolves in one of three ways. Payment completes and the
claim converts to a fulfilled allocation — stock has genuinely left. The shopper abandons or
explicitly cancels and the claim is released, returning its quantity to availability. Or nothing
happens at all, the lifetime elapses, and expiry releases it the same way cancellation would.

That third path is the one that carries operational weight. Expiry is what makes the system
self-healing when a checkout dies mid-flight, and it is also the single mechanism whose failure
silently erodes availability. When sellable quantity drifts below physical stock with no
corresponding sales, expired claims that were never released is the first hypothesis.

## Trade-offs we accept

Computing availability on read costs more than reading a stored counter, and it costs most on
exactly the items under heaviest demand. We accept it because a stored counter is a second source
of truth about stock, and reconciling two sources of truth is a worse problem than a more expensive
read.

A shopper can be shown an item as available and refused thirty seconds later at checkout. That is
the direct cost of [reserving at checkout rather than at cart entry](../adl/0001-reserve-inventory-at-checkout.md),
and it is accepted deliberately rather than worked around.

Claim lifetime is a single global duration rather than something tuned per item or per payment
method. A slow bank transfer and an instant card payment get the same window, which means the
window is set long enough for the slowest case and holds stock longer than necessary for the
fastest. Making it adaptive is deferred; it would buy a modest availability gain in exchange for a
mechanism that is much harder to reason about when it misbehaves.

## Reference

| Claim state | Availability effect | Resolved by |
|---|---|---|
| Held | Subtracted from availability | Payment, cancellation, or expiry |
| Fulfilled | Stock permanently reduced | Terminal |
| Released | Returned to availability | Terminal |

- [ADR 0001](../adl/0001-reserve-inventory-at-checkout.md) — why claims are created at checkout
- [Ordering module](../domain/ordering/README.md) — the bounded context that owns claims
- [Stuck reservation release](../runbooks/on-call/stuck-reservation-release.md) — when expiry fails
