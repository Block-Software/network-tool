---
layout: page
title: Database structure
permalink: /db_structure/
---

## Databases

### Events

#### Categories

_id_: uuid

_name_: string

_short_code_: string

_description_: string

_assigned_applications_: array of objects

```JSON
[
  {
    "executable": "string",
    "type": "string"
  }
]
```

_metrics_: indexed objects

```JSON
{
  "listener": "string",
  "callback": "string",
  "meta": [ "array of strings" ]
}
```

#### Entries

_id_: uuid

_date_: datetime

_device_: uuid reference (Accounts:Devices)

_category_: uuid reference (Events:Entries)

_event_: uuid reference (Events:Entries:metrics)

_data_: object

```JSON
{
  "indexed": [ "additional", "data" ]
}
```

### Accounts

#### Users

_id_: uuid

_active_directory_id_: ad reference

_email_: string

_mobile_: string

_preference_: enum: `email | mobile`

_role_: uuid reference (Accounts:Roles)

_manager_: uuid reference (Accounts:Users)

#### Devices

_id_: uuid

_mac_: string

_name_: string

_user_: uuid reference (Accounts:Users)

_location_: uuid reference (Accounts:Locations)

#### Roles

_id_: uuid

_name_: string

_parent_: uuid reference (Accounts:Roles)

_permissions_: array of objects

```JSON
[
  {
    "access": "string",
    "allow": true | false
  }
]
```

#### Locations

_id_: uuid

_name_: string

_network_parameters_: object

```JSON
{
  "meta": "data"
}
```

### Preferences

#### Sync Settings

_id_: uuid

_user_: uuid reference (Accounts:Users)

_meta_: object

```JSON
{
  "property_name": "value"
}
```

#### Dashboard Views

_id_: uuid

_user_: uuid reference (Accounts:Users)

_widgets_: indexed object of uuid references (Preferences:Widgets)

```JSON
{
  "id": "uuid",
  "position": "x,y",
  "size": "w,h"
}
```

#### Widgets

_id_: uuid

_name_: string

_query_: SQL string

_view_: enum: `chart | pie | table | tag-cloud | sum`