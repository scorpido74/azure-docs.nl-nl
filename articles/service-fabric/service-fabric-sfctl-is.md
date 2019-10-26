---
title: Azure Service Fabric CLI-sfctl is | Microsoft Docs
description: Beschrijft de Service Fabric CLI sfctl is-opdrachten.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 9a09d8c36fd282450767880a9ad144e1192dcd2e
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901568"
---
# <a name="sfctl-is"></a>sfctl is
Query's uitvoeren en opdrachten verzenden naar de infrastructuur service.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| command | Hiermee wordt een beheer opdracht aangeroepen voor het opgegeven infrastructuur service-exemplaar. |
| query | Hiermee wordt een alleen-lezen query aangeroepen voor het opgegeven infrastructuur service-exemplaar. |

## <a name="sfctl-is-command"></a>sfctl is opdracht
Hiermee wordt een beheer opdracht aangeroepen voor het opgegeven infrastructuur service-exemplaar.

Voor clusters waarvoor een of meer exemplaren van de infrastructuur service zijn geconfigureerd, biedt deze API een manier om met een infra structuur-specifieke opdracht naar een bepaald exemplaar van de infrastructuur service te verzenden. Beschik bare opdrachten en de bijbehorende antwoord indelingen variëren, afhankelijk van de infra structuur waarop het cluster wordt uitgevoerd. Deze API ondersteunt het Service Fabric-platform. het is niet bedoeld om rechtstreeks vanuit uw code te worden gebruikt.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --opdracht [vereist] | De tekst van de opdracht die moet worden aangeroepen. De inhoud van de opdracht is specifiek voor een infra structuur. |
| --Service-id | De identiteit van de infrastructuur service. <br><br> Dit is de volledige naam van de infrastructuur service zonder het URI-schema van het Fabric-\:. Deze para meter is alleen vereist voor het cluster waarop meer dan één exemplaar van de infrastructuur service wordt uitgevoerd. |
| --time-out-t | Standaard\: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --fouten opsporen | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-is-query"></a>sfctl is query
Hiermee wordt een alleen-lezen query aangeroepen voor het opgegeven infrastructuur service-exemplaar.

Voor clusters waarvoor een of meer exemplaren van de infrastructuur service zijn geconfigureerd, biedt deze API een manier om infrastructuur query's te verzenden naar een bepaald exemplaar van de infrastructuur service. Beschik bare opdrachten en de bijbehorende antwoord indelingen variëren, afhankelijk van de infra structuur waarop het cluster wordt uitgevoerd. Deze API ondersteunt het Service Fabric-platform. het is niet bedoeld om rechtstreeks vanuit uw code te worden gebruikt.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --opdracht [vereist] | De tekst van de opdracht die moet worden aangeroepen. De inhoud van de opdracht is specifiek voor een infra structuur. |
| --Service-id | De identiteit van de infrastructuur service. <br><br> Dit is de volledige naam van de infrastructuur service zonder het URI-schema van het Fabric-\:. Deze para meter is alleen vereist voor het cluster waarop meer dan één exemplaar van de infrastructuur service wordt uitgevoerd. |
| --time-out-t | Standaard\: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --fouten opsporen | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |


## <a name="next-steps"></a>Volgende stappen
- [Stel](service-fabric-cli.md) de service Fabric cli in.
- Meer informatie over het gebruik van de Service Fabric CLI met behulp van de [voorbeeld scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).