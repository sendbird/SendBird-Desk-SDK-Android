# [Sendbird](https://sendbird.com) Desk SDK for Android

![Platform](https://img.shields.io/badge/platform-ANDROID-orange.svg)
![Languages](https://img.shields.io/badge/language-JAVA-orange.svg)

## Table of contents

  1. [Introduction](#introduction)
  1. [Before getting started](#before-getting-started)
  1. [Getting started](#getting-started)
  1. [Creating your first ticket](#creating-your-first-ticket)
  1. [Implementation guide](#implementation-guide)

<br />

## Introduction

**Sendbird Desk** enables strong customer engagement through live, in-app support. The Desk SDK lets you easily initialize, configure, and build customer support-related functionality into your Android applications.

### How it works

Sendbird Desk is a plugin of the [Sendbird Chat Platform](https://sendbird.com/docs/chat) for managing tickets, and all Desk events are handled through the [Chat SDK](https://github.com/sendbird/SendBird-SDK-Android). 

Every ticket is assigned appropriate agents and will be directed to a chat's group channel, which implements real-time messaging on tickets with Sendbird Chat SDK. 

### Concepts

These are a few of the main components of Desk SDK. 

- **Channels**: The various ways through which support can be requested e.g. in-app chats from different OS platforms or social media like Facebook and Instagram.
- **Tickets**: A ticket is created when a customer and agent start a conversation and is seen as a unit of customer’s inquiry. There are five types of tickets.
- **Agents**: An agent receives the requests and also handles the conversation in the [Sendbird Dashboard](https://dashboard.sendbird.com/auth/signin). 
- **Admins**: Admins are agents who are granted the additional privileges of managing the overall dashboard settings and the tickets. 
- **Messages**: Desk has two types of messages that fall into further subtypes. The following table shows the hierarchical structure of messages. 

||Sender|Subtypes|
|---|---|---|
| User message| Agent or customer|Rich messages |
| Admin message |Sent from the Desk server without a specific sender |Notification messages and System messages |

> **Note**: Rich messages are further classified into [URL preview](#add-url-previews), [confirmation request for ticket closing](#request-confirmation-to-close-a-ticket), and [feedback request](#request-customer-feedback) messages. 

### More about Sendbird Desk SDK for Android

Find out more about Sendbird Desk SDK for Android on [Desk SDK for Android doc](https://sendbird.com/docs/desk/v1/android/quickstart/create-first-ticket). If you have any comments or questions regarding bugs and feature requests, visit [Sendbird community](https://community.sendbird.com).

<br />

## Before getting started

This section shows the prerequisites you need to check to use Sendbird Desk SDK for Android.

### Requirements

- `Android 5.0 (API level 21) or higher`
- `Java 8 or higher`
- `Support androidx only`
- `Android Gradle plugin 4.0.1 or higher`
- `Sendbird Chat SDK for Android 4.0.3 and later`

<br />

## Getting started

This section gives you information you need to get started with Sendbird Desk SDK for Android. 

### Try the sample app

Our sample app demonstrates the core features of Sendbird Desk SDK. Download the app from our GitHub repository to get an idea of what you can do with the actual SDK and to get started building your own project.
 
- https://github.com/sendbird/quickstart-desk-android

### Step 1: Create a Sendbird application from your dashboard

A Sendbird application comprises everything required in a chat service including users, message, and channels. To create an application:

1. Go to the Sendbird Dashboard and enter your email and password, and create a new account. You can also sign up with a Google account.
2. When prompted by the setup wizard, enter your organization information to manage Sendbird applications.
3. Lastly, when your dashboard home appears after completing setup, click **Create +** at the top-right corner.

Regardless of the platform, only one Sendbird application can be integrated per app; however, the application supports communication across all Sendbird’s provided platforms without any additional setup. 

> **Note**: All the data is limited to the scope of a single application, thus users in different Sendbird applications are unable to chat with each other.

### Step 2: Download and install the Desk SDK

Installing the Chat SDK is simple if you're familiar with using external libraries or SDKs. First, add the following code to your **root** `build.gradle` file:

```gradle
allprojects {
    repositories {
        ...
        maven { url "https://repo.sendbird.com/public/maven" }
    }
}
```

> **Note**: Make sure the above code block isn't added to your module `bundle.gradle` file.

Then, add the dependency to the project's top-level `build.gradle` file.

```gradle
dependencies {
    implementation 'com.sendbird.sdk:sendbird-desk-android-sdk:1.1.1'
}
```

> **Note**: Desk SDK versions `1.0.12` or lower can be downloaded from JCenter until February 1, 2022. SDK versions higher than `1.0.12` will be available on Sendbird's remote repository.

Alternatively, you can download the Desk SDK from this repository. Copy the Desk SDK into your `libs/` folder, and make sure you include the library in your `build.gradle` file as well.

## Creating your first ticket

After installation has been completed, a ticket can be created for communication between an agent and customer. Follow the step-by-step instructions below to create your first ticket. 

### Step 1: Initialize the Desk SDK

First, a ‘SendBirdDesk’ instance must be initialized when launching a client app. Call the SendbirdChat.init()’ and the ‘SendBirdDesk.init()’ on the ‘Application.onCreate()’. The SendbirdChat.init() should be initialized first by the APP_ID of your Sendbird application in the dashboard.  

```java
public class MyApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        final InitParams initParams = new InitParams(APP_ID, this, false);
        SendbirdChat.init(initParams, new InitResultHandler() {
            @Override
            public void onMigrationStarted() {
            }

            @Override
            public void onInitFailed(SendbirdException e) {
                // If initializing fails, this method is called.
            }

            @Override
            public void onInitSucceed() {
                // If initializing is successful, this method is called and you can proceed to the next step.
                // You can use all Sendbird APIs, including Connect, after init is completed in your app.
                SendBirdDesk.init();
            }
        });
    }
}
```

> **Note**: The same `APP_ID` should be used for both Desk and Chat SDKs. If you initiate the Sendbird Desk with a Sendbird instance of another `App_ID`, all existing data in the client app will be cleared. 

It is possible to use the Chat SDK only or both Chat and Desk SDKs together in your client app depending on the chat service you want to provide.

|SDK|Used for|
|---|---|
|Chat SDK|In-app messenger where customers can chat with each other.|
|Chat and Desk SDKs|Tickets where customers can chat with agents.|

```java
public class MyApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        final InitParams initParams = new InitParams(APP_ID, this, false);
        SendbirdChat.init(initParams, new InitResultHandler() {
            @Override
            public void onMigrationStarted() {
            }

            @Override
            public void onInitFailed(SendbirdException e) {
                // If initializing fails, this method is called.
            }

            @Override
            public void onInitSucceed() {
                // If initializing is successful, this method is called and you can proceed to the next step.
                // You can use all Sendbird APIs, including Connect, after init is completed in your app.
                SendBirdDesk.init();
            }
        });
    }
}
```

### Step 2: Authenticate to Sendbird server 

Customers can request support from various types of channels: in-app chats or social media such as Facebook, Instagram and Twitter. To use these support features of Desk SDK, the `SendBirdDesk` instance should be connected with Sendbird server depending on which channel the request is from: 

- **Sendbird Chat Platform**: Authenticate using the `authenticate()` method with their user IDs. 
- **Social media platforms**: No authentication needed as customers are automatically registered in the dashboard with their social media accounts.

Once authenticated, customers can live-chat with agents based on Sendbird Chat platform.

```java
SendbirdChat.connect(userId, accessToken, new ConnectHandler() {
    @Override
    public void onConnected(User user, SendbirdException e) {
        if (e != null) {    // error.
            return;
        }
        // Use the same user Id and access token used in the SendbirdChat.connect().
        SendBirdDesk.authenticate(userId, accessToken, new SendBirdDesk.AuthenticateHandler() {
            @Override
            public void onResult(SendbirdException e) {
                if (e != null) {    //error.
                    return;
                }

                // SendBirdDesk is now initialized, and the customer is authenticated.
            }
        });
    }
});
```

> **Note**: **Customers from Sendbird Chat platform** signifies users who are already authenticated with the Chat SDK. If you’re implementing Chat SDK and Desk SDK at the same time, [connect a user to Sendbird server with their user ID and access token](https://sendbird.com/docs/chat/v4/android/getting-started/send-first-message#2-get-started-3-step-3-initialize-the-chat-sdk) first.

### Step 3: Create a ticket

Implement the `Ticket.create()` method to create a new ticket either before or after the customer’s initial message. 

```java
Ticket.create(ticketTitle, userName, new Ticket.CreateHandler() {
    @Override
    public void onResult(Ticket ticket, SendbirdException e) {
    if (e != null) {    // error
        return;
        }

        // The ticket is created. Agents and customers can chat with each other by sending a message through the ticket.getChannel().sendUserMessage() or sendFileMessage().
    }
});
```
 
Once a ticket is successfully created on the Sendbird server, you can access the ticket and its channel in the `ticket.getChannel()` through the callback from the server. 

Before a customer sends the first message, agents can’t see the ticket in the dashboard and ticket assignment does not occur. When conversation starts, the ticket is assigned to an available agent by the Desk Dashboard while messages are sent and received through the Chat SDK.

You can use the following parameters when creating a ticket.

> **Note**: Only Groupkey and customFields need to be defined and are only accessible from the Dashboard. 

|Argument|Type|Description|
|---|---|---|
|TICKET_TITLE|string |Specifies the title of the ticket.|
|USER_NAME|string |Specifies the name of the user who submits or receives the ticket.|
|GROUP_KEY|string | Specifies the identifier of a specific team.|
|customFields|nested object|Specifies additional information of the ticket that consists of **key-value** custom items. Only custom fields already registered in **Settings** > **Ticket** fields in your dashboard can be used as a key. |
|PRIORITY |string |Specifies the priority value of the ticket. Higher values stand for higher priority. Valid values are **LOW**, **MEDIUM**, **HIGH** and **URGENT**. |
|RELATED_CHANNEL_URLS|array | Specifies group channels in Sendbird Chat platform that are related to this ticket and consists of channel URLs and channel names. Up to 3 related channels can be added.|

```java
Map<String, String> customFields = new HashMap<>();
customFields.put("product", "desk");
customFields.put("line", "14");
customFields.put("select", "option2");

Ticket.create(TICKET_TITLE, USER_NAME,
    "cs-team-1",                // GROUP_KEY
    customFields,               // CUSTOM_FIELDS
    PRIORITY,
    RELATED_CHANNEL_URLS,
    new Ticket.CreateHandler() {
        @Override
        public void onResult(Ticket ticket, SendbirdException e) {
            if (e != null) {    // Error.
                return;
            }
            // The ticket is created with parameters.
        }
    }
);
```

<br />

## Implementation guide

This section details the procedure to handle and close a ticket from your client app. 

### Add custom information to a ticket

Use the `ticket.setCustomFields()` method to add additional information about a specific ticket.

```java
Map<String, String> customFields = new HashMap<>();
customFields.put("product", "Desk");
customFields.put("line", String.valueOf(30));

ticket.setCustomFields(customFields, new Ticket.SetCustomFieldHandler() {
    @Override
    public void onResult(Ticket ticket, SendbirdException e) {
        if (e != null) {    // Error.
            return;
        }

        // Custom fields for the ticket are set.
        // Some fields can be ignored if their keys aren't registered in the dashboard.
    }
});
```

> **Note**: Only custom fields registered in **Desk** > **Settings** > **Ticket fields** of your dashboard can be used as a key.

### Add custom information to a customer

Use the `setCustomerCustomFields()` method of the ‘SendBirdDesk’ to make your customers add additional information about themselves.

> **Note**: Only custom fields registered in **Desk** > **Settings** > **Customer fields** of your dashboard can be used as a key.

```java
Map<String, String> customFields = new HashMap<>();
customFields.put("gender", "female");
customFields.put("age", String.valueOf(30));

SendBirdDesk.setCustomerCustomFields(customFields, new SendBirdDesk.SetCustomerCustomFieldsHandler() {
    @Override
    public void onResult(SendbirdException e) {
        if (e != null) {    // Error.
            return;
        }

        // Custom fields for the customer are set.
        // Some fields can be ignored if their keys aren't registered in the dashboard.
    }
});
```

### Add custom information to a customer

Use the `setCustomerCustomFields()` method of the `SendBirdDesk` to make your customers add additional information about themselves.

> **Note**: Only custom fields registered in **Settings** > **Customer fields** of your dashboard can be used as a key.

```java
Map<String, String> customFields = new HashMap<>();
customFields.put("gender", "female");
customFields.put("age", String.valueOf(30));

SendBirdDesk.setCustomerCustomFields(customFields, new SendBirdDesk.SetCustomerCustomFieldsHandler() {
    @Override
    public void onResult(SendbirdException e) {
        if (e != null) {    // Error.
            return;
        }

        // Custom fields for the customer are set.
        // Some fields can be ignored if their keys aren't registered in the dashboard.
    }
});
```

### Add related channels

Related channels indicate group channels in Sendbird Chat platform that are related to a ticket. When creating a ticket, pass the `channel_url`s of the related group channels as an argument to the `relatedChannelUrls` parameter in the `Ticket.create()` method. To update related channels, use the `ticket.setRelatedChannelUrls()` instead. The `ticket.relatedChannels` property in the callback indicates the group channel object of related channels and it contains channel names and their URLs.

```java
ticket.setRelatedChannelUrls(RELATED_CHANNEL_URLS, new Ticket.SetRelatedChannelUrlsHandler() {
    @Override
    public void onResult(Ticket ticket, SendbirdException e) {
        if (e != null) {    // Error.
            return;
        }

        // The ticket.relatedChannels property has been updated.
    }
});
```

> **Note**: Up to 3 related channels can be added per ticket.

### Add URL previews

With URL previews, your application users can meet their expectations of what they’re going to get before they open the link during the conversations.

To preview URLs, every text message should be checked if it includes any URLs. When a text message including a URL is successfully sent, the URL should be extracted and passed to Sendbird server using the `getUrlPreview()` method. Set the parsed data received from the server as a `JSON` object and stringify the object to pass it as an argument to a parameter in the `updateUserMessage()` method. Then the updated message with URL preview is delivered to the client apps through the `onMessageUpdated()` method of the channel event handler.

```java
ticket.getChannel().sendUserMessage(TEXT, new UserMessageHandler() {
    @Override
    public void onResult(UserMessage userMessage, SendbirdException e) {
        if (e != null) {    // Error.
            return;
        }   
        
        List<String> urls = extractUrlsFromMessage(userMessage.getMessage());
        if (urls.size() > 0) {
            String strUrlPreview = toJsonString(getOGTagsWithUrl(urls.get(0)));
            UserMessageUpdateParams updateParams = new UserMessageUpdateParams(TEXT);
            updateParams.setData(strUrlPreview);
            updateParams.setCustomType("SENDBIRD_DESK_RICH_MESSAGE");
            ticket.getChannel().updateUserMessage(userMessage.getMessageId(), updateParams, new UserMessageHandler() {
                @Override
                public void onResult(UserMessage userMessage, SendbirdException e) {
                    if (e != null) {    // Error.
                        return;
                    }
                }
            });
        } 
    }
});
```

> **Note**: Go to [Github page](https://github.com/sendbird/quickstart-desk-android) and refer to the `updateUserMessageWithUrl()` and `UrlPreviewAsyncTask` class in the sample code. You’ll get an idea on how to implement methods in the above sample code, such as the `extractUrlsFromMessage()`, `getOGTagsWithUrl()`, and `toJsonString()`, which aren’t actual code but intended to help you understand the overall flow to use URL previews.

In the channel event handler's `onMessageUpdated()` method, you can find the data for URL preview in the `message.data` property as below.

```json
{
    "type": "SENDBIRD_DESK_URL_PREVIEW",
    "body": {
        "url": "https://sendbird.com/",
        "site_name": "Sendbird",
        "title": "Sendbird - A Complete Chat Platform, Messaging and Chat SDK and API",
        "description": "Sendbird's chat, voice and video APIs and SDKs connect users through immersive, modern communication solutions that drive better user experiences and engagement.",
        "image": "https://6cro14eml0v2yuvyx3v5j11j-wpengine.netdna-ssl.com/wp-content/uploads/sendbird_thumbnail.png"
    }
}
```
 
### Receive system messages

Admin messages are customizable messages that are sent by the system, and there are 2 types of admin messages. **Notifications** are messages that are sent and displayed to both customers and agents, such as welcome messages or delay messages. **System messages** are messages sent and displayed to agents in the **Ticket details view** when a ticket has some changes, such as changes in ticket status and assignee.

> **Note**: You can customize notifications in **Desk** > **Settings** > **Triggers**, and system messages in **Desk** > **Settings** > **System messages** in your dashboard.

When the client app receives the message through the ‘onMessageReceived()’ method of the channel event handler, system messages are distinguished from notification messages by the value of the `message.custom_type`, and their subtype is specified in the `message.data` as below.

```json
{
    "message_id" : 40620745,
    "type": "ADMM",
    "custom_type": "SENDBIRD_DESK_ADMIN_MESSAGE_CUSTOM_TYPE",
    "data": "{\"type\": \"SYSTEM_MESSAGE_TICKET_ASSIGNED_BY_SYSTEM\", \"ticket\": <Ticket Object>}",
    "message": "The ticket is automatically assigned to Cindy."
}
```

> **Note**: The `transfer` appears only when the `data` has `SYSTEM_MESSAGE_TICKET_TRANSFERRED_BY_AGENT`.

System messages are intended to be displayed for agents only. Refer to the following sample code to avoid displaying them to your customers.

```java
public static boolean isVisible(BaseMessage message) {
    if (message instanceof AdminMessage) {
        String data = message.getData();
        if (!TextUtils.isEmpty(data)) {
            String customType = message.getCustomType();
            boolean isSystemMessage = ADMIN_MESSAGE_CUSTOM_TYPE.equals(customType);
            
            JsonObject dataObj = new JsonParser().parse(data).getAsJsonObject();
            String type = dataObj.get("type").getAsString();
            
            return !isSystemMessage
                && !EVENT_TYPE_ASSIGN.equals(type)
                && !EVENT_TYPE_TRANSFER.equals(type)
                && !EVENT_TYPE_CLOSE.equals(type);
        }
    }
    
    return true;
}
```

### Request confirmation to close a ticket

While admins have permission to directly close a ticket, agents can either close a ticket as admins do or ask customers whether to close a ticket, depending on the agent permission setting. The confirmation request message can have 3 types of state as below.

|State|Description|
|---|---|
|WAITING |Set when an agent sends a confirmation request message. |
|CONFIRMED| Set when a customer agrees to close the ticket. (Default: **true**)|
|DECLINED |Set when a customer declines to close the ticket. (Default: **false**)|

When a customer replies to the message, the response true (agree) or false (decline) is sent to Sendbird server as `CONFIRMED` or `DECLINED` by calling the `Ticket.confirmEndOfChat()` method.

```java
ticket.confirmEndOfChat(USER_MESSAGE, true|false, new Ticket.ConfirmEndOfChatHandler() {
    @Override
    public void onResult(Ticket ticket, SendbirdException e) {
        if (e != null) {    // Error.
            return;
        }
        
        // You can update the UI of the message. For example, you can hide YES and No buttons.
    }
});
```

```json
{
    "type": "SENDBIRD_DESK_INQUIRE_TICKET_CLOSURE",
    "body": {
        "state": "CONFIRMED"
    }
}
```

### Request customer feedback

You can send a message to customers right after closing a ticket to ask whether they are satisfied with the support provided through the ticket. When the **Customer satisfaction rating** feature is turned on in your dashboard, customers will get a message asking to give a score and leave a comment as feedback. The message can have 2 states as below.

|State|Description|
|---|---|
|WAITING|Set when an agent sends a customer feedback request message.|
|CONFIRMED|Set when a customer sends a response.|

When a customer replies to the message, their score and comment for the ticket are sent to the Desk server by calling the `ticket.submitFeedback()` method. Then, the state of the confirmation request message is changed to `CONFIRMED`.

```java

ticket.submitFeedback(USER_MESSAGE, SCORE, COMMENT, net Ticket.SubmitFeedbackHandler() {
    @Override
    public void onResult(Ticket ticket, SendbirdException e) {
        if (e != null) {    // Error.
            return;
        }
    }
});

```

Sendbird Desk server notifies the customer’s client app of updates through the `onMessageUpdate()` method of the channel event handler.

```java
public void onMessageUpdated(final BaseChannel channel, final BaseMessage message) {
    Ticket.getByChannelUrl(channel.getUrl(), new Ticket.GetByChannelUrlHandler() {
        @Override
        public void onResult(Ticket ticket, SendbirdException e) {
            if (e != null) return;

            String data = message.getData();
            if (!TextUtils.isEmpty(data)) {
                JsonObject dataObj = new JsonParser().parse(data).getAsJsonObject();
                String type = dataObj.get("type").getAsString();
                boolean isFeedbackMessage = "SENDBIRD_DESK_CUSTOMER_SATISFACTION".equals(type);
                if (isFeedbackMessage) {
                    JsonObject feedback = dataObj.get("body").getAsJsonObject();
                    String state = feedback.get("state").getAsString();
                    switch (state) {
                        case "CONFIRMED":
                            // TODO: Implement your code for the UI when there is a response from the customer.
                            break;
                        case "WAITING":
                            // TODO: Implement your code for the UI when there is no response from the customer.
                            break;
                    }
                }
            }
        }
    });
}
```

> **Note**: You can find the stringified `JSON` object of the following in the `message.data` property within the `onMessageUpdate()` method of the channel event handler.

```json
{
    "type": "SENDBIRD_DESK_CUSTOMER_SATISFACTION",
    "body": {
        "state": "CONFIRMED",
        "customerSatisfactionScore": 3,                           
        "customerSatisfactionComment": "It was really helpful :)"
    }
}
```

### Reopen a closed ticket

A closed ticket can be reopened by using the `reopen()` method in the `Ticket`.

```java
ticket.reopen(new Ticket.ReopenHandler() {
    @Override
    public void onResult(Ticket ticket, SendbirdException e) {
        if (e != null) {    // Error.
            return;
        }     
    }
});
```

### Retrieve a list of tickets

You can retrieve a list of the current customer’s open and closed tickets by using the `Ticket.getOpenedList()` and `Ticket.getClosedList()`.

You can design an inbox activity for open tickets and closed tickets history for your customer. Zero is a good start value, then the maximum 10 tickets will be returned for each call by last message creation time descending order. 

> **Note**: Only 10 tickets can be retrieved per request by message creation time in descending order.

```java
// getOpenedList()
Ticket.getOpenedList(OFFSET, new Ticket.GetOpenedListHandler() {
    @Override
    public void onResult(List<Ticket> tickets, boolean hasNext, SendbirdException e) {
        if (e != null) {    // Error.
            return;
        }

        // offset += tickets.size(); for the next tickets.
        // TODO: Implement your code to display the ticket list. 
    }
});
```

```java
// getClosedList() 
Ticket.getClosedList(OFFSET, new Ticket.GetClosedListHandler() {
    @Override
    public void onResult(List<Ticket> tickets, boolean hasNext, SendbirdException e) {
        if (e != null) {    // Error.
            return;
        }

        // offset += tickets.size(); for the next tickets.
        // TODO: Implement your code to display the ticket list. 
    }
});
```

For tickets set with custom fields, you can add a filter to the `getOpenList()` and `getClosedList()` to sort tickets by keys and values of custom fields.

```java
Map<String, String> customFieldFilter = new HashMap<>();
customFieldFilter.put("subject", "doggy_doggy");

Ticket.getOpenedList(OFFSET, customFieldFilter, new Ticket.GetOpenedListHandler() {
    @Override
    public void onResult(List<Ticket> tickets, boolean hasNext, SendbirdException e) {
        if (e != null) {    // Error.
            return;
        }

        List<Ticket> openedTicket = tickets;
        // offset += tickets.length; for the next tickets.
        // TODO: Implement your code to display the ticket list.
    }
});
```

### Retrieve a ticket

You can retrieve a specific ticket with its channel URL.

```java
Ticket.getByChannelUrl(channel.getUrl(), new Ticket.GetByChannelUrlHandler() {
    @Override
    public void onResult(Ticket ticket, SendbirdException e) {
        if (e != null) {    // Error.
            return;
        }
    }
});
```

### Display open ticket count

You can display the number of open tickets on your client app by using the `Ticket.getOpenCount()`.

```java
Ticket.getOpenCount(new Ticket.GetOpenCountHandler() {
    @Override
    public void onResult(int count, SendbirdException e) {
        if (e != null) {    // Error.
            return;
        }

        // TODO: Implement your code with the result value.
    }
});
```

### Close a ticket

Use the `ticket.close()` method to allow customers to directly close a ticket on their client app so that agents can quickly switch to other customer inquiries without delay or a customer’s confirmation.

```java
ticket.close(CLOSE_COMMENT, new Ticket.CloseHandler() {
    @Override
    public void onResult(Ticket ticket, SendbirdException e) {
        if (e != null) {    // Error.
            return;
        }
     
        // TODO: Implement your code to close a ticket.
    }
});
```

### Error Codes

In case of an API request failure, the `SendbirdException` parameter in a handler will contain the information about the error.

#### - SendbirdException

|Property|Description|
|---|---|
|code|SendBirdError.ERR_REQUEST_FAILED (800220)|
|message|Detailed error message with specific error code, if exists.|

