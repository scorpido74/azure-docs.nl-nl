---
title: Azure Service Fabric CLI- sfctl mesh secretvalue
description: Meer informatie over sfctl, de opdrachtregelinterface van Azure Service Fabric. Bevat een lijst met opdrachten voor het verkrijgen en verwijderen van servicefabric mesh-bronnen met geheime waarde.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: b2fe3a7995cdd88a8f63d0d865e72e9f161cf9d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905958"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Netwerkgeheime bronnen verwijderen en verwijderen.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| delete | Hiermee verwijdert u de opgegeven waarde van de benoemde geheime bron. |
| list | Vermeld namen van alle waarden van de opgegeven geheime bron. |
| weergeven | Hiermee geeft u de opgegeven waarde van de geheime resource weer. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl mesh secretvalue delete sfctl mesh secretvalue delete sfctl mesh secretvalue
Hiermee verwijdert u de opgegeven waarde van de benoemde geheime bron.

Hiermee verwijdert u de geheime waardebron die door de naam is geïdentificeerd. De naam van de resource is meestal de versie die aan die waarde is gekoppeld. Verwijdering mislukt als de opgegeven waarde in gebruik is.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --secret-name -n [Vereist] | De naam van de geheime bron. |
| --versie -v [Vereist] | De naam van de geheime versie. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-mesh-secretvalue-list"></a>sfctl mesh secretvalue list
Vermeld namen van alle waarden van de opgegeven geheime bron.

Krijgt informatie over alle geheime waardebronnen van de opgegeven geheime bron. De informatie bevat de namen van de geheime waardebronnen, maar niet de werkelijke waarden.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --secret-name -n [Vereist] | De naam van de geheime bron. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl mesh secretvalue show
Hiermee geeft u de opgegeven waarde van de geheime resource weer.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --secret-name -n [Vereist] | De naam van de geheime bron. |
| --versie -v [Vereist] | De naam van de geheime versie. |
| --show-waarde | De werkelijke waarde van de geheime versie weergeven. |

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