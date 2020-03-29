---
title: Azure Service Fabric CLI- sfctl mesh-servicereplica
description: Meer informatie over sfctl, de opdrachtregelinterface van Azure Service Fabric. Bevat een lijst met opdrachten voor het verkrijgen van replicagegevens voor een toepassingsbronnen.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: ac604ec0a854964746ef34627b054e853662265f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905954"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
Download replicadetails en lijstreplica's van een bepaalde service in een toepassingsbron.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| list | Hiermee worden alle replica's van een service weergegeven. |
| weergeven | Krijgt de gegeven replica van de service van een toepassing. |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl mesh service-replica lijst
Hiermee worden alle replica's van een service weergegeven.

Krijgt de informatie over alle replica's van een service. De informatie bevat de beschrijving en andere eigenschappen van de servicereplica.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --app-naam --applicatie-naam [Vereist] | De naam van de toepassing. |
| --servicenaam [Vereist] | De naam van de service. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl mesh service-replica show
Krijgt de gegeven replica van de service van een toepassing.

Hiermee krijgt u de informatie over de servicereplica met de voornaam. De informatie bevat de beschrijving en andere eigenschappen van de servicereplica.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --app-naam --applicatie-naam [Vereist] | De naam van de toepassing. |
| --naam -n [Vereist] | De naam van de servicereplica. |
| --servicenaam [Vereist] | De naam van de service. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |


## <a name="next-steps"></a>Volgende stappen
- Stel de SERVICE Fabric CLI [in.](service-fabric-cli.md)
- Meer informatie over het gebruik van de CLI van de ServiceFabric met behulp van de [voorbeeldscripts](/azure/service-fabric/scripts/sfctl-upgrade-application).