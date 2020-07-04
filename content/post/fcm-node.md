---
title: Firebase Cloud Messaging
date: 2017-12-24 14:44:28
tags:
  - nodejs
  - firebase
---
Enable Firebase Cloud Messaging for your application.<!--more--> Push notifications are a great way to reach out the clients and send useful information or upgrades regarding your product. Firebase provides a cloud messaging service that is compatible with Android, iOS and Web. In this post we will build a small nodejs application that will send push notifications to the clients.

## Create a firebase project

Go to [Firebase console](https://console.firebase.google.com/) and log into it. Create a google account if you don't have one. Just click on Add project and fill the required details. 
![](/images/create_project.png)
Once the project is created you can view a rich dashboard with a lot of features. Let us move on and create the application to send notifications. We will come back to the console a little later.

## Node js admin SDK

Google provides firebase admin [SDK](https://firebase.google.com/docs/admin/setup) for interacting with various features provided by firebase like realtime database etc. Here we are going to use it for sending push notifications. IMO this is the easiest way to send a notification to Android and iOS apps. As of writing this post, Firebase FCM API was only available for node js. Google may extend this SDK to other languages as well.

![](node_only.png)

## Let's start the code

We are going create a small node js server app using express js and write a simple API that will send notification to the mobile device. Let's begin by installing express.

``` npm
npm install express --save
```
Now install the firebase admin SDK and save it to your package.json file
``` npm
npm install firebase-admin --save
```

### Creating the app

Create an app.js file and add the express and firebase sdk. 

{{< codeblock "app.js" "javascript"  >}}
// create an express app
var express = require('express');
var app = express();
// use body parser to to read json from the API request
var bodyParser = require('body-parser');
app.use(bodyParser.json());
// get the firebase admin sdk
var admin = require('firebase-admin');
var serviceAccount = require('./key.json');
{{< /codeblock >}}
### Get the service account key file

Notice the last line that references a key.json file. In order to get that file go to your Firebase project dashboard and click on the settings icon and then choose Project Settings. Then select Service Accounts from tab list. Once you click that you will find "Generate new private key" button at the bottom.

![](private_key.png)

### Implement the core function

After generating the key move it to a safe location. Do not commit this key file. After this write a function that takes the notification data and sends it to a particular device.

{{< codeblock "firebase.js" "javascript"  >}}}
var sendNotification = function(notificationData) {
    var payload = {
        data: {
          score: "850",
          time: "2:45"
        },
        notification: {
          title: notificationData.title,
          body: notificationData.body
        }
      };

      admin.initializeApp({
        credential: admin.credential.cert(serviceAccount),
        databaseURL: 'your_db_url'
      });

      /**
       * in case you do not have access to the key file as a whole then you can
       * admin.initializeApp({
            credential: admin.credential.cert({
            projectId: 'your_project_id',
            clientEmail: 'your_client_email',
            privateKey: 'your_private_key_from_the_json_file'
            }),
            databaseURL: 'your_db_url'
        });
        **/ 
      
        // the fcm token can also be a list of tokens 
      admin.messaging().sendToDevice(notificationData.fcmToken, payload).then(resolve =>{
        console.log('message sent successfully');
      }).catch(reject => {
        console.log('Error in push notification ', reject);
      });

}
{{< /codeblock >}}
In the above code we need to mention the firebase DB url. You can find this in the Database section of your dashboard. Replace "your_db_url" with the one mentioned in your console.

![](db_name.png)

### Create an API

Create an endpoint in order to access it from a rest client or another application.

{{< codeblock "app.js" "javascript"  >}}
app.post('/notify', function(req,res,next) {

    /**
     * request is of the format
     * {
	"title": "My title",
	"body": "My message body",
	"fcmToken": "your_device_token"  # can also be an array if you want to send it to multiple devices
}
     */
    var notificationData = req.body;
    sendNotification(notificationData); // add a callback if you want to
    next();

}) ;
{{< /codeblock >}}
It is a simple POST method that takes the title, message and the device token as the input and sends the message to the device. Note that you can also send the same message to multiple devices. In order to do that just change the type of fcmToken key from string to array of strings and then pass all the tokens.

### Fire the server

{{< codeblock "app.js" "javascript"  >}}
var server = require('http').Server(app);

var port = 3000
server.listen(port);
console.log('Listening on port '+port);
{{< /codeblock >}}

Please note that this is just the server side implementation of FCM and in order to receive this notification we need a sample android app but that's for another day (soon). The complete code for this post can be found [here](https://github.com/kagov/fcm-node). 