---
title: Azure Service Fabric CLI-sfctl mesh code-pakket-log
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten voor het verkrijgen van Logboeken voor een opgegeven code pakket.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 78edc9bb36b711f72300942bc9900b0fde7c51d2
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646140"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
De logboeken ophalen voor de container van het opgegeven code pakket voor de opgegeven service replica.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| Toevoegen | Hiermee worden de logboeken van de container opgehaald. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl netcode-pakket-logboek ophalen
Hiermee worden de logboeken van de container opgehaald.

Hiermee haalt u de logboeken voor de container van het opgegeven code pakket van de service replica.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --app-naam--Application name [required] | De naam van de toepassing. |
| --code-pakket naam [vereist] | De naam van het code pakket van de service. |
| --replica-naam [vereist] | Service Fabric replica naam. |
| --Service-naam [vereist] | De naam van de service. |
| --staart | Aantal regels dat moet worden weer gegeven aan het einde van de logboeken. De standaard waarde is 100. ' all ' om de volledige logboeken weer te geven. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |


## <a name="next-steps"></a>Volgende stappen
- [Stel](service-fabric-cli.md) de service Fabric cli in.
- Meer informatie over het gebruik van de Service Fabric CLI met behulp van de [voorbeeld scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).