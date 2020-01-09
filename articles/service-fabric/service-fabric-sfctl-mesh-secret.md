---
title: Azure Service Fabric CLI-sfctl mesh-geheim
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten voor het ophalen en verwijderen van Service Fabric net-netwerk bronnen.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: f43f4fba0d7550ccb09e54f178a78d01f01cfc9d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645341"
---
# <a name="sfctl-mesh-secret"></a>sfctl mesh secret
Geheim bronnen van het net ophalen en verwijderen.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| delete | Hiermee verwijdert u de geheime resource. |
| list | Een lijst met alle geheime bronnen. |
| weergeven | Hiermee wordt de geheime resource met de opgegeven naam opgehaald. |

## <a name="sfctl-mesh-secret-delete"></a>sfctl net-geheim verwijderen
Hiermee verwijdert u de geheime resource.

Hiermee verwijdert u de opgegeven geheime resource en alle bijbehorende benoemde waarden.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --naam-n [vereist] | De naam van de geheime resource. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-mesh-secret-list"></a>sfctl mesh-lijst met geheimen
Een lijst met alle geheime bronnen.

Hiermee wordt de informatie opgehaald over alle geheime resources in een bepaalde resource groep. De informatie bevat de beschrijving en andere eigenschappen van het geheim.

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-mesh-secret-show"></a>sfctl net-geheim tonen
Hiermee wordt de geheime resource met de opgegeven naam opgehaald.

Hiermee wordt de informatie opgehaald over de geheime resource met de opgegeven naam. De informatie bevat de beschrijving en andere eigenschappen van het geheim.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --naam-n [vereist] | De naam van de geheime resource. |

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