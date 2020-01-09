---
title: Azure Service Fabric CLI-sfctl rpm
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten voor de reparatie Manager-service.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 674970276046034d13801db7c1bb4ab5175385fb
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639085"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Query's uitvoeren en opdrachten verzenden naar de service reparatie beheer.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| goed keuren forceren | Hiermee wordt de goed keuring van de opgegeven herstel taak geforceerd. |
| delete | Hiermee verwijdert u een voltooide herstel taak. |
| list | Hiermee wordt een lijst met herstel taken opgehaald die overeenkomen met de opgegeven filters. |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm goed keuren-forceren
Hiermee wordt de goed keuring van de opgegeven herstel taak geforceerd.

Deze API ondersteunt het Service Fabric-platform. het is niet bedoeld om rechtstreeks vanuit uw code te worden gebruikt.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --taak-id [vereist] | De ID van de herstel taak. |
| --versie | Het huidige versie nummer van de herstel taak. Als dit niet het geval is, wordt de aanvraag alleen uitgevoerd als deze waarde overeenkomt met de werkelijke huidige versie van de herstel taak. Als de waarde nul is, wordt er geen versie controle uitgevoerd. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-rpm-delete"></a>sfctl rpm verwijderen
Hiermee verwijdert u een voltooide herstel taak.

Deze API ondersteunt het Service Fabric-platform. het is niet bedoeld om rechtstreeks vanuit uw code te worden gebruikt.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --taak-id [vereist] | De ID van de voltooide herstel taak die moet worden verwijderd. |
| --versie | Het huidige versie nummer van de herstel taak. Als dit niet het geval is, wordt de aanvraag alleen uitgevoerd als deze waarde overeenkomt met de werkelijke huidige versie van de herstel taak. Als de waarde nul is, wordt er geen versie controle uitgevoerd. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-rpm-list"></a>sfctl rpm-lijst
Hiermee wordt een lijst met herstel taken opgehaald die overeenkomen met de opgegeven filters.

Deze API ondersteunt het Service Fabric-platform. het is niet bedoeld om rechtstreeks vanuit uw code te worden gebruikt.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --uitvoerbaar filter | De naam van de reparatie-uitvoerder waarvan de gedeclareerde taken moeten worden opgenomen in de lijst. |
| --status-filter | Een bitsgewijze-of van de volgende waarden, opgeven welke taak statussen moeten worden opgenomen in de lijst met resultaten. <br> 1-gemaakt <br>2-geclaimd  <br>4: voorbereiden  <br>8-goedgekeurd  <br>16-uitvoeren  <br>32-herstellen  <br>64-voltooid |
| --task-id-filter | Het voor voegsel van de reparatie taak-ID moet worden gevonden. |

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