# Delayed Authentication

Should the QR codes be one-view only or stored in your BitBadges account (under the Authentication Codes) tab?&#x20;



With the BitBadges API, we use an authentication code system. Each unique user can generate a code which is associated with their respective sign in details. They then present the code to the provider (how they do so is up to the provider but typically its via a QR code). The provider can then verify the authentication details corresponding to that code.

The main design decision with delayed authentication is who is going to cache the details for the user (BitBadges or you).

* BitBadges: If you let BitBadges store the details for you, you can fetch it from the API when you need it (i.e. user presents you with code ID -> fetch details from API -> verify). This requires you to have API access at verification time. An added benefit of this method is that you do not need to host a custom frontend. Users can simply navigate to the bitbadges.io link directly. Authentication codes will be stored in their BitBadges account under the Authentication Codes tab.
* Provider: If you want to cache the details yourself, you will need to know the details when the user generates them. The easiest way to do so is to use the callback feature of Sign In with BitBadges. This passes back all relevant information for the user's authentication code, and you can store them.&#x20;
