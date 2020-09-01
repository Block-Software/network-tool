---
layout: page
title: Example application - EMIS
permalink: /example_data/emis
---

## Application probe setup

To initialise the NHS Spine EMIS Application, the following data is added to the metrics for this application

```JSON
[
  {
    "category": "9ab4e9ee-1c80-4389-9bf9-47bf7f1f3ea2",
    "executable": "nhsnet-emis.exe",
    "type": "spine",
    "metrics": [
      {
        "listener": "interval",
        "callback": "pingComplete",
        "parameters": {
          "input_type": "ping",
          "target_ip": "0.0.0.0/1",
          "frequency": "*/5 * * * *",
          "threshold": "10ms"
        },
      },
      {
        "listener": "interval",
        "callback": "portCheckComplete",
        "parameters": {
          "input_type": "open_port_checker",
          "target_ip": "0.0.0.0/1",
          "target_ports": [33956, "33962-33972"],
          "frequency": "0 */5 * * *"
        }
      },
      {
        "listener": "interval",
        "callback": "healthcheckComplete",
        "parameters": {
          "input_type": "curl",
          "target_url": "tcp://sds.spine.emis.thirdparty.nhs.uk:33956/Emis.SDS.Common/ISDSPatchHubService?agentid=1",
          "method": "post",
          "content_type": "application/octet-stream",
          "payload": {
            "MachineGuid": "f2a33172-ee72-4ed5-89cb-99dcef9a8622",
            "Environment": "EN_GP13 Cdb 10155",
            "IsLocalSpoke": false,
            "IsExistingCluster|Emis.SDS.Common|SDSPatchHubService": "Emis.SDS.Common",
            "Version": "6.3.13.0",
            "Culture": "neutral",
            "PublicKeyToken": null
          },
          "expect_response": {
            "body": "GEmis.SDS.Common, Version=6.3.13.0, Culture=neutral, PublicKeyToken=null......Emis.SDS.Common.UpdateInfo.....<TargetVersions>k__BackingField$<DeploymentReference>k__BackingField!<ClusterReference>k__BackingField.<MachineType>k__BackingField!<ProductTypesHash>k__BackingField.<ProductSetId>k__BackingField........System.Collections.Generic.List`1[[Emis.SDS.Common.ProductData, Emis.SDS.Common, Version=6.3.13.0, Culture=neutral, PublicKeyToken=null]].Emis.SDS.Common.SDSMachineType......... ..........EN_GP13......10155......Emis.SDS.Common.SDSMachineType.....value__........... ....8+IVNddSKh+eV+gqySZTYKGExVg=.........4System.Runtime.Remoting.Messaging.LogicalCallContext...........System.Collections.Generic.List`1[[Emis.SDS.Common.ProductData, Emis.SDS.Common, Version=6.3.13.0, Culture=neutral, PublicKeyToken=null]]....._items._size._version....Emis.SDS.Common.ProductData[]"
          },
          "frequency": "*/5 * * * *"
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
