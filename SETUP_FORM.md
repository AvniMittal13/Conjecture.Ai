# Setting up the email capture backend

Since the site is static (GitHub Pages), we use a **Google Apps Script** as a free backend.
It does two things:
1. Saves signups to a Google Sheet
2. Sends you an email notification

## Setup steps (5 minutes)

### 1. Create a Google Sheet
- Go to [sheets.google.com](https://sheets.google.com) and create a new sheet
- Name it "Conjecture.AI Signups"
- In row 1, add headers: `Timestamp` | `Email`

### 2. Create the Apps Script
- In the sheet, go to **Extensions → Apps Script**
- Delete any code there and paste this:

```javascript
function doPost(e) {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var data = JSON.parse(e.postData.contents);

  // Save to sheet
  sheet.appendRow([data.timestamp || new Date(), data.email]);

  // Send notification email
  MailApp.sendEmail({
    to: 'avni.mittal2002@gmail.com',
    subject: '🎉 New Conjecture.AI signup: ' + data.email,
    body: 'New early access request:\n\nEmail: ' + data.email + '\nTime: ' + data.timestamp
  });

  return ContentService
    .createTextOutput(JSON.stringify({ status: 'success' }))
    .setMimeType(ContentService.MimeType.JSON);
}
```

### 3. Deploy as Web App
- Click **Deploy → New deployment**
- Type: **Web app**
- Execute as: **Me**
- Who has access: **Anyone**
- Click **Deploy**
- Copy the URL (looks like `https://script.google.com/macros/s/AKfyc.../exec`)

### 4. Update index.html
- In `index.html`, replace `YOUR_GOOGLE_APPS_SCRIPT_URL` with the URL you just copied

## That's it!
Every signup will:
- Appear as a new row in your Google Sheet
- Trigger an email to avni.mittal2002@gmail.com
