# FB spend warnings

Simple Google Sheets script for sending a warning with SMS and email if Facebook spend drops or increases 50% in 1-2 days. The script tracks the changes for two days because if your campaign breaks in the evening the it will not notice the change in the spend until the second day. 

The script uses [Smartly.io's](https://www.smartly.io/) Facebook marketing tool to fetch the Facebook spend, [46Elks](https://46elks.com/) API to send the SMS and Google's free [MailApp](https://developers.google.com/apps-script/reference/mail/mail-app) to send the email. 

Feel free to commit and make it more general. 

Google sheets template [here](https://docs.google.com/spreadsheets/d/1KaxHviDif4R0QDcuXmvZy0ih6uUZ_-vvlxZGFi-fb78/edit?usp=sharing). 

## How to use the sheet

If you get a warning from the spend change, check if there are some errors in your campaigns or in the productfeed. After you have fixed the problem mark "Warning checked" column to "ok" so you won't get notified about the same issue tomorrow. 


## Future development needs

* Facebook API integration
* Hourly spend tracking
