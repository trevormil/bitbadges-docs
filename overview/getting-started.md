# 🔨 Getting Started

All major flows are fully supported directly in the BitBadges site! We envision that >95% of use cases can be fully implemented directly in-site with no code required.

If you do need more advanced functionality or are confused at any part, we refer you to the respective parts of this documentation for more information. The developer documentation can help you implement more custom applications and flows.

Please reach out in Discrd if you are having trouble, have feedback, or have feature requests!

### **Creating Badges, Lists, Attestations, Protocols**

Head over to the BitBadges site -> Create tab to get started! The forms should walk you through the entire process. You can then manage them from their respective page.

<figure><img src="../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>

### **In-Site Claims**

For in-site claims, head over to the respective list / badge/ claim page and complete the claim forms. Note that some creators might implement custom flows which are handled outside the BitBadges site. This is specific to the badge collection / list.

<figure><img src="../.gitbook/assets/image (17).png" alt="" width="375"><figcaption></figcaption></figure>



### **Creating Application / Product Pages**

Application or product pages are an abstraction over badges, claims, and all else we offer. This allows you to create an all-in-one page that displays everything for your application.\ and redirects users wherever necessary. Create pages in the developer portal.

The only entrypoint is claims, so to showcase badges, lists, etc, you can make a wrapper parent claim that can check for badge ownership, list inclusion, or anything else you want to check.

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

### **Setting Protocol Values**

For manually setting protocol values, you can do so on the protocols page.

<figure><img src="../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

### Adding Attestations via Invite Codes

If you are the recipient or holder of an attestation, the issuer or creator may give you an invite code to add it to your account. The attestation can then be added on the Attestations -> Add via Code page.

<figure><img src="../.gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

### **Sign In with BitBadges**

Sign In with BitBadges is a more developer-oriented flow since you will have your own server or resource you are gating access to. We refer you to the documentation here.

{% content-ref url="../for-developers/authenticating-with-bitbadges/" %}
[authenticating-with-bitbadges](../for-developers/authenticating-with-bitbadges/)
{% endcontent-ref %}
