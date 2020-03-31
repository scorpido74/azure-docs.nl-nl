---
title: Azure Service Fabric CLI- sfctl chaos schema
description: Meer informatie over sfctl, de opdrachtregelinterface van Azure Service Fabric. Bevat een lijst met opdrachten voor het plannen van chaos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 29b365c48e405830e238945f1d94156f477c15b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906188"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Pak het chaosschema.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| ophalen | Krijg het Chaos Schema dat bepaalt wanneer en hoe chaos moet worden uitgevoerd. |
| set | Stel het schema in dat door Chaos wordt gebruikt. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl chaos schema te krijgen
Krijg het Chaos Schema dat bepaalt wanneer en hoe chaos moet worden uitgevoerd.

Hiermee wordt de versie van het Chaos-schema in gebruik genomen en het chaosschema dat bepaalt wanneer en hoe chaos moet worden uitgevoerd.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl chaos schema ingesteld
Stel het schema in dat door Chaos wordt gebruikt.

Chaos plant automatisch runs op basis van het Chaos Schema. Het chaosschema wordt bijgewerkt als de meegeleverde versie overeenkomt met de versie op de server. Bij het bijwerken van het chaosschema wordt de versie op de server verhoogd met 1. De versie op de server zal terug naar 0 na het bereiken van een groot aantal. Als Chaos wordt uitgevoerd wanneer deze oproep wordt gedaan, zal de oproep mislukken.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --chaos-parameters-woordenboek | Json gecodeerde lijst die een toewijzing van string namen naar ChaosParameters worden gebruikt door Jobs. |
| --expiry-date-utc | De datum en tijd voor wanneer u moet stoppen met het gebruik van de planning om Chaos te plannen.  Standaard\: 9999-12-31T23\:\:59 59.999Z. |
| --vacatures | JSON gecodeerde lijst van ChaosScheduleJobs die aangeeft wanneer chaos uit te voeren en met welke parameters chaos te draaien met. |
| --start-date-utc | De datum en tijd voor wanneer u de planning moet gebruiken om Chaos te plannen.  Standaard\: 1601-01-01T00\:\:00 00.000Z. |
| --time-out -t | Standaard\: 60. |
| --versie | Het versienummer van de planning. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

### <a name="examples"></a>Voorbeelden

Met de volgende opdracht wordt een schema ingesteld (ervan uitgaande dat het huidige schema versie 0 heeft) dat begint op 2016-01-01 en verloopt op 2038-01-01 waarop Chaos 24 uur per dag, 7 dagen per week wordt uitgevoerd.
Chaos zal worden gepland op het cluster voor die tijd.
```
sfctl chaos schedule set --version 0 --start-date-utc "2016-01-01T00:00:00.000Z" --expiry-date-utc "2038-01-01T00:00:00.000Z"
    --chaos-parameters-dictionary
    [
    {
        "Key":"adhoc",
        "Value":{
            "MaxConcurrentFaults":3,
            "EnableMoveReplicaFaults":true,
            "ChaosTargetFilter":{
                "NodeTypeInclusionList":[
                "N0010Ref",
                "N0020Ref",
                "N0030Ref",
                "N0040Ref",
                "N0050Ref"
                ]
            },
            "MaxClusterStabilizationTimeoutInSeconds":60,
            "WaitTimeBetweenIterationsInSeconds":15,
            "WaitTimeBetweenFaultsInSeconds":30,
            "TimeToRunInSeconds":"600",
            "Context":{
                "Map":{
                "test":"value"
                }
            },
            "ClusterHealthPolicy":{
                "MaxPercentUnhealthyNodes":0,
                "ConsiderWarningAsError":true,
                "MaxPercentUnhealthyApplications":0
            }
        }
    }
    ]
    --jobs
    [
    {
        "ChaosParameters":"adhoc",
        "Days":{
            "Sunday":true,
            "Monday":true,
            "Tuesday":true,
            "Wednesday":true,
            "Thursday":true,
            "Friday":true,
            "Saturday":true
        },
        "Times":[
            {
                "StartTime":{
                "Hour":0,
                "Minute":0
                },
                "EndTime":{
                "Hour":23,
                "Minute":59
                }
            }
        ]
    }
    ]
```



## <a name="next-steps"></a>Volgende stappen
- Stel de SERVICE Fabric CLI [in.](service-fabric-cli.md)
- Meer informatie over het gebruik van de CLI van de ServiceFabric met behulp van de [voorbeeldscripts](/azure/service-fabric/scripts/sfctl-upgrade-application).
