# 🕵 Verification Tools

Here, we provide documentation for some common verification tools / integrations as a reference. Let us know if you want to add a tool or tutorial here. **Always use third party tools at your own risk!**&#x20;

For developers, see the following page as well.

{% content-ref url="../for-developers/badge-verification/" %}
[badge-verification](../for-developers/badge-verification/)
{% endcontent-ref %}

**Blockin**

Badge-gate both digitally (e.g. websites) and in-person with Blockin. Requires technical knowledge to implement.

{% content-ref url="https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/" %}
[Blockin](https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/)
{% endcontent-ref %}

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

**BitBadges Website - Authentication QR Codes for In-Person Events**

Consider creating QR codes through the BitBadges site to be presented at an in-person event. See [https://bitbadges.io/auth/linkgen](https://bitbadges.io/auth/linkgen) for becoming a QR code authentication provider.

Users will sign a message to prove badge ownership and store the signature as a QR code. At authentication time, you will scan this QR code with a QR code scanner and verify the authentication request.&#x20;

We have created an in-browser verification tool you can use at [https://bitbadges.io/auth/verify](https://bitbadges.io/auth/verify). However, this is probably only useful for very simple use cases. See [here](../for-developers/badge-verification/) for developer documentation to help implement a more customizable solution.

&#x20;<img src="../.gitbook/assets/image.png" alt="" data-size="original">
