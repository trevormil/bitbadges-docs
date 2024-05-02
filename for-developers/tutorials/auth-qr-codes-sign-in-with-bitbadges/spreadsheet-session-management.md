# Spreadsheet Session Management

For some use cases, a spreadsheet like Google Sheets can be used for maintaining sessions (e.g. which QR codes have been used, etc). Note this tool only works out of the box with codes stored via the BitBadges API (aka in users's accounts).

1. To get started, create a spreadsheet that looks like this. The Verify button is a Drawing.&#x20;
2. Go to Extensions -> App Scripts and copy / paste the JavaScript code below.
3. Go to your Verify Drawing and assign the script. Set the script to makeAPIRequest.
4. Now, when you click the button, it will use the values, call the getAuthCode BitBadges API route, and return the response. The response includes the message, params, and the Blockin verification response.
5. IMPORTANT: You have to implement any additional checks, such as session management, expected parameters, avoiding replay attacks. etc.
   1. You can customize this further to handle sessions however you would like. Using responseData.params is typically what you are looking for. See the TODOs in the script.

See the Blockin documentation for more information.

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

```javascript
function makeAPIRequest() {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var cellValue = sheet.getRange('B2').getValue(); // Change 'A1' to the cell where the user enters text
  const apiKeyValue = sheet.getRange('B1').getValue()
  var url = 'https://api.bitbadges.io/api/v0/authCode'; // Replace this with the API endpoint you want to call

  //Reset to blank
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

  //Parse response and update the main section
  var response = UrlFetchApp.fetch(url, options);
  var responseData = JSON.parse(response.getContentText());
  var cosmosAddress = "cosmos"
  var params = responseData.params;
  var message = responseData.message;
  var verificationResponse = responseData.verificationResponse

  sheet.getRange('B5').setValue(message);
  sheet.getRange('B6').setValue(JSON.stringify(responseData, null, 2))

  // Write verificationResponse.success (boolean) to cell D1
  sheet.getRange('B3').setValue(verificationResponse.success);

  // Write verification.errorMessage (if error) to cell E1
  if (!verificationResponse.success) {
    sheet.getRange('B4').setValue(verificationResponse.errorMessage);
  } else {
    sheet.getRange('B4').setValue("");
  }

  //------------------------------VALIDATION----------------------------------------
  //TODO: This section should be implemented by you

  // Check if cosmosAddress already exists
  var existingAddresses = sheet.getRange(2, 2, sheet.getLastRow() - 1, 1).getValues().flat();
  if (existingAddresses.includes(cosmosAddress)) {
    sheet.getRange('B3').setValue(false);
    sheet.getRange('B4').setValue("Error: Address already used!")
    throw new Error("Address already used!");
  }
  

  //TODO: Implement any other checks you may want
  //- 1 use per address is implemented for you above.
  //- Verify domain, uri, and/or statement is correct
  //- Verify nonces are valid
  //- Protect against replay attacks
  //- Verify timing (expiration, not before)
  //- Can reuse the same badge / asset? Or is each one-time use only

  //---------------------------------------------------------------------------------

  
  // Find the next available row
  var nextRow = sheet.getLastRow() + 1;
  const keys = Object.keys(params);

  var column = 3; 
  sheet.getRange(nextRow, 2).setValue(cosmosAddress);
  
  // Loop through the params and write the corresponding values horizontally
  for (const key of keys) {
    if (params.hasOwnProperty(key)) {
      sheet.getRange(nextRow, 1).setValue("Authentication #" + (nextRow - 7))

      sheet.getRange(nextRow, column).setValue(params[key]);
      column++; // Move to the next column
    }
  }
}
```
