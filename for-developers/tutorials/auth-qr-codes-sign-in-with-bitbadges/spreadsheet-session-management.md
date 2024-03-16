# Spreadsheet Session Management

For some use cases, a spreadsheet like Google Sheets can be used for maintaining sessions (e.g. which QR codes have been used, etc).

1. To get started, create a spreadsheet that looks like this. The Verify button is a Drawing.&#x20;
2. Go to Extensions -> App Scripts and copy / paste the JavaScript code below.
3. Go to your Verify Drawing and assign the script. Set the script to makeAPIRequest.
4. Now, when you click the button, it will use the values, call the getAuthCode BitBadges API route, and return the response. The response includes the message, params, and the Blockin verification response.
   1. IMPORTANT: Note that the responseData.verificationResponse (and thus the verification response in B3) is the Blockin verification. This verifies only that the signature is well-formed and badge ownership. However, you have to implement any additional checks, such as session management, expected parameters, etc.
5. You can customize this further to handle sessions however you would like. Using responseData.params is typically what you are looking for.

See the Blockin documentation for more information.

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

```javascript
function makeAPIRequest() {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var cellValue = sheet.getRange('B2').getValue(); // Change 'A1' to the cell where the user enters text
  const apiKeyValue = sheet.getRange('B1').getValue()
  var url = 'https://api.bitbadges.io/api/v0/authCode'; // Replace this with the API endpoint you want to call

  sheet.getRange('B3').setValue("");
  sheet.getRange('B4').setValue("");
  sheet.getRange('B5').setValue("");

  var options = {
    'method': 'POST', // Change this to 'POST' or 'PUT' if needed
    'contentType': 'application/json',
    'headers': {
      'x-api-key': apiKeyValue // Add x-api-key header
    },
    'payload': JSON.stringify({ signature: cellValue })
  };

  var response = UrlFetchApp.fetch(url, options);
  var responseData = JSON.parse(response.getContentText());

  // Process the response data as needed
  // For example, you can write it to another cell in the spreadsheet
  sheet.getRange('B5').setValue(responseData.message); // Change 'B1' to the cell where you want to display the response

  // Write verificationResponse.success (boolean) to cell D1
  sheet.getRange('B3').setValue(responseData.verificationResponse.success);

  // Write verification.errorMessage (if error) to cell E1
  if (!responseData.verificationResponse.success) {
    sheet.getRange('B4').setValue(responseData.verificationResponse.errorMessage);
  } else {
    sheet.getRange('B4').setValue("");
  }
}
```
