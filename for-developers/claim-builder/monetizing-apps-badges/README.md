# Monetizing Claims

If your use case requires monetization, you have a couple options.

**Option 1: $BADGE**

While $BADGE is the native credits of the blockchain and we do offer accepting $BADGE fees per on-chain approval use, we do not recommend using it for payment purposes. Note this is only applicable to badges with on-chain balances.

This is because:

* We intend for $BADGE to mainly be used as a compute credits system for on-chain transactions. It is not meant to be a payment currency.
* Users are not "BitBadges native". They are native to their own respective chains and wallets. It is better user experience to accept what your users are used to.
* Tooling is not great

**Option 2: Stripe / Other Payment Processors**

If using the BitBadges claim builder, you can customize your claiming flow to trigger claim completion upon payments or gate claims to those who have paid already. This can be via any payment method you want to accept.

This can be manually, via Zapier, or any way you want to connect it to a claim.

{% content-ref url="stripe-payments.md" %}
[stripe-payments.md](stripe-payments.md)
{% endcontent-ref %}

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Option 3: Crypto Payments**

For the payment layer, you can consider using a cross-chain decentralized exchange, such as Thorchain or Chainflip or Maya Protocol. Cross-chain decentralized exchanges go hand in hand with BitBadges because they both were built for the same purpose (one interface for all chains). We recommend ThorSwap and their SwapKit SDK which can be used for all of the above.

You can also just manually accept payments on all supported chains as well.
