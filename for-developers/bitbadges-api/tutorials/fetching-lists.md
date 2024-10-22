# Fetching Lists

You can also fetch address lists using the BitBadges API.  AddressLists are stored and fetched using the [BitBadgesAddressList](https://bitbadges.github.io/bitbadgesjs/packages/bitbadgesjs-sdk/docs/interfaces/BitBadgesAddressList.html) interface. Visit the [SDK docs](../../bitbadges-sdk/) for lots of useful functions for dealing with accounts.

Note off-chain list IDs are prefixed with "bitbadgesAddress\_".&#x20;

<pre class="language-typescript"><code class="lang-typescript"><strong>const listsRes = await BitBadgesApi.getAddressLists([{
</strong><strong>    //example
</strong><strong>    listId: "...",
</strong><strong>    viewsToFetch: [{
</strong>        viewType: 'listActivity',
        viewId: 'listActivity',
        bookmark: ''
    }]
<strong>}])
</strong><strong>
</strong><strong>const list = listsRes[0];
</strong></code></pre>

### Metadata

The **metadata** will be in the same format as badges / collections.&#x20;

### NSFW / Reported

NSFW or reported lists will be flagged with the **nsfw** or **reported** properties. Everything is still fetched as normal with typical requests, and it is up to you to do what you want with these accounts.

On the app, we hide all profile details (links, bios, etc) and provide a warning message.

### Alias

The **alias** field is the alias BitBadges address for the list (a fake account that can receive badges).

### **Views / Paginations**

Views have a base **viewType** describing the query type and a unique **viewId** for identification. Each request you will pass in a bookmark obtained from the previous request (pass in '' for the first request). This will fetch the next 25 documents for that view. Once no more docs can be fetched, the returned **hasMore** will be false.

The list interface only currently supports "listActivity" to fetch the latest list information.

### **Privacy / Permission**

The **private** field denotes whether it is to show up in search results or not.&#x20;

If **private** and **viewableWithLink** are true, the list is private but can be viewed by others with the link / list ID. If **private** and not viewable with a link, it is only viewable by the creator.
