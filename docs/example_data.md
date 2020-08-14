---
layout: page
title: Example data
permalink: /example_data/
---

## User preferences creating a dashboard of a teams usage

Below are the steps to initiate a device onto the network. Associate a user to a device, report entries to the platform and allow the user to view the data in their location.

### Create a member

A network administrator creates users based on their location and who their managers are. Each manager has access to the dashboard to view the metrics and reporting within their location, and their team members.

PUT `/api/user`
Payload:

```JSON
{
  "name": "Foo Bar",
  "email": "test@example.com",
  "phone": "01223217212",
  "role": "b295645d-c869-4d2f-ada6-f65c86b77332",
  "manager": "1098eee4-e57a-440e-b9cb-8d0a1676c9e8"
}
```

### Register devices

When a new device is added to the network, and the agent is installed, it automatically sends information to the central API about itself. If anything can't be automatically gathered from the device, a wizard will popup:

1. Enter network location - From a list of pre-defined locations:
    * South Block
    * Science Labs
1. List of expected users - a pre-determined list of common users:
    * auto-complete search of users
1. Device group - which department this device belongs to:
    * Research & Development
    * Pharmacy

POST `/api/device`

Payload:

```JSON
{
  "name": "WIN10-south-014",
  "mac": "00:0a:95:9d:68:16",
  "location": "8221d426-f6f7-4f0e-9907-b1f1fc4e5f61",
  "users": [
    "5193c475-7d8c-4bf7-83e8-94d6babbcc8e",
    "3466486d-5dcd-49da-b8e0-7dd161ad4efe",
    "8e9756ba-c5c7-4313-a9ba-dfb520a80a39",
    "9d81fa92-3ebb-4641-8c80-2a4addf9abb7",
    "a3315953-2fd6-461c-a7d0-2f03fbf9c092"
  ],
  "group": "a8821ff2-92f1-49eb-a23e-2b35b9fd2800"
}
```

### Link devices to account

When a user logs into a device that hasn't been used by that user before, an automated POST is sent to associate the device to the user.

PUT `/api/user/d055182c-79d2-482c-9fcb-d93db8686d51/devices`

Payload:

```JSON
{
  "device_id": "23054467-7e42-4d56-823e-ce21e43916ba"
}
```

### Associate applications

Once a user has logged in and the device is registered, the agent scrapes the applications it can discover and creates a local cache of the installed applications. These then allow the agent to build a listener for the network activity and expected monitoring for each application. This is a unique piece of functionality as we are in-advance determining what to listen out for, instead of analysing logs after they have occurred.

PUT `/api/user/d055182c-79d2-482c-9fcb-d93db8686d51/applications`

Payload:

```JSON
{
  "detected_applications": [
    {
      "executable": "Outlook.exe",
      "last_accessed": "2020-07-15 16:34:15T00"
    },
    {
      "executable": "Chrome.exe",
      "last_accessed": "2020-08-10 10:15:20T00"
    }
  ],
  "detected_internet_shortcuts": [
    {
      "browser": "Chrome",
      "location": "https://pas.internal.local"
    }
  ]
}
```

Response:

```JSON
[
  {
    "category": "9ab4e9ee-1c80-4389-9bf9-47bf7f1f3ea2",
    "executable": "Outlook.exe",
    "type": "email_client",
    "metrics": [
      {
        "listener": "interval",
        "callback": "pingComplete",
        "parameters": {
          "input_type": "ping",
          "target_ip": "0.0.0.0/1",
          "frequency": "* * * */5"
        },
      },
      {
        "listener": "interval",
        "callback": "healthcheckComplete",
        "parameters": {
          "input_type": "curl",
          "target_url": "https://example.com/healthcheck",
          "method": "post",
          "content_type": "application/json",
          "payload": {
            "example": "payload"
          },
          "frequency": "* * 1 *"
        }
      },
      {
        "listener": "open",
        "callback": "emailOpened",
        "arguments": [ "time" ]
      }
    ]
  }
]
```
### Activity detected

Once the agent has received a list of event listeners from the `/api/user/{id}/applications` endpoint it activates them on the system and runs based on each listeners schedule. This is a unique section of functionality as we are creating tailor made metric analytics for specific platforms. It's a learned solution, so as soon as new applications, or better methods to monitor the availability / runtime of an application or service are known, they are instantly rolled out to all members.

PUT `/api/event`

Payload:
cfa6a752-26d7-418b-bd23-df65e43d0b07
```JSON
{
  "user": "d055182c-79d2-482c-9fcb-d93db8686d51",
  "device": "83827872-2da4-4b3e-a216-a094b1fa2528",
  "cpu": "35%",
  "ram": "10%",
  "latency": "200ms",
  "event": "open",
  "application": "cfa6a752-26d7-418b-bd23-df65e43d0b07",
  "trigger_time": "2020-08-10-02 15:13:14T00"
}
```

### Activity monitoring

As data starts to be added via the events and application reporting, a dashboard of real-time metrics is then available and begins to take a shape. A user has the capability to create a dashboard with targeted locations and their own preferences for which areas of a network they would like to monitor. For example: The manager of the Research Department may have an interest in the Science Lab machines that belong to the research team. The dashboard will create a query using the unique query builder as follows:
`SELECT {device|user|group} WHERE {location|role|user} = {value}`

This will return data for all devices placed on a map with heatmaps of which application is being used within those cohorts of devices or users.

The data can be grouped by the application, by the location or by who the manager is. This way managers are easily able to identify any problems they may be facing within the team and if there is a known issue it will report back any outstanding tickets. Thus reducing the number of unnecessary support calls as the data is immediately shown to them.

This uses websockets to send the data in realtime:

```JSON
{
  "active_users": 10,
  "most_active_application": "Outlook",
  "average_latency": "100ms",
  "number_of_applications_open": 11
}
```

