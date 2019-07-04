function check_spend_and_warnings() {
  /* ##### CHANGE THESE VARIABLES ##### */
  var email_recipients = ['name.lastname@gmail.com'] //emails separated by comma
  var sms_recipients = ['+35812345678'] //phone numbers with country code separated with comma
  var username = 'XXXXXXXXXXXXXXXXXXXXXXXXX'; //46elk API username
  var password = 'XXXXXXXXXXXXXXXXXXXXXXXXX'; //46elk API password
  var reporting_url = "https://api.smartly.io/stats/v2/view/XXXXXXXXXXXXXXXXXXXXXXXXX?token=XXXXXXXXXXXXXXXXXXXXXXXXX&timeframe=yesterday&timezone=Europe/Helsinki&format=json"; //Smartly.io reportin API url
  
  //get spend and current time
  var date = new Date();
  var spend = get_spend(reporting_url);
  
  //get the sheet and the data on it
  var sheetName = 'FB_Spend';
  var range = SpreadsheetApp.getActiveSpreadsheet().getSheetByName(sheetName).getDataRange();
  var last_row = getFirstEmptyRow()-1;
  var values = range.getValues();
  var headers = values[0];
  var spendColumnIndex = getIndexOf(headers, 'Yesterday spend', sheetName);
  var dateColumnIndex = getIndexOf(headers, 'Log date', sheetName);
  var warningColumnIndex = getIndexOf(headers, 'Warnings send', sheetName);
  var warningCheckedColumnIndex = getIndexOf(headers, 'Warning checked', sheetName);
  
  //get two latest spend and dates
  var last_row_values_1 = values[last_row-1];
  var previous_spend_1 = last_row_values_1[spendColumnIndex];
  var previous_date_1 = last_row_values_1[dateColumnIndex];
  var warningChecked = last_row_values_1[warningCheckedColumnIndex];
  var last_row_values_2 = values[last_row-2];
  var previous_spend_2 = last_row_values_2[spendColumnIndex];
  var previous_date_2 = last_row_values_2[dateColumnIndex];

  //Set new time and spend
  var last_cell = range.getCell((last_row), (spendColumnIndex+1));
  last_cell.offset(1, 0).setValue(spend);
  last_cell.offset(1, -1).setValue(date);
  
  //compare date and spend
  var spend_diff_1 = Math.floor(((spend - previous_spend_1)/spend)*100);
  var spend_diff_2 = Math.floor(((spend - previous_spend_2)/spend)*100);
  
  //Send warning if the spend is was more than 50% less yesterday than the day before that. 
  if (spend_diff_1 >= 50 || spend_diff_1 <= -50 ) {
    var warning = 'Warning FB spend changed ' + spend_diff_1 + '% in 1 day';
    last_cell.offset(1, 1).setValue(warning);
    Logger.log(warning);
    sendEmailWarning(warning, email_recipients);
    sendSmsWarning(warning, sms_recipients, username, password);
  }
  else if (spend_diff_2 >= 50 || spend_diff_2 <= -50 ) {
    if (warningChecked != "ok") {
      var warning = 'Warning FB spend changed ' + spend_diff_2 + '% in 2 days';
      last_cell.offset(1, 1).setValue(warning);
      Logger.log(warning);
      sendEmailWarning(warning, email_recipients);
      sendSmsWarning(warning, sms_recipients, username, password);
    }
  }
}

function getFirstEmptyRow() {
  var sheet = SpreadsheetApp.getActiveSheet();
  var range = sheet.getDataRange();
  var values = range.getValues();
  for (var row=0; row<values.length; row++) {
    if (!values[row].join("")) break;
  }
  return (row+1);
}


function sendEmailWarning(warning, email_recipients) {
  for (var index in email_recipients) {
    var recipient = email_recipients[index]
    MailApp.sendEmail({
      to: recipient,
      subject: warning,
      htmlBody: warning
    });
  }
}

function sendSmsWarning(warning, sms_recipients, username, password) {
  var prop = PropertiesService.getScriptProperties();
  var auth = Utilities.base64Encode(username + ":" + password);
  
  for (var index in sms_recipients) {
    try {
      var response = UrlFetchApp.fetch("https://api.46elks.com/a1/sms", {
        "method": "post",
        "headers": { "Authorization": "Basic " + auth },
        "muteHttpExceptions": true,
        "payload": {
          "from" : 'WarningBot',
          "to" : sms_recipients[index],
          "message" : warning
        }
      });
    } catch (err) {
      Logger.log("SMS warning was not send");
    }
  }
}

function get_spend(reporting_url) {
  var options = { "method":"GET",
                 "contentType" : "application/json",
                 "muteHttpExceptions": true
                };
  var response = UrlFetchApp.fetch(reporting_url, options);
  var status = response.getResponseCode();
  var dataObject = JSON.parse(response)['report'];
  
  //get spend and current time
  var spend = dataObject['0']['2'];
  
  return spend
}

function getIndexOf(array, value, sheetName) {
  for (i = 0; i < array.length; i++) {
    if (typeof value === 'string') {
      if (array[i].toString().toLowerCase() === value.toLowerCase()) {
        return i;
      }
    }
    else {
      if (array[i] == value) {
        return i;
      }
    }
  }
  // If column is not found by name reference, throw an explicit error
  throw new Error(value + " column not found in sheet " + sheetName);
}
