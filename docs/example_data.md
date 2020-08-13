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

1. Enter network location
1. List of expected users (a pre-determined list of common users)
1. Device group (which department this device belongs to)

POST `/api/device`

Payload:

```JSON
{
  "name": "computer name",
  "mac": "mac address",
  "location": "uuid_reference"
}
```

### Link devices to account

PUT `/api/user/devices`

### Associate applications

PUT `/api/user/applications`

Payload:
