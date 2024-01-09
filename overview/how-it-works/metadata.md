# Metadata

### Overview

Each badge and collection will have its own metadata, where it can specify details about it such as name, description, image, and category.&#x20;

This metadata can be stored directly on the blockchain but is typically stored via another method to save blockchain resources. For the BitBadges website, we store via [IPFS](https://ipfs.tech/).

### **Default Metadata Standard**

The metadata details depend on the type of badge collection or [Standard](../../for-developers/core-concepts/standards.md) that the collection implements. The default metadata standard we use is the following:

**Name** (required): Name of the collection or badge

**Description** (required): Description of the collection or badge

**Image** (required): A URL that points to the image to be displayed. Images will be displayed with rounded corners.  We display them with a maximum size of 300 x 300 on the BitBadges website. Images are also used for all avatar icons. We recommend 512 x 512 at a minimum, however.

**Video:** A URL that points to a video to be displayed. Videos can either be a valid video file (e.g. .mp4) or a YouTube embed link. Videos are only viewable upon viewing the "main" page. We will use the required image as an avatar icon in other places. For video files, we use the image as a thumbnail as well (YouTube videos have their own thumbnails).

**Validity**: Defines the start and end times for how long the badge is to be considered valid for. If left blank, we assume it is always valid. This is just for descriptive purposes and has no functional significance.

**Category**: Specify a category to make the metadata more queryable.

**External URL**: A custom URL / website that will be hyperlinked.

**Tags**: Keywords to make the metadata more queryable.

**Socials:** Provide socials links (Discord, Telegram, etc)

<figure><img src="../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

### For Developers

Visit [here](../../for-developers/bitbadges-api/designing-for-compatibility.md) for more details.
