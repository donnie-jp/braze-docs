---
nav_title: "POST: Send Transactional Messages via API Triggered Delivery"
page_order: 4
layout: api_page
page_type: reference
platform: API
tool:
  - Campaigns
hidden: true
description: "This article outlines details about the Send Transactional Message via API Triggered Delivery Braze endpoint."
---
{% api %}
# Sending Transactional Messages via API Triggered Delivery
{% apimethod post %}
/transactional/v1/campaigns/YOUR_CAMPAIGN_ID_HERE/send
{% endapimethod %}

The Send Transactional Message endpoint allows you to send immediate, ad-hoc messages to designated users. Similar to the [Send triggered campaign endpoint]({{site.baseurl}}/api/endpoints/messaging/send_messages/post_send_triggered_campaigns/) endpoint, this campaign type allows you to house message content inside of the Braze dashboard while dictating when a message is sent, and to whom via your API.  Unlike the Send triggered campaign endpoint which accepts an audience or segment to send a message too, a request to the send transactional message endpoint must specify a single user either by External User ID or by User Alias as this campaign type is purpose-built for 1:1 messaging of alerts like order confirmations or password resets. 

Please note that to send messages with this endpoint, you must have a Campaign ID, created when you build an [Transactional Campaign]({{site.baseurl}}/api/api_campaigns/transactional_campaigns).

Transactional Messaging is currently available for email as part of select Braze packages. Please reach out to your Braze Customer Success Manager for more details.

{% alert important %}
__Looking for the `api_key` parameter?__<br>As of May 2020, Braze has changed how we read API keys to be more secure. Now API keys must be passed as a request header, please see `YOUR_REST_API_KEY` within the __Example Request__ below.
{% endalert %}

## Request Body

```
Content-Type: application/json
Authorization: Bearer YOUR_REST_API_KEY
```

```json
{
  "external_send_id": (optional, string) see external_send_id below,
  "trigger_properties": (optional, object) personalization key-value pairs that will apply to the user in this request,
  "recipient": (required, object)
    {
      // Either "external_user_id" or "user_alias" is required. Requests must specify only one.
      "user_alias": (optional, User Alias Object) User Alias of the user to receive message,
      "external_user_id": (optional, string) External Id of user to receive message,
      "attributes": (optional, object) fields in the attributes object will create or update an attribute of that name with the given value on the specified user profile before the message is sent and existing values will be overwritten
    }
}
```

### Request Parameters

| Parameter | Required | Data Type | Description |
| --------- | ---------| --------- | ----------- |
|`external_send_id`| Optional | String |  A Base64 compatible string. Validated against the following regex `/^[a-zA-Z0-9-_+\/=]+$/`. This optional field allows you to pass an internal identifier for this particular send which will be included in events sent from the Transactional HTTP event postback. When passed, this identifier will also be used as a deduplication key, which Braze will store for 24 hours. Passing the same identifier in another request will not result in a new instance of a send by Braze for 24 hours.|
|`trigger_properties`|Optional|Object|Personalization key-value pairs that will apply to the user in this request|
|`recipient`|Required|Object|The user you are targeting this message to|
{: .reset-td-br-1 .reset-td-br-2 .reset-td-br-3  .reset-td-br-4}

### Request Components
- [Recipients]({{site.baseurl}}/api/objects_filters/recipient_object/)
- [Trigger Properties]({{site.baseurl}}/api/objects_filters/trigger_properties_object/)
- [User Alias Object]({{site.baseurl}}/api/objects_filters/user_alias_object/)
- [User Attributes Object]({{site.baseurl}}/api/objects_filters/user_attributes_object/)
- [API Parameters]({{site.baseurl}}/api/parameters)
<br><br>

