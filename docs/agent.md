---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: page
title: Performance and monitoring agent
---

# Block Device performance and monitoring agent

This document aims to uncover the journeys for the various user types and the reporting mechanisms for the various detection inputs.

## User Stories

### App configuration for a network

1. A Network administrator must log into the Dashboard (eg: dashboard.block.io) and authenticate against Active Directory
1. Create connection between dashboard and the local network / cloud Active Directory
1. Generate application groups and target specific AD resources (machines, groups, users)
  * An application group consists of multiple [applications](#applications)
  * Users targeted by an application group will have the applications monitored from their desktop agent
  * Roaming profiles will allow the agent to be installed to multiple machines, but the configuration for the agent is loaded based on the logged in user

### Background app monitoring for a network user

1. User logs into their workstation (Windows PC) and the application runs at startup minimised to the system tray
1. The first time a user accesses the application, it appears with a QR code to ask them if they would like to enable OOB communication. This presents them with a website to ask for their mobile number or what their preferred method of communication will be
1. User begins using their device and the app monitors all network activity and transmits it over a websocket connection to the backend service
1. Network usage incuding response times, application coverage and activity is recorded, this is processed by the backend server and filters used to detect if there is an issue

### Network analysis and recommendations

The backend receives network information from each agent on the network to highlight network traffic and performance information. This is then surfaced to administrators on the Dashboard to ask them to configure further information.

_Configuring a network application_

A network application will be configured with the following details:

1. Application name
1. Process name (as identified by Windows)
1. Target IP address(es)
1. Additional service types and connections
1. Check frequency - to ensure all agents aren't risking DDoS'ing a service
1. Metrics and tests - including the acceptable thresholds for success

### Detecting an issue with a specific desktop application on a users PC

When a desktop application is started, a JSON payload is submitted via the websocket to the backend. This payload will look like:

```JSON
{
  "user": "user_active_directory_id",
  "process_name": "name_of_running_process",
  "timestamp": "datetime"
}
```

The websocket will submit data back with all the metrics that are used to measure the application (if it is found within the configured network applications database). This payload will look like:

```JSON
{
  "metrics": [
    {
      "input_type": "ping",
      "target_ip": "0.0.0.0/1",
      "frequency": "* * * */5"
    },
    {
      "input_type": "curl",
      "target_url": "https://example.com/healthcheck",
      "method": "post",
      "content_type": "application/json",
      "payload": {
        "example": "payload"
      },
      "frequency": "* * 1 *"
    }
  ]
}
```
Frequency is a schedule in the format of crontab scheduling for easy parsing and readability.

### Detecting an issue with a web application

Web applications use deployed nodes to perform scheduled metric tests. These can be deployed into the agents themselves, or as separate hardware running schedules. They connect via websocket to the backend and receive payloads for tests. The payload is distributed by the backend and processed by the subscribed devices. The payload for a test will look like:

```JSON
{
  "application": {
    "application_id": "uuid",
    "tests": [
      {
        "input_type": "ping",
        "target_ip": "0.0.0.0/1",
        "frequency": "* * * */5"
      },
      {
        "input_type": "curl",
        "target_url": "https://example.com/healthcheck",
        "method": "post",
        "content_type": "application/json",
        "payload": {
          "example": "payload"
        },
        "frequency": "* * 1 *"
      }
    ]
  }
}
```

The results of the tests will be submitted instantly to the backend with the attached `application_id` as the channel. A new channel will be setup for each application, this way all communication for a specific application is isolated to a channel for easy monitoring and isolation.

### Determining affected users of an issue

The results of the tests both scheduled and by devices are processed by the backend. The backend will then check the acceptable criteria for an issue within the tests. If these trigger an issue for a configured network application, the target users will be notified. If more than 1 AD group is associated to an application, where geographic location is known for the AD group, the affected users will only be notified if they are in the same geographic location as the source test.

Payload for a notification to an affected user will look like:

```JSON

```

### Reporting / notifying users of an issue

Notifying a user that has been affected will be based on their preferences. All users will receive the websocket notification but their preferences will set how to show. The preferences will be:
* Toast notification
* SMS notification
* Email
* Hidden

This will be set on a per severity and per application basis, or for their entire user account.

## Network Applications

There are 2 types of network applications. Desktop installed and Web applications.

### Desktop installed



### Web applications

