# Ordering

Ordering owns the path from a committed shopper to a paid order, and it owns the claims on
inventory that path creates. If something in the system makes stock unavailable to another shopper,
this module did it.

## What this module owns

The checkout transition is the module's core responsibility: taking a cart, deciding whether the
requested quantities can actually be claimed, and creating the claims if they can. That decision is
the single authoritative point at which overselling is prevented, and it lives here by design — no
other module is permitted to form its own opinion about whether stock is available.

Everything that follows from a claim is also ours. Claims have a lifetime, and ordering is
responsible for resolving them: converting them when payment completes, releasing them when a
shopper abandons, and expiring them when a checkout simply stops. Expiry is the module's quietest
and most consequential job, because it is what keeps availability honest when checkouts die
mid-flight.

Order state after payment belongs here too — an order exists, is paid, and has a fulfilment
request. The order is the record of what was sold.

## Boundaries

**Catalog owns stock.** Physical quantity on hand is not ours. We read it, we claim against it, and
we never write it. When stock changes because a pallet arrived or a count was corrected, catalog
made that change.

**Catalog owns displayed availability.** The number a shopper sees comes from catalog, computed as
stock minus our live claims. We publish the claims; catalog does the arithmetic and presents it.
This is deliberate: it keeps a single surface responsible for what shoppers are shown, and it keeps
us out of presentation.

**Payments owns money.** We ask for an authorization and we react to the outcome. Card details,
retries, refunds, and settlement are never ours, and no part of our claim logic depends on how a
payment was taken.

**Fulfilment owns the physical world.** Once an order is paid, we hand off a fulfilment request.
Picking, shipping, and delivery exceptions are theirs. A delivery failure does not reopen one of
our claims — it opens a return, which is their flow and then payments'.

## How it behaves today

A checkout arrives carrying the items and quantities a shopper has committed to. We evaluate
availability for each line and attempt the claims as one unit of work: either every line is claimed
or none is, because a partially claimed order is not something a shopper agreed to buy. Where
availability falls short, the checkout is refused immediately with the lines that failed, so the
shopper learns the truth at the moment they acted.

With claims in place, we request payment authorization. Success converts the claims and the order
becomes paid; a fulfilment request goes out and the module's work on that order is done. Failure
releases the claims and the shopper is returned to their cart with the items intact — losing a
claim is not the same as losing a cart.

A checkout that neither succeeds nor fails is the case that matters most. Those claims sit until
their lifetime elapses, and expiry releases them. This runs continuously and is the only thing
standing between a dead checkout and permanently stranded stock.

## Decisions that constrain this module

- [ADR 0001](../../adl/0001-reserve-inventory-at-checkout.md) — claims are created at checkout, not
  at cart entry, and availability is advisory everywhere until the claim write

## Related

- [Inventory reservation](../../concepts/inventory-reservation.md) — the claim model and its vocabulary
- [Stuck reservation release](../../runbooks/on-call/stuck-reservation-release.md) — repairing
  availability when expiry falls behind
