# Standards

Collections can optionally implement a specific type of collection or standard. Standards define the expected format of the collection and help others to know how to interpret the details of the collection.

Standards can define the expected values and format of everything about a collection, such as its expected metadata format or the expected genesis conditions. If you implement a standard, it is your responsibility to follow the rules defined by the standard.

See a list of all [standards](../../for-developers/concepts/standards.md) here. Choose the most appropriate standard(s) for your desired use case. You may choose to mix and match more than one as long as they are compatible.

**Examples**

For example, a non-fungible standard may enforce that the max supply of any badge never exceeds one. Or, a non-deletable standard may enforce that the delete collection permission is never enabled. Or, an attendance-badge standard might define a specific metadata format that is niche to attendance-based events only.&#x20;
