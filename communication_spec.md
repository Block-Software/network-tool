# Connectivity monitoring and self-healing
## PUT request for logging events:
### PUT: `/api/event`
### Payload: `JSON`
```JSON
{
  "cpu": "cpu usage percentage",
  "ram": "ram usage percentage",
  "latency": "network latency in milliseconds",
  "event": "event name",
  "application": "application triggering event",
  "trigger_time": "timestamp for event",
  "meta": { a JSON object of unique data for the category }
}
```
### Response: `JSON` (this response is added to the callback trigger)
```JSON {
  "status": "good | warning | error",
  "message": "message relating to the status",
  "average": [ "an array of average responses in the category" ],
  "event_id": "unique event ID for follow up tracing"
}
```
## GET request for retrieving an application category:
### GET: `/api/category?application=Outlook`
### Response: `JSON`
```JSON
{
  "category": "email_client | productivity_app | creative_app | database_app | cloud_app",
  "valid_metrics": {
    "open": {
      "meta": [ "fields related to the category" ],
      "callback": "action name to trigger on success – and pass the event response"
    },
    "close": {
      "meta": [ "fields related to the category" ],
      "callback": "action name to trigger on success – and pass the event response"
    },
    "hang":  {
        "meta": [ "fields related to the category" ],
        "callback": "action name to trigger on success – and pass the event response",
        "trigger_criteria": [ "unresponsive": "> 10000ms"]
    },
    "sync": {
      "listeners": [
          {
            "url": "URL to listen from application",
            "method": "POST | GET"
          }
        ]
    }
}
```
## Application categories:

Application categories are used to allow different organisations to provide their own specific application and for it to fall within our dashboard and reporting tools correctly.

`email_client`: Eg Outlook, Mac Mail, Thunderbird, Gmail

`productivity_app`: Eg MS Word, MS Excel

`creative_app`: Eg Photoshop

`database_app`: Eg PAS, MS Access

`cloud_app`: Eg Dropbox, Sage

## Event codes:
`open_${app_category}`: When opening an application in a specific category

`hang_${app_category}`: Application in specific category is unresponsive for longer than 10000 miliseconds

`close_${app_category}`: When closing an application in a specific category

`network_sync`: An application makes an HTTP(S) request
