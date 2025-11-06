# Adding Data

**Method 1: UI**

You can directly manage your store in the interface.

**Method 2: API**

```typescript
await BitBadgesApi.performStoreAction(...)
await BitBadgesApi.performBatchStoreAction(...)
```

If you want to use the BitBadges API to send hooks / POST requests to, you can do so manually. We refer you to the interface in the developer portal for getting the exact route / body you might need.

<figure><img src="../../../.gitbook/assets/image (346).png" alt=""><figcaption></figcaption></figure>

**Method 3: Zapier**

Use the BitBadges Zapier integration to automate workflows for you. Zapier connects to over 7000+ integrations. In this case, your trigger will be the app you want to integrate nad the action will be BitBadges Add User to Dynamic Store.

Typically, you will want to field map data from your trigger (e.g. Eventbrite attendee email) into the BitBadges Store Action step. For documentation on how to do this, see Zapiers documentation.

* [Field Mapping](https://help.zapier.com/hc/en-us/articles/31709122224653-Enter-data-in-Zap-fields#01JC4MFMXXJXSS7GBAYZP32XKZ)
* [Send Data Between Steps By Mapping Fields](https://help.zapier.com/hc/en-us/articles/8496343026701-Send-data-between-steps-by-mapping-fields)

You may notice that we have a few different identifier fields (email, address, id, username). Leave the ones you don't need blank. Which field you populate should correspond to the store type you have created.

<figure><img src="../../../.gitbook/assets/image (349).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (347).png" alt=""><figcaption></figcaption></figure>

You can also use the Zapier approach to check criteria as well. For example,

1. Setup a Google Form that users can enter their email (or parse the Gmail from the metadata).
2. Setup a Zap to trigger upon Google Forms responses.
3. Check if existing Mailchimp subscriber using email provided via the Mailchimp integration plugin.
4. Add email to dynamic store if subscribed (note: we only allow claims for verified emails)

If setting up an in-site claim, you can redirect the users to the form via the URL Click plugin, Custom Instructions Plugin, or just in the description as well.
