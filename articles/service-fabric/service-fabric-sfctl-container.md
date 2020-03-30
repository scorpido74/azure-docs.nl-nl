---
title: Azure Service Fabric CLI- sfctl-container
description: Meer informatie over sfctl, de opdrachtregelinterface van Azure Service Fabric. Bevat een lijst met opdrachten voor containers.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 07861e2b67eea67740b341cbea994de978973664
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906113"
---
# <a name="sfctl-container"></a>sfctl container
Containergerelateerde opdrachten uitvoeren op een clusterknooppunt.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| invoke-api | Een container-API aanroepen op een container die is geïmplementeerd op een Service Fabric-knooppunt voor het opgegeven codepakket. |
| logboeken | Hier worden de containerlogboeken voor container geïmplementeerd op een Service Fabric-knooppunt. |

## <a name="sfctl-container-invoke-api"></a>sfctl container invoke-api
Een container-API aanroepen op een container die is geïmplementeerd op een Service Fabric-knooppunt voor het opgegeven codepakket.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-id [Vereist] | De identiteit van de applicatie. <br><br> Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de toepassingsnaam bijvoorbeeld "fabric\:/myapp/app1" is, is\~de identiteit van de toepassing "myapp app1" in 6.0+ en "myapp/app1" in eerdere versies. |
| --code-package-instance-id [Vereist] | ID die op unieke wijze een codepakketinstantie identificeert die is geïmplementeerd op een servicefabricknooppunt. <br><br> Kan worden opgehaald met 'service code-package-list'. |
| --code-package-name [Vereist] | De naam van het codepakket dat is opgegeven in het servicemanifest dat is geregistreerd als onderdeel van een toepassingstype in een cluster servicestructuur. |
| --container-api-uri-pad [Vereist] | Container REST API URI pad, gebruik '{ID}' in plaats van containernaam/id. |
| --node-name [Vereist] | De naam van het knooppunt. |
| --service-manifest-naam [Vereist] | De naam van een servicemanifest dat is geregistreerd als onderdeel van een toepassingstype in een cluster servicestructuur. |
| --container-api-body | HTTP-aanvraagbody voor containerREST API. |
| --container-api-content-type | Inhoudstype voor container REST API, standaard 'application/json'. |
| --container-api-http-werkwoord | HTTP-werkwoord voor containerREST API, standaard get. |
| --time-out -t | Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-container-logs"></a>sfctl-containerlogboeken
Hier worden de containerlogboeken voor container geïmplementeerd op een Service Fabric-knooppunt.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-id [Vereist] | De identiteit van de applicatie. <br><br> Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de toepassingsnaam bijvoorbeeld "fabric\:/myapp/app1" is, is\~de identiteit van de toepassing "myapp app1" in 6.0+ en "myapp/app1" in eerdere versies. |
| --code-package-instance-id [Vereist] | Code pakket instance ID, die kan worden opgehaald door 'service code-package-list'. |
| --code-package-name [Vereist] | De naam van het codepakket dat is opgegeven in het servicemanifest dat is geregistreerd als onderdeel van een toepassingstype in een cluster servicestructuur. |
| --node-name [Vereist] | De naam van het knooppunt. |
| --service-manifest-naam [Vereist] | De naam van een servicemanifest dat is geregistreerd als onderdeel van een toepassingstype in een cluster servicestructuur. |
| --staart | Aantal regels dat vanaf het einde van de logboeken moet worden weergegeven. Standaard is 100. 'alles' om de volledige logs weer te geven. |
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