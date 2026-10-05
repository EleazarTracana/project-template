# ADR 0001: Reserve inventory at checkout, not at cart entry

## Status

ACCEPTED

## Context

Every storefront that sells finite stock has to answer one question: at what moment does a unit of
inventory stop being available to other shoppers? There are two defensible moments. The unit can be
held when it enters a cart, or it can be held when the shopper commits at checkout.

The question is forced on us rather than chosen. Until it is answered consistently, availability
means something different in every part of the system — the catalog shows one number, the cart
assumes another, and fulfilment discovers the truth last.

## Decision

Inventory is reserved at checkout. A unit in a cart is not held and carries no claim on stock.

Availability shown anywhere in the system is advisory until the moment of commitment. The
authoritative check happens once, at the reservation write, and it is conditional on remaining
stock. Two shoppers may both be shown the last unit; exactly one reservation succeeds, and the
other is refused at the moment of commitment.

A reservation is a claim with a lifetime, not a permanent decrement. An abandoned checkout returns
its units.

## Rationale

Reserving at cart entry is the more intuitive option and we took it seriously. It has a real
virtue: a shopper who adds the last unit is guaranteed to get it, so nobody is ever disappointed
after committing. For a low-traffic catalogue that is close to free.

We rejected it because a cart is a browsing artifact, not an intention. Most carts are never
completed, and the correlation runs the wrong way — the most desirable items are the ones most
often added speculatively. Holding stock on cart entry means the items under the most genuine
demand spend their day locked on behalf of shoppers who were never going to buy, while shoppers who
would have bought see them as unavailable. The mechanism inverts exactly when it matters most.

We also considered a middle path: hold on cart entry, but with a short expiry. It fixes the worst
of the lock-up but introduces something worse — availability that changes under the shopper without
any action on their part, which is far harder to explain than a clean refusal.

Now, our choice does open a window. Between the moment availability is shown and the moment it is
committed, stock can go. We close that at the write rather than by reserving earlier: the
conditional write makes the race safe, and the loser gets an immediate, legible refusal at the
moment they committed rather than a silent cancellation hours later.

This is critical to understand, because it is the part that is easy to get wrong: correctness here
does not come from reserving early. It comes from the reservation write being the single
authoritative decision point. Any second place in the system that believes it knows whether stock
is available reintroduces the oversell we are preventing.

## Consequences

We gain honest availability for the majority of shoppers, who never reach checkout at all, and a
single place in the system where overselling can be prevented.

We pay by moving disappointment later in the funnel — to the most expensive place to disappoint
someone. That cost is deliberate and it is the whole price of the decision.

The choice creates an operational obligation that did not exist before. Reservations now have a
lifetime, which means something must release them when a checkout is abandoned, and that release
path becomes a production concern we own and have to be able to repair by hand. It is the first
thing to look at when availability and physical stock disagree.

Advisory availability also constrains the product surface. Anywhere we display stock, the display
has to tolerate being wrong by the time the shopper acts, which rules out presenting it as a
promise.

## References

- [ADR 0000](0000-record-architecture-decisions.md) — why decisions are recorded this way
- [Inventory reservation](../concepts/inventory-reservation.md) — the mechanism and its vocabulary
- [Ordering module](../domain/ordering/README.md) — the bounded context that owns reservations
