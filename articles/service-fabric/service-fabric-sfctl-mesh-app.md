---
title: Azure Service Fabric CLI-sfctl mesh-app | Microsoft Docs
description: Beschrijft de Service Fabric CLI-opdrachten voor sfctl net-apps.
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
ms.openlocfilehash: 18cf1107ed86915f96dbc92d588a6ebedab530b4
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901481"
---
# <a name="sfctl-mesh-app"></a>sfctl mesh app
Toepassings resources ophalen en verwijderen.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| delete | Hiermee verwijdert u de toepassings resource. |
| list | Een lijst met alle toepassings bronnen. |
| weergeven | Hiermee wordt de toepassings resource met de opgegeven naam opgehaald. |

## <a name="sfctl-mesh-app-delete"></a>sfctl-mesh-app verwijderen
Hiermee verwijdert u de toepassings resource.

Hiermee verwijdert u de toepassings resource die wordt geïdentificeerd door de naam.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --naam-n [vereist] | De naam van de toepassing. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --fouten opsporen | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-mesh-app-list"></a>lijst met sfctl mesh-apps
Een lijst met alle toepassings bronnen.

Hiermee haalt u de informatie op over alle toepassings resources in een bepaalde resource groep. De informatie bevat de beschrijving en andere eigenschappen van de toepassing.

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --fouten opsporen | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-mesh-app-show"></a>sfctl-mesh-app weer geven
Hiermee wordt de toepassings resource met de opgegeven naam opgehaald.

Hiermee wordt de informatie opgehaald over de toepassings resource met de opgegeven naam. De informatie bevat de beschrijving en andere eigenschappen van de toepassing.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --naam-n [vereist] | De naam van de toepassing. |

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