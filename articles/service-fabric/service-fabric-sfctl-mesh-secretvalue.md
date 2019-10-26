---
title: Azure Service Fabric CLI-sfctl mesh secretvalue | Microsoft Docs
description: Beschrijft de Service Fabric CLI sfctl net secretvalue-opdrachten.
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
ms.openlocfilehash: 96ce4efe00d89c136a0c11d445170b2f67be6fcd
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901178"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Net secretvalue-resources ophalen en verwijderen.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| delete | Hiermee verwijdert u de opgegeven waarde van de naam van de named Secret-resource. |
| list | Lijst met namen van alle waarden van de opgegeven geheime resource. |
| weergeven | Hiermee wordt de opgegeven waarde van de geheime resource weer gegeven. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl mesh secretvalue verwijderen
Hiermee verwijdert u de opgegeven waarde van de naam van de named Secret-resource.

Hiermee verwijdert u de bron van de geheime waarde die wordt geïdentificeerd door de naam. De naam van de resource is doorgaans de versie die aan die waarde is gekoppeld. Als de opgegeven waarde wordt gebruikt, mislukt de verwijdering.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --geheim-naam-n [vereist] | De naam van de geheime resource. |
| --versie-v [vereist] | De naam van de geheime versie. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --fouten opsporen | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-mesh-secretvalue-list"></a>sfctl mesh secretvalue-lijst
Lijst met namen van alle waarden van de opgegeven geheime resource.

Haalt informatie op over alle bronnen van de geheime waarde van de opgegeven geheime resource. De informatie bevat de namen van de resources van de geheime waarde, maar niet de werkelijke waarden.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --geheim-naam-n [vereist] | De naam van de geheime resource. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --fouten opsporen | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl mesh secretvalue show
Hiermee wordt de opgegeven waarde van de geheime resource weer gegeven.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --geheim-naam-n [vereist] | De naam van de geheime resource. |
| --versie-v [vereist] | De naam van de geheime versie. |
| --weer geven-waarde | De werkelijke waarde van de geheime versie weer geven. |

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