---
title: Azure Service Fabric CLI-sfctl mesh-netwerk | Microsoft Docs
description: Beschrijft de Service Fabric CLI-netwerk opdrachten voor sfctl.
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
ms.openlocfilehash: 4d74e3d37891ad50d8bbaa69757d0d99c80591cc
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901219"
---
# <a name="sfctl-mesh-network"></a>sfctl mesh network
Net-netwerk resources ophalen en verwijderen.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| delete | Hiermee verwijdert u de netwerk bron. |
| list | Een lijst met alle netwerk resources. |
| weergeven | Hiermee wordt de netwerk bron met de opgegeven naam opgehaald. |

## <a name="sfctl-mesh-network-delete"></a>sfctl net-netwerk verwijderen
Hiermee verwijdert u de netwerk bron.

Hiermee verwijdert u de netwerk resource die wordt geïdentificeerd door de naam.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --naam-n [vereist] | De naam van het netwerk. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --fouten opsporen | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-mesh-network-list"></a>lijst met sfctl mesh-netwerken
Een lijst met alle netwerk resources.

Hiermee haalt u de informatie over alle netwerk resources in een bepaalde resource groep op. De informatie bevat de beschrijving en andere eigenschappen van het netwerk.

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --fouten opsporen | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-mesh-network-show"></a>sfctl net-netwerk weer geven
Hiermee wordt de netwerk bron met de opgegeven naam opgehaald.

Hiermee wordt de informatie opgehaald over de netwerk bron met de opgegeven naam. De informatie bevat de beschrijving en andere eigenschappen van het netwerk.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --naam-n [vereist] | De naam van het netwerk. |

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