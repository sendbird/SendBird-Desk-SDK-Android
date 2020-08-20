# Senbird Desk - QuickStart for Android

## introduction
SendBird Desk is a chat customer service platform built on SendBird SDK and API.

Desk Android SDK provides customer-side integration on your own application, so you can easily implement a ticketing system with chat inquiry, inquiries inbox with UI theming. 

This repo was made to share a barebones quickstart implementation of how to use SendBird Desk.

It goes through the steps of:
- Connecting to SendBird
- Connecting to SendBird Desk
- Creating a Ticket
- Retrieving Closed Tickets

## Table of Contents

  1. [Create a Sendbird application](#create-a-sendbird-application)
  1. [Installation](#installation)
  1. [Initialization](#initialization)
  1. [Authentication](#authentication)
  1. [Setting customer customFields](#setting-customer-customfields)
  1. [Creating a new ticket](#creating-a-new-ticket)
  1. [Count of opened tickets](#count-of-opened-tickets)
  1. [Loading ticket list](#loading-ticket-list)
  1. [Confirming end of chat](#confirming-end-of-chat)
  1. [Handling ticket event](#handling-ticket-event)
  1. [Rich messages](#rich-messages)
  1. [Ticket Feedback](#ticket-feedback)
  1. [Error codes](#error-codes)

## Prerequisites
- Android 4.0 or later and SendBird Android SDK 3.0.55 or later

## Create a Sendbird application
  1. Login or Sign-up for an account at [dashboard](https://dashboard.sendbird.com/)
  1. Create or select an application on the SendBird Dashboard.
  1. Note the `Application ID` for future reference.
  1. [Contact sales](https://sendbird.com/contact-sales) to get the `Desk` menu enabled in the dashboard. 
  1. Sendbird Desk is available only for free-trial or Enterprise plan 
  
## Installation

Installing the Desk SDK is a straightforward process if you're familiar with using external libraries or SDKs in your projects.
To install the Desk SDK using Gradle, add the following lines to your project-level `build.gradle` file.
```gradle
repositories {
    maven { url "https://raw.githubusercontent.com/sendbird/SendBird-SDK-Android/master/" }
    maven { url "https://raw.githubusercontent.com/sendbird/SendBird-Desk-SDK-Android/master/" }
}
```

And then add the following lines to your app-level `build.gradle` file.
```gradle
dependencies {
    implementation 'com.sendbird.sdk:sendbird-android-sdk:3.0.141'
    implementation 'com.sendbird.sdk:sendbird-desk-android-sdk:1.0.8'
}
```

## Initialization

Invoke `SendBird.init()` with your SendBird App ID just like when you initialize SendBird SDK and then
call `SendBirdDesk.init()` to use SendBird Desk SDK's features. Please be sure to initialize SendBird SDK before SendBirdDesk SDK.
```java
public class MyApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        SendBird.init(APP_ID, this);
        SendBirdDesk.init();
    }
}
```

> Calling `SendBird.init()` and `SendBirdDesk.init()` on `Application.onCreate()` is highly recommended.

> Even you use SendBird Desk SDK, you have to handle chat messages thru SendBird SDK. SendBird Desk SDK provides add-on features like chat ticket creation and loading chat tickets.
Ticket is the concept that does not exist on SendBird SDK and newly introduced on SendBird Desk SDK to support customer service ticketing system.
Every ticket created will be assigned to the appropriate agents and it will have a mapping channel of SendBird SDK, so you can implement real-time messaging on tickets with SendBird SDK.
> While using SendBird Desk SDK, it is also possible that you implement your own chat service using SendBird SDK.
For example, if you are operating an on-demand service, you can add an in-app messenger (for your platform users) as well as customer service chat (between users and agents)
into your application or website by combination of SendBird SDK and SendBird Desk SDK.


## Authentication

After initialization, connecting to SendBird's server by SendBird SDK is required for real-time messaging. 
This part is fully described on [SendBird SDK guide docs](https://docs.sendbird.com/android#authentication_2_authentication).
Authentication of SendBird Desk `SendBirdDesk.authenticate()` is also a mandatory for you to use ticket related features.
Below is an example for SendBird SDK connection and SendBird Desk SDK authentication.
```java
SendBird.connect(userId, accessToken, new SendBird.ConnectHandler() {
    @Override
    public void onConnected(User user, SendBirdException e) {
        if (e != null) {
            // Error handling.
            return;
        }

        // Use the same user Id and access token used on SendBird.connect.
        SendBirdDesk.authenticate(userId, accessToken, new SendBirdDesk.AuthenticateHandler() {
            @Override
            public void onResult(SendBirdException e) {
                if (e != null) {
                    // Error handling.
                    return;
                }
                
                // Now you can create a ticket, get open ticket count and load tickets.
            }
        });
    }
});
```
  
Now your customers are ready to create chat tickets and start inquiry with your agents!

## Setting customer customFields

Customer information could be kept in `customFields`. 
`setCustomerCustomFields()` in `SendBirdDesk` lets the SDK set the `customFields` of the current customer. 
The `customFields` columns should be defined in SendBird Dashboard beforehand. 
Otherwise, the setting would be ignored.

```java
Map<String, String> customFields = new HashMap<>();
customFields.put("gender", "male");
customFields.put("age", String.valueOf(20));

SendBirdDesk.setCustomerCustomFields(customFields, new SendBirdDesk.SetCustomerCustomFieldsHandler() {
    @Override
    public void onResult(SendBirdException e) {
        if (e != null) {
            // Error handling.
            return;
        }
        
        // customer's customFields is rightly set
        // (or a certain key could get ignored if the key is not defined yet)
    }
});
```

## Creating a new ticket

Creating a new ticket is as simple as just calling `Ticket.create()`. Ticket title and user name can be passed at the same time.
The returned ticket will have a channel instance which can be accessed by `ticket.getChannel()`. So you can send messages to the channel using SendBird SDK.
For more detail of sending messages to channel, please refer to [SendBird SDK guide docs](https://docs.sendbird.com/android#group_channel_3_sending_messages).
Please notice that only after customers sending at least one message to the ticket, the ticket will be routed to the online agents so they can answer it.
```java
Ticket.create(ticketTitle, userName, new Ticket.CreateHandler() {
    @Override
    public void onResult(Ticket ticket, SendBirdException e) {
        if (e != null) {
            // Error handling.
           return;
        }
        // Now you can send messages to the ticket by ticket.getChannel().sendUserMessage() or sendFileMessage().
    }
});
```
> `Ticket.create()` has a overloaded method with `Priority` parameters so you can set the priority of ticket as well.
```java
Ticket.create(ticketTitle, userName, Priority priority, new Ticket.CreateHandler() {
    @Override
    public void onResult(Ticket ticket, SendBirdException e) {
        if (e != null) {
            // Error handling.
           return;
        }
        // Now you can send messages to the ticket by ticket.getChannel().sendUserMessage() or sendFileMessage().
    }
});
```
> `Ticket.create()` has a overloaded method with `groupKey` and `customField` parameters. The values could be evaluated when a ticket is created though it's used only in Dashboard currently. `groupKey` is the key of an agent group so that the ticket is assigned to the agents in that group. `customField` holds customizable data for the individual ticket.
```java
HashMap<String, String> customFields = new HashMap<>();
customFields.put("text", "hello");
customFields.put("number", "14");
customFields.put("select", "option2");

Ticket.create(ticketTitle, userName,
        "cs-team-1",    // groupKey
        customFields,   // customFields
        new Ticket.CreateHandler() {
    @Override
    public void onResult(Ticket ticket, SendBirdException e) {
        if (e != null) {
            // Error handling.
            return;
        }
        // Ticket is created with groupKey "cs-team-1" and customFields.
    }
});
```
> Each key in `customFields` should be preregistered in Dashboard. Otherwise, the key would be ignored.

## Setting Ticket customFields

Ticket information could be kept in `customFields`. 
`setCustomFields()` in `Ticket` lets the SDK set the `customFields` of the current ticket. 
The `customFields` columns should be defined in SendBird Dashboard beforehand. 
Otherwise, the setting would be ignored.

```java
Map<String, String> customFields = new HashMap<>();
customFields.put("gender", "male");
customFields.put("age", String.valueOf(20));

ticket.setCustomFields(customFields, new Ticket.SetCustomFieldHandler() {
    @Override
    public void onResult(Ticket ticket, SendBirdException e) {
        if (e != null) {
            // Error handling.
            return;
        }
        
        // ticket's customFields is rightly set
        // (or a certain key could get ignored if the key is not defined yet)
    }
});
```

## Setting Ticket priority

Ticket information could be kept in `Priority`. 
`setTicketPriority()` in `Ticket` lets the SDK set the `Priority` of the current ticket. 
If you didn't set the Priority of ticket the default `Priority` has been set to `Priority.MEDIUM`


```java
ticket.setTicketPriority(priority, new Ticket.SetTicketPriorityHandler() {
    @Override
    public void onResult(Ticket ticket, SendBirdException e) {
        if (e != null) {
            // Error handling.
            return;
        }
        
        // ticket's priority is rightly set
    }
});
```

## Count of opened tickets
When you need to display opened ticket count somewhere on your application, `Ticket.getOpenCount()` is useful.
```java
Ticket.getOpenCount(new Ticket.GetOpenCountHandler() {
    @Override
    public void onResult(int count, SendBirdException e) {
        if (e != null) {
            // Error handling.
            return;
        }

    }
});
```

## Loading ticket list
Usually you will design `Inbox` activity for open tickets and closed tickets history for your customer.
Open tickets and closed tickets can be loaded from `Ticket.getOpenedList()` and `Ticket.getClosedList()`.
Zero is a good start value of the offset, then the maximum 10 tickets will be returned for each call by last message creation time descending order.
Open ticket list and closed ticket list can be loaded like below:
```java
Ticket.getOpenedList(offset, new Ticket.GetOpenedListHandler() {
    @Override
    public void onResult(List<Ticket> tickets, boolean hasNext, SendBirdException e) {
        if (e != null) {
            // Error handling.
            return;
        }

        // offset += tickets.size(); for the next tickets.
        // This is the best place you display tickets on inbox. 
    }
});
```

```java
Ticket.getClosedList(offset, new Ticket.GetClosedListHandler() {
    @Override
    public void onResult(List<Ticket> tickets, boolean hasNext, SendBirdException e) {
        if (e != null) {
            // Error handling.
            return;
        }
        
        // offset += tickets.size(); for the next tickets.
        // This is the best place you display tickets on inbox. 
    }
});
```
> `Ticket.getOpenedList()` and `Ticket.getClosedList()` have overloaded methods with `customFieldFilter` parameter. Once you set `customField` to tickets, you can put `customFieldFilter` to `getOpenedList()` and `getClosedList()` in order to filter the tickets by `customField` values.

## Confirming end of chat
There are predefined rich messages on SendBird Desk and `Confirm end of chat` is one of them. For other rich messages, please refer to [Handling messages](#handling-messages).
All rich messages have message custom type (can be accessed by `UserMessage.getCustomType()` on SendBird SDK) as `SENDBIRD_DESK_RICH_MESSAGE`,
and `Confirm end of chat` message has custom data (can be accessed by `UserMessage.getData()` on SendBird SDK) as below:
```js
{
    "type": "SENDBIRD_DESK_INQUIRE_TICKET_CLOSURE",
    "body": {
        "state": "WAITING" // also can have "CONFIRMED", "DECLINED"
    }
}
```
This `Confirm end of chat` massage is initiated from agents to inquire closure of ticket to customers.
The initial `state` will be `WAITING` and you have to implement of updating the `state` according to customers action.
Usually, you can display `YES` or `NO` button like sample and update to `CONFIRMED` when customers touch `YES` button. Updating to `DECLINED` is also possible when customers touch `NO`.
For update the `state` of `Confirm end of chat`, please use `ticket.confirmEndOfChat()`.
```java
ticket.confirmEndOfChat(userMessage, confirm_or_decline, new Ticket.ConfirmEndOfChatHandler() {
    @Override
    public void onResult(Ticket ticket, SendBirdException e) {
        if (e != null) {
            // Error handling.
            return;
        }
        
        // You can update message UI like hiding YES NO buttons.
    }
});
```
At the moment, tickets will be closed (ticket close event will be sent to customers) only after customers confirming end of chat,  

## Handling ticket event
SendBird Desk SDK uses some predefined AdminMessage custom type (`AdminMessage.getCustomType()` on SendBird SDK) for ticket update notification.
This reserved custom type value is `SENDBIRD_DESK_ADMIN_MESSAGE_CUSTOM_TYPE` and at the moment there are 3 kinds of ticket event, which are `Ticket assign`, `Ticket transfer` and `Ticket close`.
Each event has the following `AdminMessage.getData()`:
```js
{
    "type": "TICKET_ASSIGN" // "TICKET_TRANSFER", "TICKET_CLOSE"
}
```
You can check these messages from `ChannelHandler.onMessageReceived()` callback on SendBird SDK.
SendBird Desk SDK internally tracks these events and update ticket status automatically. So when you see these events, you can directly get ticket object by `Ticket.getByChannelUrl()` and then use it for e.g. 
rendering assigned agent's profile or moving ticket from open list to closed list.

## Rich messages
Besides, `Confirm end of chat` message, URL preview is available as one of rich messages. (We are adding more very fast.)
URL preview message's `UserMessage.getCustomType()` is also the same as `Confirm end of chat`, so it is `SENDBIRD_DESK_RICH_MESSAGE`.
Its `UserMessage.getData()` has the following format:
```js
{
    "type": "SENDBIRD_DESK_URL_PREVIEW",
    "body": {
        "url": "string",
        "site_name": "string",
        "title": "string",
        "description": "string",
        "image": "string (image url)"
    }
}
```
Therefore, when this type of message is received on `ChannelHandler.onMessageReceived()` or `channel.getPreviousMessagesByTimestamp()`, you can parse the data and use it for URL preview rendering.
Also if you extract URL information from customers text, build above JSON, stringify it and then send it as custom data by `channel.sendUserMessage()`, agents can also see URL preview.

## Ticket Feedback
If Desk satisfaction feature is on, a message would come after closing the ticket. 
The message is for getting customer feedback including score and comment. 
The data of satisfaction form message looks like below.
```
{
    "type": "SENDBIRD_DESK_CUSTOMER_SATISFACTION",
    "body": {
        "state": "WAITING" // also can have "CONFIRMED",
        "customerSatisfactionScore": null, // or a number ranged in [1, 5]
        "customerSatisfactionComment": null // or a string (optional)
    }
}
```
Once the customer inputs the score and the comment, the data could be submitted by calling `ticket.submitFeedback(message, score, comment, callback)`. 
Then updated message is going to be sent in `ChannelHandler.onMessageUpdated(BaseChannel channel, BaseMessage message)`.

## Error Codes
In case of an API request failure, the `SendBirdException` parameter in a handler will contain the information about the error.
#### SendBirdException
|Property|Description|
|----|----|
|code|`SendBirdError.ERR_REQUEST_FAILED (800220)`|
|message|Detailed error message with specific error code, if exists.|

## Reference
Please see the following link for Android Desk SDK Documentation https://github.com/sendbird/SendBird-Desk-iOS-Framework
