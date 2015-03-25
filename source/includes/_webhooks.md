# Webhooks

The Todoist Webhooks API allows applications to receive realtime notification (in form of HTTP POST payload) on the subscribed user events. 

Notice that once you have webhook setup, you will start receiving webhook events from __all your app users__.


## Webhooks Configuration

Before you can start receiving webhook event notifications, you must have your webhook configured at the App Management Console. 


### Callback URL Verification

To receive webhook event notifications, you must supply a "callback URL" to recieve these notification. It could be configured at the App Management Console.

> An example of a callback_url verification request:
```
HTTP GET https://your_callback_url?challenge=GJ12X4D1IGJXB
```

After you enter the webhook callback URL and save the setting, we will send a verification request to the callback URL to verify that the endpoint belongs to you. It would be a HTTP GET request with a `challenge` query paramter that contains a random hex string. 

Upon receiving such a request, your callback URL endpoint must return a HTTP 200 response with the same `challenge` text in the response body to complete the verification process. Once the verification is completed, your app's "Webhook Status" field should become "Active", and you will start receiving webhook event notification from all your app users.



### Events

Here is a list of events that you could subscribe to, and you could configured this at the App Management Console.


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

Each webhook event is structured as a JSON object. The event JSON object follow the general structure:

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

When your subscribed webhook events occourred, we will deliver event notification to your configured "Webhook Callback URL". 
It will be a HTTP POST request, with `Content-Type: application/json`. Notice that it will be a __JSON array__ because we might 
deliver multiple event notification (could be from different users) at once.



### Payload Verification 

To verify each webhook request is sent by us, we include a __X-Todoist-Hmac-SHA256__ header which contains a SHA256 Hmac 
that is generated using `client_secret` as key and the whole request payload as message. The resulting Hmac is encoded as 
a base64 string.