### Example Request
```
curl -X POST \
  -H 'Content-Type:application/json' \
  -H 'Authorization: Bearer YOUR-REST-API-KEY' \
  -d '{
        "external_send_id" : YOUR_BASE64_COMPATIBLE_ID
        "trigger_properties": {
          "example_string_property": YOUR_EXAMPLE_STRING,
          "example_integer_property": YOUR_EXAMPLE_INTEGER
        },
        "recipient": {
          "external_user_id": TARGETED_USER_ID_STRING
        }
      }' \
  https://rest.iad-01.braze.com/transactional/v1/campaigns/YOUR_CAMPAIGN_ID_HERE/send
```

## Response Details
The Send Transactional Message endpoint will respond with the message's `dispatch_id` which represents the instance of this message send. This identifier can be used along with events from the Transactional HTTP event postback to trace the status of an individual message sent to a single user. 

### Example response
```json
{
    "dispatch_id": Braze generated Unique ID of the instance of this send
    "status": Current status of the message
}
```

## Transactional HTTP Event Postback
All Transactional Messages are complimented with event status postbacks sent as an HTTP request back to your specified URL. This will allow you to evaluate the message status in real-time and take action to reach the user on another channel if the message goes undelivered, or fallback to an internal system if Braze is experiencing latency. 

In order to associate the incoming events to a particular instance of send, you can choose to either capture and store the Braze Dispatch ID returned in the API response as detailed above, or pass your own identifier to the `external_send_id` field. An example of a value you may choose to pass to that field may be an order ID, where after completing order 1234, an order confirmation message is triggered to the user through Braze and `external_send_id : 1234` is included in the request. All following event postbacks such as `Sent` and `Delivered` will include `external_send_id : 1234` in the payload allowing you to confirm that user successfully received their order confirmation email. 

To get started using the Transactional HTTP Event Postback, please provide your desired URL where you would like to receive the postback events to your CSM.


### Postback Body

```json
{
  "dispatch_id": (string, Braze generated Unique ID of the instance of this send),
  "status": (string, Current status of message from fields below)
  "external_send_id": (string, If provided at the time of the request, Braze will pass your internal identifier for this instance of a send for all postbacks)
  "metadata" : (object, timestamps relating to the execution of an event. Sent events will have a timestamp reflecting when the message was sent, in addition to timestamps reflecting the received_at, enqueued_at, and executed_at to show the time it took Braze to process the send. All other events will have a single timestamp field showing the time the event was processed.
}
```

### Example Postback
```json
// Sent Event
{
    "dispatch_id": "497a72794391cca2ea7a8bcfa6113b30",
    "external_send_id" : "11dbc68a-e2fc-11ea-87d0-0242ac130003", 
    "status": "sent",
    "metadata": {
      "received_at": "2020-08-18T16:25:45.000+00:00",
      "enqueued_at": "2020-08-18T16:25:45.000+00:00",
      "executed_at": "2020-08-18T16:25:45.000+00:00",
      "sent_at": "2020-08-18T16:25:45.000+00:00"
    }
  }


// Delivered Event
{
    "dispatch_id": "497a72794391cca2ea7a8bcfa6113b30",
    "external_send_id" : "11dbc68a-e2fc-11ea-87d0-0242ac130003",
    "status": "delivered",
    "metadata": {
      "delivered_at": "2020-08-18T16:25:47.000+00:00"
    }
  }
```

|  Status | Description |
| ------------ | ----------- |
| `Sent` | Message successfully dispatched to Braze's email sending partner  |
| `Processed` | Email sending partner has successfully received and prepared the message for delivery  |
| `Aborted` | Braze was unable to successfully dispatch the message due to the user not having an emailable address, or liquid abort logic was called in the message body. Includes `reason` field indicating why the message was aborted |
|`Delivered`| Message was accepted by the user's email inbox provider |
|`Bounced`| Message was rejected by the user's email inbox provider. Includes `reason` field reflecting the bounce error provided by the inbox provider |
{: .reset-td-br-1 .reset-td-br-2}

{% endapi %}

[41]: https://dashboard-01.braze.com/app_settings/developer_console/activitylog/
[42]: {{site.baseurl}}/api/parameters/#broadcast
