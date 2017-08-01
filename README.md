SendBird Desk SDK Integration Guide for Android
===========
SendBird Desk is the Zendesk-integrated chat customer service platform built on SendBird SDK and API.

Desk Android SDK provides customer-side integration on your own application, so you can easily implement customers' **chat inquiry, inquiries inbox with UI theming**.  
Desk Android SDK requires devices running **Android 4.0 or higher** and **SendBird Android SDK 3.0.33 or higher**.

## Table of Contents

  1. [Installation](#installation)
  1. [Initialization](#initialization)
  1. [Creating a new ticket](#creating-a-new-ticket)
  1. [Loading inbox](#loading-inbox)
  1. [Receiving events](#receiving-events)
  
## Installation

First of all, you need SendBird App ID to start (It can be created on [SendBird Dashboard](https://dashboard.sendbird.com), but for Desk usage, you may need upgrade.),
so please contact [desk@sendbird.com](mailto:desk@sendbird.com) if you want one.

Installing the Desk SDK is a straightforward process if you're familiar with using external libraries or SDKs in your projects.
To install the Desk SDK using Gradle, add the following lines to your app-level `build.gradle` file.
```gradle
repositories {
    maven { url "https://raw.githubusercontent.com/smilefam/SendBird-SDK-Android/master/" }
    maven { url "https://raw.githubusercontent.com/smilefam/SendBird-Desk-SDK-Android/master/" }
}
```

And then add the following lines to your project-level `build.gradle` file.
```gradle
dependencies {
    compile 'com.sendbird.sdk:sendbird-android-sdk:3.0.33'
    compile 'com.sendbird.sdk:sendbird-desk-android-sdk:0.9.9'
}
```

## Initialization

Invoke `SendBirdDesk.init()` with your SendBird App ID before you use other features on SendBird Desk SDK.
```java
public class MyApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        SendBirdDesk.init(APP_ID, this);
    }
}
```

> Calling `SendBirdDesk.init()` on `Application.onCreate()` is highly recommended.

> You can use SendBird SDK with SendBird Desk SDK (for example, when you want to build your own messenger and also use Desk service together on your application.)
> In that case, you must use **the same APP_ID** for both SDK (`SendBird.init()` is not necessary, `SendBirdDesk.init()` is sufficient because it calls `SendBird.init()` inside).
And please note that SendBird SDK version must be 3.0.33 or higher to work with Desk SDK.

You should prepare FCM settings too. On your FirebaseMessagingService, add the following lines.
```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        if (SendBirdDesk.isSendBirdPushNotification(remoteMessage)) {
            SendBirdDesk.handlePushNotificationMessage(this, remoteMessage);
        }
    }
}
```
By just calling `SendBirdDesk.handlePushNotificationMessage()`, Desk SDK will build and pop-up notification from Desk messages for you.

After init, connect a user to SendBird's server using **User ID**.
This part is fully described on [SendBird SDK guide docs](https://docs.sendbird.com/android#authentication_3_connecting_with_userid),
and you can think of this process as a pre-step for Desk SDK, for it is built on SendBird SDK.
Below is the code snippet which can be found on Desk sample to show you what can be done after connection.
```java
SendBird.connect(USER_ID, new SendBird.ConnectHandler() {
    @Override
    public void onConnected(User user, SendBirdException e) {
        if (e != null) {
            return;
        }
  
        SendBird.updateCurrentUserInfo(USER_NAME, null, new SendBird.UserInfoUpdateHandler() {
            @Override
            public void onUpdated(SendBirdException e) {
                if (e != null) {
                    return;
                }
  
                SendBirdDesk.setUserInfo(USER_ID, null);  // Saves user information on Desk.
                
                SendBirdDesk.getOpenTicketCount(new SendBirdDesk.GetOpenTicketCountHandler() {
                    @Override
                    public void onResult(int count, SendBirdException e) {
                        if (e != null) {
                            return;
                        }
                        // Open tickets count can be read.
                    }
                });
                
                if (FirebaseInstanceId.getInstance().getToken() == null) return;
                SendBirdDesk.updatePushToken(FirebaseInstanceId.getInstance().getToken());  // Updates push token.
            }
        });
    }
});
```
  
Now your customers are ready to create chat tickets and start inquiry with your agents!

## Creating a new ticket

Creating a new ticket is as simple as just calling `SendBirdDesk.startChat()`. Ticket title and custom information can be passed at the same time.
Currently, at this Zendesk integration version, you can set custom information as Zendesk ticket object you want to create at agent side.
```java
JsonObject ticket = new JsonObject();
  
// Requester.
JsonObject requester = new JsonObject();
requester.addProperty("name", USER_NAME);
requester.addProperty("email", USER_ID);
ticket.add("requester", requester);
  
// Subject.
ticket.addProperty("subject", YOUR_TICKET_TITLE);
  
// Comment.
JsonObject comment = new JsonObject();
comment.addProperty("body", COMMENT_TO_DISPLAY_ON_ZENDESK_TICKET);
ticket.add("comment", comment);
  
// Tags.
JsonArray tags = new JsonArray();
tags.add("sendbird");
ticket.add("tags", tags);
  
JsonObject zendesk = new JsonObject();
zendesk.add("ticket", ticket);
  
SendBirdDesk.startChat(
        zendesk.get("ticket").getAsJsonObject().get("subject").getAsString(),
        zendesk.toString()
);
```

## Loading inbox
Inbox displays all the open tickets and closed ticket history for the customer.
Customers can move to inbox from chat screen by touching menu on top bar, but you can also directly let customers go to inbox in your application by calling
```java
SendBirdDesk.showInbox();
```

## Receiving events
You may want to gather your customers action to your own funnel analysis tools.
All customers action like text sending, file sending or menu touching will be called back on `SendBirdDesk.EventListener` so you can utilize it.
```java
public class MyApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
       
        SendBirdDesk.init(APP_ID, this);
        SendBirdDesk.setEventListener(new SendBirdDesk.EventListener() {
            @Override
            public void onEvent(String action, Map<String, String> data) {
                // Send to your own funnel analysis tools.
            }
        });
    }
}
```

The actions and data you can track at the moment are belows.

|Action|Data|Description|
| -- | -- | -- |
|CHAT_ENTER|title, status, ticket_id|User enters chat screen|
|CHAT_SEND_USER_MESSAGE|message|User sends text message|
|CHAT_ATTACH_FILE|file_name, file_size, mime_type|User attaches file message|
|CHAT_DOWNLOAD_AGENT_FILE|file_name, url|User downloads file message from agents|
|CHAT_CONFIRM_END_OF_CHAT|choice|User confirms end of chat from agents inquiry|
|CHAT_CLOSE_ALL|-|User closes screen by touching X button on toolbar of chat|
|CHAT_EXIT|-|User exits chat screen|
|WEB_VIEWER_ENTER|url|User enters web viewer|
|WEB_VIEWER_RELOAD|-|User touches reload button of web viewer|
|WEB_VIEWER_EXIT|-|User exits web viewer|
|PHOTO_VIEWER_ENTER|file_name, file_size, mime_type|User enters photo viewer|
|PHOTO_VIEWER_DOWNLOAD_FILE|file_name, url|User downloads file from photo viewer|
|PHOTO_VIEWER_EXIT|-|User exits photo viewer|
|VIDEO_PLAYER_ENTER|file_name, file_size, mime_type|User enters video player|
|VIDEO_PLAYER_DOWNLOAD_FILE|file_name, url|User downloads file from video player|
|VIDEO_PLAYER_EXIT|-|User exits video player|
|INBOX_ENTER|-|User enters inbox|
|INBOX_OPEN_TAB_SELECTED|-|User selects open tickets tab|
|INBOX_CLOSE_TAB_SELECTED|-|User selects closed tickets tab|
|INBOX_OPEN_TICKET_SELECTED|title, status, ticket_id|User selects an open ticket|
|INBOX_CLOSE_TICKET_SELECTED|title, status, ticket_id|User selects a closed ticket|
|INBOX_MOVE_TO_SETTINGS|-|User touches settings button on toolbar of inbox|
|INBOX_CLOSE_ALL|-|User closes screen by touching X button on toolbar of inbox|
|INBOX_EXIT|-|User exits inbox|
|SETTINGS_ENTER|-|User enters settings|
|SETTINGS_PUSH_ON|-|User sets on push notification|
|SETTINGS_PUSH_OFF|-|User sets off push notification|
|SETTINGS_CLOSE_ALL|-|User closes screen by touching X button on toolbar of settings|
|SETTINGS_EXIT|-|User exits settings|
