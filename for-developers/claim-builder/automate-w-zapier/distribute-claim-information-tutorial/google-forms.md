# Google Forms

## Setting Up a Claim with Google Forms Zapier

The easiest way is to simply create a Zap using the Google Forms and BitBadges integrations. See the link below for a full tutorial.

<figure><img src="../../../../.gitbook/assets/image (145).png" alt=""><figcaption></figcaption></figure>

{% content-ref url="./" %}
[.](./)
{% endcontent-ref %}

## Setting Up a Claim with Google Forms Script Editor

This guide will walk you through setting up a Google Form to generate unique claim codes using a seed code from the BitBadges Codes plugin.

### Prerequisites

* A Google account
* Access to Google Forms
* A claim with the BitBadges Codes plugin

### Steps

#### 1. Create a Google Form

* Go to Google Forms.
* Create a new form or open an existing one. Customize as needed.

#### 2. Open the Script Editor

* In your Google Form, click on the three dots in the upper right corner.
* Select Script editor.

<figure><img src="../../../../.gitbook/assets/image (138).png" alt=""><figcaption></figcaption></figure>

#### 3. Add the Script

1\. Delete any existing code in the script editor.

2\. Copy and paste the following code into the script editor.\


```javascript
var SEED_CODE = 'ENTER_SEED_CODE_HERE';

function onFormSubmit(e) {
  var form = FormApp.getActiveForm();
  var submissionNumber = getSubmissionNumber();
  var code = generateCodeFromSeed(SEED_CODE, submissionNumber);
  setCustomConfirmation(form, code);
}

function generateCodeFromSeed(seedCode, submissionNumber) {
  var data = seedCode + '-' + submissionNumber;
  var hash = Utilities.computeDigest(Utilities.DigestAlgorithm.SHA_256, data);
  var hexHash = hash.map(function(byte) {
    var hex = (byte & 0xFF).toString(16);
    return (hex.length === 1 ? '0' : '') + hex;
  }).join('');
  return hexHash + '-' + submissionNumber;
}

function getSubmissionNumber() {
  var form = FormApp.getActiveForm();
  return form.getResponses().length;
}

function setCustomConfirmation(form, code) {
  var confirmationMessage = "Thank you for completing the survey. Your unique code is: " + code + '. Provide this in the form on BitBadges when claiming.'
  form.setConfirmationMessage(confirmationMessage);
}
```

3\. Replace 'ENTER\_SEED\_CODE\_HERE' with your actual seed code. To get your seed code, use the Codes plugin on BitBadges:

* Click on the Distribute button which opens up the modal.
* Select Batch.
* Copy the seed code.

<figure><img src="../../../../.gitbook/assets/image (139).png" alt=""><figcaption></figcaption></figure>

#### 4. Set Up the Trigger

* In the script editor, click on the clock icon to open the Triggers page.
* Click on + Add Trigger.
* Set the following options:
  * Choose which function to run: onFormSubmit
  * Choose which deployment should run: Head
  * Select event source: From form
  * Select event type: On form submit

<figure><img src="../../../../.gitbook/assets/image (140).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (137).png" alt="" width="554"><figcaption></figcaption></figure>

#### 5. Save and Close

Save your script by clicking on the disk icon or pressing Ctrl + S. Close the script editor.

#### 6. Test Your Form

Submit a response to your form.

* Check the confirmation message to see your unique code.
* Note that the forms are assigned based on number of responses (including any test submissions). If you want to reset from scratch, you can delete all saved responses in your form.



**Add-Ons**

Consider adding functionality to the script like sending the code via emails, protecting against multiple submissions, etc. We leave thi sup to you and your needs.

