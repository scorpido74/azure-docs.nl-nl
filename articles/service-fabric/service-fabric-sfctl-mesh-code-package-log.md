---
title: Azure Service Fabric CLI-sfctl mesh code-pakket-log | Microsoft Docs
description: Beschrijft de Service Fabric CLI sfctl netcode-package-log-opdrachten.
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
ms.openlocfilehash: d1f0e34389a48b79c049f26e8b04c870f0f1a9a7
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901268"
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
| --fouten opsporen | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |


## <a name="next-steps"></a>Volgende stappen
- [Stel](service-fabric-cli.md) de service Fabric cli in.
- Meer informatie over het gebruik van de Service Fabric CLI met behulp van de [voorbeeld scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).