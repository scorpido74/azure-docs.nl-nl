---
title: Azure Service Fabric CLI- sfctl mesh-service
description: Meer informatie over sfctl, de opdrachtregelinterface van Azure Service Fabric. Bevat een lijst met opdrachten voor het verkrijgen van servicegegevens voor een toepassingsbron.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 2b9f16062b02d7a1a3403d396643a7989b8976b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905937"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
Ontvang servicegegevens en lijstservices van een toepassingsbron.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| list | Hier worden alle servicebronnen weergegeven. |
| weergeven | Hiermee krijgt u de servicebron met de voornaam. |

## <a name="sfctl-mesh-service-list"></a>sfctl mesh-servicelijst
Hier worden alle servicebronnen weergegeven.

Krijgt de informatie over alle services van een toepassingsbron. De informatie bevat de beschrijving en andere eigenschappen van de Service.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --app-naam --applicatie-naam [Vereist] | De naam van de toepassing. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-mesh-service-show"></a>sfctl mesh service show
Hiermee krijgt u de servicebron met de voornaam.

Hiermee krijgt u de informatie over de Service-bron met de voornaam. De informatie bevat de beschrijving en andere eigenschappen van de Service.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --app-naam --applicatie-naam [Vereist] | De naam van de toepassing. |
| --naam -n [Vereist] | De naam van de service. |

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