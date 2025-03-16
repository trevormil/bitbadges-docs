# Payment Checking

If your use case requires monetization (such as memberships or recurring payments), we host an in-site Stripe plugin for you to use. While $BADGE is the native credits of the blockchain and we do offer accepting $BADGE fees per on-chain approval use, we do not recommend using it for payment purposes. We recommend to accept payments using existing processors like Stripe.&#x20;

Note: This is provided for convenience and is not the only solution. You can choose to self-implement payments however you want. Ex: Self-host and gate by emails who have paid.

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Setting Up Your Account

To setup a Stripe Connect account, go to the connected accounts section of the Developer Portal ([https://bitbadges.io/developer](https://bitbadges.io/developer)).&#x20;

### Creating a Claim

Once setup, you can then get started creating claims with the Stripe Payment plugin. Users will be prompted to complete the checkout process before claiming.

Note: Refunds are possible (learn more below). In the event of an auto-refund, the Stripe fees are not returned, and you make up the difference.

To mitigate refunds, the golden rule for designing payment-gated claims that avoids any refund logic is that a a specific claim attempt from a user will either always succeed or always fail. It should never be able to succeed at time A but not at time B. In other words, assume that if the queue is backed up for 100 days, that should not alter whether a claim succeeds or fails.

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Payment Capture

Since claims and Stripe are two separate services, we can either capture payments immediately before or after the claim has been completed. For our design, we capture claims before to guarantee payment before the claim.

#### How Claims Work Behind the Scenes (The Basic Process)

1. The claim is fully completed / simulated to see if it will succeed (both external and internal logic). This also includes a ready to capture payment check to ensure the user has completed checkout and we are ready.&#x20;
2. If all claim checks pass, the claim is queued. We cache any external logic.
3. We capture the payment here.
4. The claim is added to a queue
5. At the queue's final processing time, we rerun the claim's internal logic (state handling) to ensure it still passes and there are no race conditions. We reuse any external logic from Step 1.
   1. If the sanity check fails, we have to issue a refund to the customer at this point. This is rare but can happen with poor claim design.

This process is often immediate (<1 second), but the time ultimately goes at the speed of the queue.

#### Capture Before Claim

* **What happens**: The payment is captured immediately before the final processing of the claim but after confirming a full simulation.
* **Advantage**: Guarantees payment for successful claims. 100% of users who complete the claim have guaranteed payment went through.
* **Risk**: In the rare case of the sanity check of failing after Step 1) full simulation succeeded, we automatically issue refunds to the customer. Stripe fees are NOT returned, you make up the difference in the refund to each customer (poor user experience as well - 2 charges on statements plus additional processing).
* **Proper Design:** This can always be mitigated with proper claim design. **T**he golden rule is design your claims such that a specific claim attempt from a user will either always succeed or always fail. It should never be able to succeed at time A but not at time B.
  * When might the refund case occur?
    * **Max Uses Exceeded:** 1000 users submit at exact same time with only 1 claim use left. All 1000 get added to the queue. We have to refund 999 of them.
    * **Claim Codes:** If you give out a one-time use code to two users and they both submit at the same time and both make it into the queue, we will have to refund one.
  * Note though, these cases are rare but need to be accounted for
    * It only applies to internal state race conditions, not external fetches
    * Even just a second or two between claim submissions typically would invalidate the initial simulation (Step 1) leading to it never being placed in the queue and payment never being captured.
    * We do our best on our end to mitigate and prevent such cases before they happen
