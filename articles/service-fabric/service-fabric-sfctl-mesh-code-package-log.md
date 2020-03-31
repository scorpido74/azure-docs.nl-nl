---
title: Azure Service Fabric CLI- sfctl mesh code-package-log
description: Meer informatie over sfctl, de opdrachtregelinterface van Azure Service Fabric. Bevat een lijst met opdrachten voor het verkrijgen van logboeken voor een opgegeven codepakket.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f8665e75e4c921a3305c9965601e2ee0825c8995
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906047"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Download de logboeken voor de container van het opgegeven codepakket voor de opgegeven servicereplica.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| ophalen | Haalt de logboeken uit de container. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-log get
Haalt de logboeken uit de container.

Hier worden de logboeken voor de container van het opgegeven codepakket van de servicereplica opgevraagd.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --app-naam --applicatie-naam [Vereist] | De naam van de toepassing. |
| --code-package-name [Vereist] | De naam van het codepakket van de service. |
| -replica-naam [Vereist] | Service Fabric replica naam. |
| --servicenaam [Vereist] | De naam van de service. |
| --staart | Aantal regels dat vanaf het einde van de logboeken moet worden weergegeven. Standaard is 100. 'alles' om de volledige logs weer te geven. |

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