---
title: Azure Service Fabric CLI- sfctl, eigenschap
description: Meer informatie over sfctl, de opdrachtregelinterface van Azure Service Fabric. Bevat een lijst met opdrachten voor het opslaan en opvragen van eigenschappen.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: a9bd75e0b7f8bfceb50a71ca83b60ff1e7b45508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905827"
---
# <a name="sfctl-property"></a>sfctl property
Opslag- en queryeigenschappen onder namen van servicefabric.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| delete | Hiermee verwijdert u de opgegeven eigenschap Service Fabric. |
| ophalen | Hier krijgt u de opgegeven eigenschap Service Fabric. |
| list | Krijgt informatie over alle eigenschappen van Service Fabric onder een bepaalde naam. |
| plaatsen | Hiermee maakt of werkt u een eigenschap Service Fabric bij. |

## <a name="sfctl-property-delete"></a>sfctl-eigenschap verwijderen
Hiermee verwijdert u de opgegeven eigenschap Service Fabric.

Hiermee verwijdert u de opgegeven eigenschap ServiceFabric onder een bepaalde naam. Er moet een eigenschap worden gemaakt voordat deze kan worden verwijderd.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --name-id [Vereist] | De naam Service Fabric,\:zonder het 'fabric' URI schema. |
| --eigenschap-naam [Vereist] | Hiermee geeft u de naam op van de eigenschap die u wilt krijgen. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-property-get"></a>sfctl eigendom te krijgen
Hier krijgt u de opgegeven eigenschap Service Fabric.

Krijgt de opgegeven eigenschap Service Fabric onder een bepaalde naam. Dit zal altijd zowel waarde als metadata retourneren.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --name-id [Vereist] | De naam Service Fabric,\:zonder het 'fabric' URI schema. |
| --eigenschap-naam [Vereist] | Hiermee geeft u de naam op van de eigenschap die u wilt krijgen. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-property-list"></a>sfctl-eigenschappenlijst
Krijgt informatie over alle eigenschappen van Service Fabric onder een bepaalde naam.

Een naam van een servicestructuur kan een of meer benoemde eigenschappen bevatten die aangepaste informatie opslaan. Met deze bewerking wordt de informatie over deze eigenschappen in een lijst met pagina's weergegeven. De informatie bevat naam, waarde en metagegevens over elk van de eigenschappen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --name-id [Vereist] | De naam Service Fabric,\:zonder het 'fabric' URI schema. |
| --continuation-token | De parameter vervolgtoken wordt gebruikt om volgende set resultaten te verkrijgen. Een vervolgtoken met een niet-lege waarde wordt opgenomen in de reactie van de API wanneer de resultaten van het systeem niet in één reactie passen. Wanneer deze waarde wordt doorgegeven aan de volgende API-aanroep, retourneert de API de volgende set resultaten. Als er geen verdere resultaten zijn, bevat het vervolgtoken geen waarde. De waarde van deze parameter mag niet worden gecodeerd met url's. |
| --include-waarden | Hiermee u opgeven of de waarden van de geretourneerde eigenschappen moeten worden opgenomen. True als waarden moeten worden geretourneerd met de metagegevens; False om alleen metagegevens van eigenschapsagegevens terug te geven. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-property-put"></a>sfctl eigendom gezet
Hiermee maakt of werkt u een eigenschap Service Fabric bij.

Hiermee maakt of werkt u de opgegeven eigenschap ServiceFabric onder een bepaalde naam.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --name-id [Vereist] | De naam Service Fabric,\:zonder het 'fabric' URI schema. |
| --eigenschap-naam [Vereist] | De naam van de eigenschap Service Fabric. |
| --waarde [Vereist] | Beschrijft de waarde van de eigenschap Service Fabric. Dit is een JSON-snaar. <br><br> De json-tekenreeks heeft twee velden, de 'Soort' van de gegevens en de waarde, ingevoerd als 'Gegevens' van de gegevens. De waarde 'Vriendelijk' moet het eerste item zijn dat in de JSON-tekenreeks wordt weergegeven en kan waarden zijn 'Binary', 'Int64', 'Double', 'String' of 'Guid'. De waarde moet serialiseren-staat voor de gegeven types. Zowel de waarden 'Vriendelijk' als 'Gegevens' moeten als tekenreeksen worden verstrekt. |
| --custom-id-type | De aangepaste type-id van de accommodatie. Met behulp van deze eigenschap kan de gebruiker het type waarde van de eigenschap taggen. |
| --time-out -t | Standaard\: 60. |

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