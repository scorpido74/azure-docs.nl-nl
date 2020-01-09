---
title: Azure Service Fabric CLI-sfctl-mesh service-replica
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten voor het ophalen van replica Details voor een toepassings bronnen.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 219a691dd1b74ec2214c156018e6e3f62366f523
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645324"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
Replica Details ophalen en replica's van een bepaalde service in een toepassings bron weer geven.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| list | Een lijst met alle replica's van een service. |
| weergeven | Hiermee wordt de opgegeven replica van de service van een toepassing opgehaald. |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl-mesh service-replica lijst
Een lijst met alle replica's van een service.

Hiermee wordt de informatie opgehaald over alle replica's van een service. De informatie bevat de beschrijving en andere eigenschappen van de service replica.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --app-naam--Application name [required] | De naam van de toepassing. |
| --Service-naam [vereist] | De naam van de service. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl-mesh service-replica show
Hiermee wordt de opgegeven replica van de service van een toepassing opgehaald.

Hiermee wordt de informatie opgehaald over de service replica met de opgegeven naam. De informatie bevat de beschrijving en andere eigenschappen van de service replica.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --app-naam--Application name [required] | De naam van de toepassing. |
| --naam-n [vereist] | De naam van de service replica. |
| --Service-naam [vereist] | De naam van de service. |

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