# 🖊 Standards

Standards are a generic concept that allows anyone to define how to interpret the details of a badge. All badges will implement the same collection interface on the blockchain (see [Collections](collections.md)), but the standard defines how these fields are interpreted and should be defined.

For example, a non-fungible standard may enforce that the max supply of any badge never exceeds one. Or, a non-deletable standard may enforce that the delete collection permission is never enabled. Or, an attendance-badge standard might define a specific metadata format that is niche to attendance-based events only.

IMPORTANT (for developers): There is no check in the blockchain logic that a specific standard is actually followed. The standard number stored on the blockchain is purely informational and for guidelines. It is your responsibility to double check standards are being followed correctly and take action accordingly.

### Adding New Standards

We want to decentralize the process of creating new standards and open it up to everyone. The comprehensive list of standards can be found on this page below. If you would like to author a new standard and have it added, contact us.

### Format

Each standard must follow the following format:

**Author:** John Doe

**Standard ID:** 0

**Version:** 0.0.1

**Standard Details:** The standard details outline the expected functionality of badges who implement this standard. Some examples of things to outline her include the following:

* Metadata
* Bytes
* Permissions&#x20;
* Disallowed Transfers
* Manager Approved Transfers
* Max Supplys
* Other



## List of Standards

### BitBadges Token Base Standard (ID: 0)

**Author**: The BitBadges Team

**Standard ID**: 0

**Version**: 0.0.1 (Last Updated: 4/12/2023)

**Standard Details:**&#x20;

The BitBadges Website standard defines the expected format of badges that are to be supported on the BitBadges website.&#x20;

* Metadata: Metadata for both collections and badges will implement the same interface. This interface is as follows:

<pre class="language-typescript"><code class="lang-typescript"><strong>{
</strong>    name: string;
    description: string;
    image: string;
    validFrom?: {
        start: number; //the number of milliseconds elapsed since the UNIX epoch
        end: number; //the number of milliseconds elapsed since the UNIX epoch
    },
    color?: string; //valid HTML color
    category?: string;
    externalUrl?: string;
    tags?: string[];
}
</code></pre>

* Bytes: The bytes field will remain arbitrary. It will serve no functional use.
* Permissions: All permissions are supported.
* Transferability: All combinations of transferability are supported.
* Manager Approved Transfers: All combinations of the manager's approved transfers are supported.
* Max Supplys: All combinations of supplys are supported.



### BitBadges Token (Off-Chain Balances) Standard (ID: 1)

**Author**: The BitBadges Team

**Standard ID**: 1

**Version**: 0.0.1 (Last Updated: 4/30/2023)

**Standard Details:**&#x20;

This standard defines the expected format of badges that are to be supported on the BitBadges website. This standard allows balances to be defined off-chain by a JSON file hosted at the URI pointed to by the **bytes** field. Because balances are stored off-chain, no on-chain transfer functionality will be supported.

There are two main use cases for this standard.&#x20;

1\) If badge balances are to be permanent (soulbound / non-transferable), this standard enables a lightweight way to create a badge by outsourcing most of the storage off-chain (although still verifiable due to the on-chain URI). It is recommended that IPFS or permanent file storage solutions are used.

2\) If the manager is to have complete control over the balances of a badge at any time, this standard is also useful to save resources.

* Metadata: Metadata for both collections and badges will implement the same interface. This interface is as follows:

<pre class="language-typescript"><code class="lang-typescript"><strong>{
</strong>    name: string;
    description: string;
    image: string;
    validFrom?: {
        start: number; //the number of milliseconds elapsed since the UNIX epoch
        end: number; //the number of milliseconds elapsed since the UNIX epoch
    },
    color?: string; //valid HTML color
    category?: string;
    externalUrl?: string;
    tags?: string[];
}
</code></pre>

* Bytes: The bytes field will be a URL to the JSON file of balances. See [ipfs://QmRpXjc5tHhh6pHgqzmfnwMRLc3bZ1FdqSLxth5iqxxXhX](ipfs://QmRpXjc5tHhh6pHgqzmfnwMRLc3bZ1FdqSLxth5iqxxXhX) for expected format of JSON file.&#x20;
* Permissions: The CanUpdateBytes permission will correspond to whether the manager can update the balances URL. The CanUpdateDisallowed permission should be set to false because on-chain transfers are not supported for off-chain balances.
* Transferability: Transferability does not matter because on-chain transfers are not supported.
* Manager Approved Transfers: Manager's Approved Transfers do not matter because on-chain transfers are not supported.
* Max Supplys: All combinations of supplys are supported.
