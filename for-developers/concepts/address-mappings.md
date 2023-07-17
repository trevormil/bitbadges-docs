# 📧 Address Mappings

[AddressMappings](https://bitbadges.github.io/bitbadgesjs/packages/proto/docs/interfaces/AddressMapping.html) are a powerful feature similar to UintRanges. They allow us to specify a list of addresses, identified by an ID.&#x20;

These are invertible meaning we can create a mapping that includes all addresses EXCEPT some specified addresses. Or, we can create a mapping that includes ONLY some specified addresses.

<pre class="language-protobuf"><code class="lang-protobuf">message AddressMapping {
  string mappingId = 1;

  repeated string addresses = 2;
  bool includeAddresses = 3;

<strong>  string uri = 4;
</strong>  string customData = 5;
}
</code></pre>

AddressMappings are permanent and not updatable once created. The same address mapping can be referenced across collections by their unique IDs.

**Reserved Address Mappings**

There are a couple IDs for AddressMappings that are reserved for efficient shorthand methods:

* If prefixed with "!", it denotes to invert the address mapping (e.g. "!id123" inverts the "id123" address mapping)
* Any valid Cosmos (bech32) address is reserved as the mapping that ONLY includes that specific address.
* "Mint" specifies the "Mint" address only.
* "Burn" specifies the "Burn" address only.
* "All" denotes all valid addresses (thus excluding the "Mint", "Burn", and "Total" addresses)
* "Manager" will be reserved for a mapping that ONLY includes the current manager of the collection
