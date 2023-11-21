# Metadata

### Overview

Each badge and collection will have its own metadata, where it can specify details about it such as name, description, image, and category.&#x20;

This metadata can be stored directly on the blockchain but is typically stored via another method to save blockchain resources. For the BitBadges website, we store via [IPFS](https://ipfs.tech/).

### **Default Metadata Standard**

The metadata details depend on the type of badge collection or [Standard](../../for-developers/collection-interface/standards.md) that the collection implements. The default metadata standard we use is the following:

**Name** (required): Name of the collection or badge

**Description** (required): Description of the collection or badge

**Image** (required): A URL that points to the image to be displayed. Images will be displayed in circular format. The recommended image size is 512 x 512.

**Validity**: Defines the start and end times for how long the badge is to be considered valid for. If left blank, we assume it is always valid. This is just for descriptive purposes and has no functional significance.

**Category**: Specify a category to make the metadata more queryable.

**External URL**: A custom URL / website that will be hyperlinked.

**Tags**: Keywords to make the metadata more queryable.



### For Developers

Visit [here](../../for-developers/bitbadges-api/compatibility.md) for more details.
