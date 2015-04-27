# Webhooks

The Todoist Webhooks API allows applications to receive realtime notification (in form of HTTP POST payload) on the subscribed user events. 

Notice that once you have webhook setup, you will start receiving webhook events from __all your app users__ immediately.


## Webhooks Configuration

Before you can start receiving webhook event notifications, you must first have your webhook configured at the App Management Console. 


### Events

Here is a list of events that you could subscribe to, and they are configured at the App Management Console.


Event Name | Description
-------- | -----------
item:added | 
item:updated | 
item:deleted | 
item:completed | 
item:uncompleted | 
note:added | 
note:updated | 
note:deleted | 
project:added | 
project:updated | 
project:deleted | 
project:archived | 
project:unarchived | 
label:added | 
label:deleted | 
label:updated | 
filter:added | 
filter:deleted | 
filter:updated | 



## Event Structure / Delivery


### Event JSON Object

Each webhook event is structured as a JSON object. The event JSON object follows this general structure:

`{"event_name": "...", "user_id"=..., "event_data": {...}}`

The structure of "event_data" object varies depending on the type of event it is. For instance, if it is a "item:added" event notification, 
The "event_data" will represents the newly added item.




> Example Delivery

```
POST /payload HTTP/1.1

Host: your_callback_url_host
Content-Type: application/json
X-Todoist-Hmac-SHA256: UEEq9si3Vf9yRSrLthbpazbb69kP9+CZQ7fXmVyjhPs=

[
  {
    "event_name": "item:added",
    "user_id": 1234,
    "event_data": {
      "due_date": null,
      "day_order": -1,
      "assigned_by_uid": 1855589,
      "is_archived": 0,
      "labels": [],
      "sync_id": null,
      "in_history": 0,
      "has_notifications": 0,
      "indent": 1,
      "checked": 0,
      "date_added": "Fri 26 Sep 2014 08:25:05 +0000",
      "id": 33511505,
      "content": "Task1",
      "user_id": 1855589,
      "due_date_utc": null,
      "children": null,
      "priority": 1,
      "item_order": 1,
      "is_deleted": 0,
      "responsible_uid": null,
      "project_id": 128501470,
      "collapsed": 0,
      "date_string": ""
    }
  },
  {
    "event_name": "item:updated",
    "user_id": 528,
    "event_data": {
      "due_date": null,
      "day_order": -1,
      "assigned_by_uid": 1855589,
      "is_archived": 0,
      "labels": [],
      "sync_id": null,
      "in_history": 0,
      "has_notifications": 0,
      "indent": 1,
      "checked": 0,
      "date_added": "Fri 26 Sep 2014 08:25:05 +0000",
      "id": 43311505,
      "content": "Task1",
      "user_id": 1855589,
      "due_date_utc": null,
      "children": null,
      "priority": 1,
      "item_order": 1,
      "is_deleted": 0,
      "responsible_uid": null,
      "project_id": 223501470,
      "collapsed": 0,
      "date_string": ""
    }
  },
  ...
}
```


### Event Delivery

When your subscribed webhook events occourred, we would deliver an event notification to your configured webhook callback url via a HTTP POST request. Notice that the payload would be a __JSON array__ because multiple event notifications could be delivered in a single notification request.


### Payload Verification 

To verify each webhook request was indeed sent by Todoist, we would include a __X-Todoist-Hmac-SHA256__ header which was a SHA256 Hmac 
generated using your `client_secret` as the encryption key and the whole request payload as the message to be encrypted. The resulting Hmac would be encoded in 
a base64 string.


### Failed Delivery
When a event notification failed to be delivered to your webhook callback URL endpoint (i.e. due to server error, network failure, incorrect response...etc), 
it would be redelivered after 30 mins (1hr and 1.5hr for the second and the third retry respectively), and each notification would be redelivered for at most three times.

__Your callback endpoint must respond with a HTTP 200 when receiving a event notification request.__ Response other than HTTP 200 would be considered as failed delivery, and the notification would be redelivered again.
