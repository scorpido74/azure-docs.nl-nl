---
title: Azure Service Fabric CLI- sfctl rpm
description: Meer informatie over sfctl, de opdrachtregelinterface van Azure Service Fabric. Bevat een lijst met opdrachten voor de reparatiemanagerservice.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 1787c0c8a8d5994ce1f1e9e4c55f155bbca6934c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904938"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Beveel opdrachten op en stuur ze naar de service voor reparatiebeheer.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| approve-force | Dwingt de goedkeuring van de gegeven reparatietaak. |
| delete | Hiermee verwijdert u een voltooide reparatietaak. |
| list | Hiermee krijgt u een lijst met reparatietaken die overeenkomen met de opgegeven filters. |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm approve-force
Dwingt de goedkeuring van de gegeven reparatietaak.

Deze API ondersteunt het Service Fabric-platform; het is niet bedoeld om rechtstreeks vanuit uw code te worden gebruikt.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --task-id [Vereist] | De id van de reparatietaak. |
| --versie | Het huidige versienummer van de reparatietaak. Als deze niet-nul is, wordt de aanvraag alleen uitgevoerd als deze waarde overeenkomt met de werkelijke huidige versie van de reparatietaak. Als nul, wordt er geen versiecontrole uitgevoerd. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-rpm-delete"></a>sfctl rpm verwijderen
Hiermee verwijdert u een voltooide reparatietaak.

Deze API ondersteunt het Service Fabric-platform; het is niet bedoeld om rechtstreeks vanuit uw code te worden gebruikt.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --task-id [Vereist] | De id van de voltooide reparatietaak die moet worden verwijderd. |
| --versie | Het huidige versienummer van de reparatietaak. Als deze niet-nul is, wordt de aanvraag alleen uitgevoerd als deze waarde overeenkomt met de werkelijke huidige versie van de reparatietaak. Als nul, wordt er geen versiecontrole uitgevoerd. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-rpm-list"></a>sfctl rpm lijst
Hiermee krijgt u een lijst met reparatietaken die overeenkomen met de opgegeven filters.

Deze API ondersteunt het Service Fabric-platform; het is niet bedoeld om rechtstreeks vanuit uw code te worden gebruikt.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| -executor-filter | De naam van de reparatieuitvoerder wiens geclaimde taken in de lijst moeten worden opgenomen. |
| --state-filter | Een bitwise-OR van de volgende waarden, waarin wordt aangegeven welke taakstatussen in de resultatenlijst moeten worden opgenomen. <ul><li>1 - Gemaakt</li><li>2 - Geclaimd</li><li>4 - Voorbereiden</li><li>8 - Goedgekeurd</li><li>16 - Uitvoeren</li><li>32 - Herstellen</li><li>64 - Voltooid</li></ul>
| --taak-id-filter | Het voorvoegsel van de reparatietaak-id dat moet worden gekoppeld. |

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
