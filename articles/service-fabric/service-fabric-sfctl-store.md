---
title: Azure Service Fabric CLI- sfctl-winkel
description: Meer informatie over sfctl, de opdrachtregelinterface van Azure Service Fabric. Bevat een lijst met opdrachten voor het uitvoeren van bestandsniveaubewerkingen in het clusterimagearchief.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 75c62b54ff3aa7f3af344aa3e1ca81d431ae0ab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905621"
---
# <a name="sfctl-store"></a>sfctl store
Voer bewerkingen op basisbestandsniveau uit in het clusterimagearchief.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| delete | Hiermee verwijdert u bestaande inhoud van het afbeeldingsarchief. |
| root-info | Krijgt de inhoudsinformatie aan de basis van het afbeeldingsarchief. |
| Stat | Hier worden de inhoudsinformatie van de afbeeldingswinkel weergegeven. |

## <a name="sfctl-store-delete"></a>sfctl store verwijderen
Hiermee verwijdert u bestaande inhoud van het afbeeldingsarchief.

Hiermee verwijdert u bestaande inhoud van het afbeeldingsarchief die wordt gevonden in het opgegeven relatieve pad van het afbeeldingsarchief. Deze opdracht kan worden gebruikt om geüploade toepassingspakketten te verwijderen zodra ze zijn ingericht.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --inhoud-pad [Vereist] | Relatief pad naar bestand of map in het afbeeldingsarchief vanaf de hoofdmap. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-store-root-info"></a>sfctl store root-info
Krijgt de inhoudsinformatie aan de basis van het afbeeldingsarchief.

Retourneert de informatie over de inhoud van het afbeeldingsarchief aan de hoofdmap van het afbeeldingsarchief.

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

## <a name="sfctl-store-stat"></a>sfctl winkel stat
Hier worden de inhoudsinformatie van de afbeeldingswinkel weergegeven.

Geeft als resultaat de informatie over de inhoud van het afbeeldingsarchief op de opgegeven contentPath. De contentPath is relatief ten opzichte van de hoofdmap van het afbeeldingsarchief.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --inhoud-pad [Vereist] | Relatief pad naar bestand of map in het afbeeldingsarchief vanaf de hoofdmap. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |


## <a name="next-steps"></a>Volgende stappen
- [Stel](service-fabric-cli.md) de SERVICE Fabric CLI in.
- Meer informatie over het gebruik van de CLI van de ServiceFabric met behulp van de [voorbeeldscripts](/azure/service-fabric/scripts/sfctl-upgrade-application).