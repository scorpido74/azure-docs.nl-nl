---
title: Azure Service Fabric CLI-sfctl-container | Microsoft Docs
description: Hierin worden de sfctl-container opdrachten van Service Fabric CLI beschreven.
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
ms.openlocfilehash: 13de6ff7b3e5a41eced5ca49a3af38fab60ba0a0
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901301"
---
# <a name="sfctl-container"></a>sfctl container
Container gerelateerde opdrachten uitvoeren op een cluster knooppunt.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| invoke-API | Container-API aanroepen voor een container die is geïmplementeerd op een Service Fabric knoop punt voor het gegeven code pakket. |
| logboeken | Hiermee worden de container logboeken opgehaald voor de container die op een Service Fabric knoop punt is geïmplementeerd. |

## <a name="sfctl-container-invoke-api"></a>sfctl-container aanroepen-API
Container-API aanroepen voor een container die is geïmplementeerd op een Service Fabric knoop punt voor het gegeven code pakket.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --toepassings-id [vereist] | De identiteit van de toepassing. <br><br> Dit is doorgaans de volledige naam van de toepassing zonder het URI-schema van het infrastructuur\:. Met ingang van versie 6,0 worden hiërarchische namen gescheiden met het teken '\~'. Als de naam van de toepassing bijvoorbeeld ' infrastructuur\:/MyApp/app1 ' is, is de toepassings-id ' Mijntoep\~app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
| --code-package-instance-id [vereist] | ID die een unieke identificatie vormt van een code pakket exemplaar dat is geïmplementeerd op een service Fabric-knoop punt. <br><br> Kan worden opgehaald door service code-Package List. |
| --code-pakket naam [vereist] | De naam van het code pakket dat is opgegeven in het service manifest, geregistreerd als onderdeel van een toepassings type in een Service Fabric cluster. |
| --container-API-URI-pad [vereist] | Container REST API URI-pad, gebruik {ID} in plaats van container naam/id. |
| --node-name [required] | De naam van het knoop punt. |
| --Service-manifest-naam [vereist] | De naam van een service manifest dat is geregistreerd als onderdeel van een toepassings type in een Service Fabric cluster. |
| --container-API-hoofd tekst | Hoofd tekst van de HTTP-aanvraag voor container REST API. |
| --container-API-inhouds type | Inhouds type voor container REST API, wordt standaard ingesteld op Application/JSON. |
| --container-API-http-verb | HTTP-term voor container REST API, standaard ingesteld op ophalen. |
| --time-out-t | Standaard\: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --fouten opsporen | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-container-logs"></a>sfctl-container logboeken
Hiermee worden de container logboeken opgehaald voor de container die op een Service Fabric knoop punt is geïmplementeerd.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --toepassings-id [vereist] | De identiteit van de toepassing. <br><br> Dit is doorgaans de volledige naam van de toepassing zonder het URI-schema van het infrastructuur\:. Met ingang van versie 6,0 worden hiërarchische namen gescheiden met het teken '\~'. Als de naam van de toepassing bijvoorbeeld ' infrastructuur\:/MyApp/app1 ' is, is de toepassings-id ' Mijntoep\~app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
| --code-package-instance-id [vereist] | Code pakket exemplaar-ID, die kan worden opgehaald door service code-Package List. |
| --code-pakket naam [vereist] | De naam van het code pakket dat is opgegeven in het service manifest, geregistreerd als onderdeel van een toepassings type in een Service Fabric cluster. |
| --node-name [required] | De naam van het knoop punt. |
| --Service-manifest-naam [vereist] | De naam van een service manifest dat is geregistreerd als onderdeel van een toepassings type in een Service Fabric cluster. |
| --staart | Aantal regels dat moet worden weer gegeven aan het einde van de logboeken. De standaard waarde is 100. ' all ' om de volledige logboeken weer te geven. |
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