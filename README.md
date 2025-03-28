# gtm


## FAST Template

```
title: 31. Common Tenant with Data Center and Server
description: Common Tenant with Data Center and Server for GSLB
parameters:
  dataCenter: DC100-Verona
  device_address: 100.8.239.233
  virtual_address: 100.8.239.230
  name_of_VS: DC100-HSW-PRD_VS
  serverName: DC100-Verona_LTM

definitions:
  dataCenter: 
    title: Enter your Data Center Name 
    type: string
  serverName:
    title: Enter servername 
    type: string
  device_address:
    title: Enter BIG_IP Device Address
    type: string
  virtual_address:
    title: Enter Virtual Server 
    type: string
  name_of_VS:
    title: Enter Name of Virtual Server
    type: string

template: | 
  {
    "class": "ADC",
    "schemaVersion": "3.6.0",
    "id": "GSLB_Sample",
    "Common": {
        "class": "Tenant",
        "Shared": {
            "class": "Application",
            "template": "shared",
            "{{dataCenter}}": {
                "class": "GSLB_Data_Center",
                "contact": "Epic",
                "location": "Verona100"
            },
            "{{serverName}}": {
                "class": "GSLB_Server",
                "dataCenter": {
                    "use": "{{dataCenter}}"
                },
                "devices": [
                    {
                        "address": "{{device_address}}"
                    }
                ],
                "virtualServers": [
                    {
                        "address": "{{virtual_address}}",
                        "port": 443,
                        "name": "{{name_of_VS}}"
                    }
                ]
            }
        }
    }
  }
```
## Common Tenant with Data Center and Server 
```
{
    "class": "ADC",
    "schemaVersion": "3.6.0",
    "id": "GSLB_Sample",
    "Common": {
        "class": "Tenant",
        "Shared": {
            "class": "Application",
            "template": "shared",
            "DC100-Verona": {
                "class": "GSLB_Data_Center",
                "contact": "Epic",
                "location": "Verona100"
            },
            "DC100-Verona_LTM": {
                "class": "GSLB_Server",
                "dataCenter": {
                    "use": "DC100-Verona"
                },
                "devices": [
                    {
                        "address": "100.8.239.233"
                    }
                ],
                "virtualServers": [
                    {
                        "address": "100.8.239.230",
                        "port": 443,
                        "name": "DC100-HSW-PRD_VS"
                    }
                ]
            }
        }
    }
}
```
## GSLB Domain with Full Path Reference

```
{
    "class": "ADC",
    "schemaVersion": "3.6.0",
    "id": "GSLB_Sample",
    "Epic_GSLB": {
        "class": "Tenant",
        "Application": {
            "class": "Application",
            "HSW-PRD_GSLB100": {
               "class": "GSLB_Pool",
                "enabled": true,
                "lbModePreferred": "topology",
                "lbModeAlternate": "topology",
                "manualResumeEnabled": true,
                "verifyMemberEnabled": true,
                "qosHitRatio": 10,
                "qosHops": 11,
                "qosKbps": 8,
                "qosLinkCapacity": 35,
                "qosPacketRate": 5,
                "qosRoundTripTime": 75,
                "qosTopology": 3,
                "qosVirtualServerCapacity": 2,
                "qosVirtualServerScore": 1,
                "members": [
                    {
                        "ratio": 1,
                        "server": {
                            "use": "/Common/Shared/DC100-Verona_LTM"
                        },
                        "virtualServer": "DC100-HSW-PRD_VS"
                    }
                ],
                "maxAnswersReturned": 1,
                "monitors": [
                    {
                        "bigip": "/Common/http"
                    }
                ],
                "resourceRecordType": "A"
            },
            "HSW-PRD-GSLB100.pcts.epic.com": {
                "class": "GSLB_Domain",
                "domainName": "HSW-PRD-GSLB100.pcts.epic.com",
                "resourceRecordType": "A",
                "poolLbMode": "topology",
                "pools": [
                    { "use": "HSW-PRD_GSLB100" }
                ]
            }
        }
    }
}
```

