---
title: Azure Service Fabric CLI- sfctl is
description: Meer informatie over sfctl, de Azure Service Fabric-opdrachtregelinterface. Bevat een lijst met opdrachten voor het beheer van de infrastructuur.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: a64aaca97c9df61d795c82d64e6048d6f9ae032f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906090"
---
# <a name="sfctl-is"></a>sfctl is
Opdrachten opvragen en verzenden naar de infrastructuurservice.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| command | Beroept zich op een administratieve opdracht op de gegeven instantie Infrastructure Service. |
| query | Roept een alleen-lezen query op voor het opgegeven instantie voor infrastructuurservice. |

## <a name="sfctl-is-command"></a>sfctl is opdracht
Beroept zich op een administratieve opdracht op de gegeven instantie Infrastructure Service.

Voor clusters waarvan een of meer exemplaren van de infrastructuurservice zijn geconfigureerd, biedt deze API een manier om infrastructuurspecifieke opdrachten naar een bepaald exemplaar van de infrastructuurservice te verzenden. Beschikbare opdrachten en de bijbehorende responsnotaties variëren afhankelijk van de infrastructuur waarop het cluster wordt uitgevoerd. Deze API ondersteunt het Service Fabric-platform; het is niet bedoeld om rechtstreeks vanuit uw code te worden gebruikt.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --opdracht [Vereist] | De tekst van de opdracht die moet worden aangeroepen. De inhoud van de opdracht is infrastructuurspecifiek. |
| --service-id | De identiteit van de infrastructuurdienst. <br><br> Dit is de volledige naam van de\:infrastructuurdienst zonder het 'fabric' URI-schema. Deze parameter is alleen vereist voor het cluster dat meer dan één instantie van infrastructuurservice uitvoert. |
| --time-out -t | Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-is-query"></a>sfctl is query
Roept een alleen-lezen query op voor het opgegeven instantie voor infrastructuurservice.

Voor clusters waarvan een of meer exemplaren van de infrastructuurservice zijn geconfigureerd, biedt deze API een manier om infrastructuurspecifieke query's naar een bepaald exemplaar van de infrastructuurservice te verzenden. Beschikbare opdrachten en de bijbehorende responsnotaties variëren afhankelijk van de infrastructuur waarop het cluster wordt uitgevoerd. Deze API ondersteunt het Service Fabric-platform; het is niet bedoeld om rechtstreeks vanuit uw code te worden gebruikt.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --opdracht [Vereist] | De tekst van de opdracht die moet worden aangeroepen. De inhoud van de opdracht is infrastructuurspecifiek. |
| --service-id | De identiteit van de infrastructuurdienst. <br><br> Dit is de volledige naam van de\:infrastructuurdienst zonder het 'fabric' URI-schema. Deze parameter is alleen vereist voor het cluster dat meer dan één instantie van infrastructuurservice uitvoert. |
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
