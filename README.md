# portfolio website: a minimalist dynamic portfolio website
![1](1.png)
## 🌐 view project
### 👉 click here: https://vidrop.netlify.app/
## 🔎 description
built a minimalist portfolio website with dynamic project loading and a contact form with email notifications using css, javascript, html, google app scripts, google sheets, and json to make it easy for non-technical users to view my projects and contact me while simplifying portfolio maintenance
## ⚙️ how to get your own custom portfolio website
- clone the repository
- open `index.html` in your browser
- modify `index.html` and `style.css` to reflect your personal branding
- update `projects.json` with your own projects
- connect a Google Sheet to the portfolio website for realtime data storage and automatic email notification for each new contact form submissions
- create a Google Sheet
- rename the sheet (the title doesn't really matter) to `portfolio contact`
- name the header of your sheets by filling up the first row with names that matches the `name` identifier in `index.html`

for example in the code below my name attributes are:

- `name`
- `message`
- `phone`
- `email`

```html
<input type="text" name="name" placeholder="name" required>
<textarea name="message" rows="4" placeholder="message" required style="resize: none;"></textarea>
<input type="tel" name="phone" placeholder="phone number" required>
<input type="email" name="email" placeholder="email" required>
```

so they must be the name of the headers of my google sheet

- go to `Extensions` > `Google Apps Script`
- copy and paste this code

```jsx
var sheetName = 'portfolio contact';
var scriptProp = PropertiesService.getScriptProperties();

function initialSetup() {
  var activeSpreadsheet = SpreadsheetApp.getActiveSpreadsheet();
  scriptProp.setProperty('key', activeSpreadsheet.getId());
}

function doPost(e) {
  var lock = LockService.getScriptLock();
  lock.tryLock(10000);

  try {
    var doc = SpreadsheetApp.openById(scriptProp.getProperty('key'));
    var sheet = doc.getSheetByName(sheetName);

    var headers = sheet.getRange(1, 1, 1, sheet.getLastColumn()).getValues()[0];
    var nextRow = sheet.getLastRow() + 1;

    var newRow = headers.map(function(header) {
      return header === 'timestamp' ? new Date() : e.parameter[header];
    });

    sheet.getRange(nextRow, 1, 1, newRow.length).setValues([newRow]);

    //send Email Notification
    var recipient = "aihduty@gmail.com";  // change to your email
    var subject = "new contact form submission";
    var body = "you received a new message:\n\n" +
               "name: " + e.parameter["name"] + "\n" +
               "message: " + e.parameter["message"] + "\n" +
               "phone: " + e.parameter["phone"] + "\n" +
               "email: " + e.parameter["email"];

    MailApp.sendEmail(recipient, subject, body);

    return ContentService
      .createTextOutput(JSON.stringify({ 'result': 'success', 'row': nextRow }))
      .setMimeType(ContentService.MimeType.JSON);
  }
  catch (error) {
    return ContentService
      .createTextOutput(JSON.stringify({ 'result': 'error', 'error': error }))
      .setMimeType(ContentService.MimeType.JSON);
  }
  finally {
    lock.releaseLock();
  }
}
```

- change this part of the code to your email

```jsx
var recipient = "aihduty@gmail.com";  // change to your email
```

- click on `Deploy` > `New Deployment`
- go on `Who has access` and choose `Anyone`
- click on `Deploy` and copy the web app URL
- paste the web app URL in `script.js`

```jsx
const scriptURL = 'your script url'
```

- test submitting a contact form on the portfolio website and see if it fills up a row and that you receive a notification email (if it does, you're done)
## ℹ️ source(s)
https://www.youtube.com/watch?v=0YFrGy_mzjY&t=6662s