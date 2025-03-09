# Payment Checking

If your use case requires monetization (such as memberships or recurring payments), we host an in-site Stripe plugin for you to use. While $BADGE is the native credits of the blockchain and we do offer accepting $BADGE fees per on-chain approval use, we do not recommend using it for payment purposes. We recommend to accept payments using existing processors like Stripe.&#x20;

Note: This is provided for convenience and is not the only solution. You can choose to self-implement payments however you want. Ex: Self-host and gate by emails who have paid.

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

### Setting Up Your Account

To setup a Stripe Connect aaccount, go to the connected accounts section of the Developer Portal ([https://bitbadges.io/developer](https://bitbadges.io/developer)).&#x20;

### Creating a Claim

Once setup, you can then get started creating claims with the Stripe Payment plugin. Users will be prompted to complete the checkout process before claiming.

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

### Payment Capture Options (Edge Cases)

We offer two options for capturing (capturing is the process of saving the payment to your connected account) payments per claim: Capture Before Claim and Capture After Claim.&#x20;

Edge cases are rare and handled as best we can, but please be aware of the approach you choose when setting up a claim.

We handle claims in the following way:

1. All external logic is checked and processed first (API calls) in addition to verifying internal logic.
2. If everything succeeds, we place it in an asynchronous queue for final processing.
3. The claim is then processed via the queue and the final results are saved.
4. We do a final sanity check of the internals to make sure it still passes all the logic checks.

This process usually takes a maximum of a couple seconds and in many cases is pretty much immediate.

For example, with a Discord Server gated claim, we:

1. Check the Discord server membership and check the user does not exceed the overall claim or claims per address
2. If it succeeds, we place it in the queue
3. The claim is processed in the queue and final results are saved
4. Final sanity check for internal logic again (ex: making sure the user still does not exceed the overall claim or claims per address). External logic (Discord server check) is not checked again

However, there are a handful of cases where internal state logic may lead to race conditions where Step 1) passes but the final Step 4) sanity check fails after a couple seconds

* Max uses (overall or per address) were fine at Time 1 but not Time 2
* A claim code was not used  in state at Time 1 but was used at Time 2
* A custom plugin claim token was not used at Time 1 but was at Time 2

These cases are rare but definitely possible and need to be accounted for. As a result of this, we have two ways that we can capture the Stripe payments: before or after claim completion with various tradeoffs.

1. Capture Before: We capture after Step 1) and before Step 2). Thus, we know the claim should succeed in almost all cases, but if one of the race conditions happens, we need to refund the user. You cover the Stripe fees in the refund.
2. Capture After: We capture immediately after the claim has been completed (after Step 4). If it fails, we keep retrying. Note: In Step 1), we confirm the payment is ready to capture, so the capture is expected to succeed, unless in extreme circumstances like a card closure in those exact seconds. We have yet to see a case where this has failed. However, it still needs to be accounted for. You must be okay with the fact that in an edge case, a user can successfully claim without payment going through. We notify you if this ever occurs.

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

#### Capture After Claim

Capturing after the claim is the recommended approach. We first complete the claim entirely, check the payment is ready to be captured, and then asynchronously capture the payment to your connected account after the fact. We attempt this as quick as possible, and in most cases, this is immediate.

However, in very rare cases, the capture may fail due to a card closure or other issues. This may leave you with the case where the claim has been completed but payment eventually fails. This is very rare, and we will notify you if this happens.

While rare, you should have measures in place to handle this rare case, such as rollbacks, refusing service until the payment is confirmed being captured, etc. For example, this would not be a good option for a blockchain transaction since there is no way to rollback the transaction.

#### Capture Before Claim

When we capture the payments before the claim, we save the payment to your connected account immediately BEFORE placing it in the queue. If the final sanity check fails, we have to issue a refund to the customer. The Stripe fees are not returned, and you make up the difference from your balance.

Note: The refunds are only triggered if 1) the initial claim is successful and 2) the final sanity check fails. This is rare but is definitely possible. For example, a user claims when we are at 999/1000 uses and two seconds later upon final processing, the claim was not successful in time. If the claim fails initially, the payment is never captured, so this is not a concern. It is only a concern if the second sanity check fails.

If implementing this approach, please design your claim to avoid such cases.

This is bad experience for the refunded customer too, as they will see both a refund and a charge on their account. Plus, refunds may take time to process.

By using this approach, you ensure that the claim is only completed by users who have paid without failure.

