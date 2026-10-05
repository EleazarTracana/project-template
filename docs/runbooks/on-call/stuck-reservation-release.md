# Stuck reservation release

## Symptom

One or both of these:

- Alert `InventoryAvailabilityDrift` — sellable quantity is below physical stock for one or more
  items with no corresponding sales
- Support reports an item showing as unavailable while the warehouse confirms units on hand

## Confirm

Before releasing anything, confirm that expiry is actually behind. An item can legitimately show
zero availability under heavy live demand.

1. Check the age of the oldest held claim.

   ```
   ops reservations oldest --state held
   ```

   Expected: age below the claim lifetime (default 30 minutes). An age in hours means expiry has
   stopped making progress and this runbook applies.

2. Check whether expiry is running at all.

   ```
   ops jobs status reservation-expiry
   ```

   Expected: `healthy`, with a last successful run inside the last five minutes. A stale or failing
   status is the actual incident — releasing claims by hand treats the symptom only.

3. Count the affected claims.

   ```
   ops reservations count --state held --older-than 1h
   ```

   A count in the low hundreds is a backlog. A count in the thousands suggests expiry has been down
   for some time; note the number before and after, so you can tell whether it is still growing.

## Resolve

Restore the job first. Only release by hand if the job cannot be restored or the backlog will not
drain in time.

1. Restart the expiry job.

   ```
   ops jobs restart reservation-expiry
   ```

   Expected: status returns to `healthy` within one minute. Watch the stale count from step 3 — if
   it falls, the backlog is draining and you are done after the verification step below.

2. If the job will not start, read the last failure before acting further.

   ```
   ops jobs logs reservation-expiry --tail 100
   ```

   A database connection or permission failure is an infrastructure incident — escalate rather than
   working around it. A single poisoned claim causing a crash loop is handled in step 3.

3. **Destructive — releases claims.** If a single claim is crashing the job, release it by
   identifier. Only do this for a claim the logs name explicitly.

   ```
   ops reservations release --id <claim-id> --reason runbook-poison-claim
   ```

   Then return to step 1.

4. **Destructive — releases claims in bulk.** If the job cannot be restored and shoppers are being
   refused, release the aged backlog. Use an age well above the claim lifetime so you cannot
   release claims belonging to live checkouts.

   ```
   ops reservations release --state held --older-than 2h --reason runbook-expiry-outage
   ```

   Expected: a count matching step 3's measurement. A substantially larger count means the age
   filter is wrong — stop and escalate rather than running it again.

## Verify

1. Availability for a reported item matches physical stock minus genuinely live claims.

   ```
   ops inventory availability --item <item-id> --explain
   ```

2. The drift alert clears within one evaluation window (five minutes).

3. The oldest held claim is back under the claim lifetime.

   ```
   ops reservations oldest --state held
   ```

## Escalate

- **Platform on-call** — immediately, if the expiry job fails on a database connection or
  permission error. This is infrastructure, not ordering.
- **Ordering team lead** — if the aged backlog exceeds 5,000 claims, or if it keeps growing after a
  successful job restart. Either means a defect in claim resolution, not a stalled job.
- **Do not escalate for** a backlog that is draining after a restart, even a large one.

## Why this happens

Claims hold stock and are released on payment, cancellation, or expiry. Expiry is the only one of
the three that covers a checkout that simply stops, so when expiry falls behind, abandoned
checkouts keep their claims and availability drops below physical stock with no sales to explain it.

See [inventory reservation](../../concepts/inventory-reservation.md) for the claim model and
[ADR 0001](../../adl/0001-reserve-inventory-at-checkout.md) for why claims exist at all.
