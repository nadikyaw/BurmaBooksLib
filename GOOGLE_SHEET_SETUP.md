# Google Sheet Setup (Admin Panel)

Follow these steps to connect your website and enable automatic booking tracking.

## 1. Prepare the Google Sheet
1. Create a new Google Sheet.
2. Rename the first tab to **"Books"**. Add these headers in Row 1:
   - `Title`, `Author`, `Category`, `Status`, `Top`, `Img`, `ReturnDate`
3. Create a second tab and rename it to **"Bookings"**. Add these headers in Row 1:
   - `Timestamp`, `Book Title`, `Customer Name`, `Phone`, `Address`

## 2. Add the Admin Script
1. In your Google Sheet, go to **Extensions** > **Apps Script**.
2. Replace all code with this updated script:

```javascript
function doGet() {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Books");
  var data = sheet.getDataRange().getValues();
  var headers = data[0];
  var rows = data.slice(1);
  
  var json = rows.map(function(row) {
    var obj = {};
    headers.forEach(function(header, i) {
      obj[header.toLowerCase().replace(/\s/g, "")] = row[i];
    });
    return obj;
  });
  
  return ContentService.createTextOutput(JSON.stringify(json))
    .setMimeType(ContentService.MimeType.JSON);
}

function doPost(e) {
  var params = JSON.parse(e.postData.contents);
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Bookings");
  
  if (params.action === 'book') {
    sheet.appendRow([
      new Date(), 
      params.title, 
      params.name, 
      params.phone, 
      params.address
    ]);
  }
  
  return ContentService.createTextOutput("Success").setMimeType(ContentService.MimeType.TEXT);
}
```

## 3. Deploy
1. Click **Deploy** > **New deployment**.
2. Select **Web app**.
3. Execute as: **Me**.
4. Who has access: **Anyone**.
5. Click **Deploy** and **Copy the Web App URL**.

## 4. Connect to Website
1. Open `index.html`.
2. Find `const SCRIPT_URL = "..."` and paste your URL.
3. Refresh your website.

### How it works:
- **Search:** People can search by title, author, or category.
- **Booking:** When they click "Book Now", a form pops up.
- **Admin:** Once they confirm, the booking is saved to your "Bookings" sheet automatically, and they are sent to WhatsApp to finish the chat with you.
