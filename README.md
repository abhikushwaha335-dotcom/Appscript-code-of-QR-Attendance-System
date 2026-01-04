# Appscript-code-of-QR-Attendance-System

---
## Important links:
- **Google Form Link:https://forms.gle/WGitvtaTJMnzi24y9**
- **Google Sheet Link:https://docs.google.com/spreadsheets/d/1WG-ypzmuwKhGe-Rja-ydxMNsuUvGwmR2Co_BxlYI0cM/edit?usp=sharing**
- **Pabbly Connect Workflow Link:https://connect.pabbly.com/v2/app/workflow/share/DEQDMVAHUzBSGAljAmdQdwoeBwNQCQZkV05TQVdYB39UGQNWUxJdN1wQAiBUNVIzURgFbwdbDDcLHwQACF5TIAceAj5XVFUvVxoBelQZDzwMUgMrUDE#**
---
  
## Apps Script Codes:-

**We can directly apply these codes from Google Sheets also by:**
**open Google Sheet➡️ Extensions➡️ Apps Script.**

### Code.gs:

-----
```code.gs
function doGet() {
  return HtmlService.createHtmlOutputFromFile('Index')
    .setXFrameOptionsMode(HtmlService.XFrameOptionsMode.ALLOWALL);}

function processLocation(data)
{
  if (!data || data.lat == null || data.lng == null) return;

  var ss = SpreadsheetApp.openById('1WG-ypzmuwKhGe-Rja-ydxMNsuUvGwmR2Co_BxlYI0cM');       // same ID as now
  var sheet = ss.getSheetByName('Form responses 1');     // exact sheet tab name

  var lat = data.lat;
  var lng = data.lng;
  var mapsLink = 'https://maps.google.com/?q=' + lat + ',' + lng;

  var lastRow = sheet.getLastRow();    // row where the latest form response is

 
  sheet.getRange(lastRow, 5).setValue(lat);       // column E
  sheet.getRange(lastRow, 6).setValue(lng);       // column F
  sheet.getRange(lastRow, 7).setValue(mapsLink);  // column G
}

function onFormSubmit(e) {
    try {
    // e.values: [Timestamp, Name, Roll, Department, Lat, Lng, MapsLink, ...] 
    var row = e.values;     // always exists when triggered correctly
    
    var payload = {
      timestamp: row[0] || '',
      student_name: row[1] || '',
      roll_no: row[2] || '',
      department_section: row[3] || '',
      latitude: row[4] || '',
      longitude: row[5] || '',
      maps_link: row[6] || ''
    };

    var webhookUrl = 'https://connect.pabbly.com/workflow/sendwebhookdata/IjU3NjcwNTZkMDYzMTA0Mzc1MjZkNTUzMjUxMzMi_pc';

    var options = {
      method: 'post',
      contentType: 'application/json',
      payload: JSON.stringify(payload),
      muteHttpExceptions: true
    };

    var res = UrlFetchApp.fetch(webhookUrl, options);
    Logger.log(res.getResponseCode());
    Logger.log(res.getContentText());

  } 
  
  catch (err) {
    Logger.log('onFormSubmit error: ' + err);
    throw err; // so you can see it in Executions
  }
}
 ```
-----

**Make a index.html file in the apps script then copy the below code in it.**
## index.html code:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Location Capture</title>
</head>
<body>
  <h3>Allow location access to continue...</h3>
  <script>
    navigator.geolocation.getCurrentPosition(function(position)  {
      var data = {lat: position.coords.latitude, lng: position.coords.longitude};
      google.script.run.withSuccessHandler(function() {
          alert('Location saved!'); window.close();
      }).processLocation(data);
    }, function() { alert('Location denied.'); window.close(); });
  </script>
</body>
</html>

