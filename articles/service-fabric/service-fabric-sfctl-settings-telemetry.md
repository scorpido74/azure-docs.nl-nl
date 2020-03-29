---
title: Azure Service Fabric CLI- sfctl-instellingen telemetrie
description: Meer informatie over sfctl, de opdrachtregelinterface van Azure Service Fabric. Bevat een lijst met opdrachten voor het configureren van sfctl-telemetrie.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 6af5fa944ef399756f9e890ddd77a7f5f32e2bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76903026"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
Configureer telemetrie-instellingen lokaal naar dit exemplaar van sfctl.

Sfctl telemetrie verzamelt opdrachtnaam zonder parameters opgegeven of hun waarden, sfctl versie, OS type, python versie, het succes of falen van de opdracht, het foutbericht geretourneerd.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| set-telemetrie | Telemetrie in- of uitschakelen. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl-instellingen telemetrie
Telemetrie in- of uitschakelen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --uit | Schakel telemetrie uit. |
| --on | Schakel telemetrie in. Dit is de standaardwaarde. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

### <a name="examples"></a>Voorbeelden

Schakel telemetrie uit.

```
sfctl settings telemetry set_telemetry --off
```

Schakel telemetrie in.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>Volgende stappen
- Stel de SERVICE Fabric CLI [in.](service-fabric-cli.md)
- Meer informatie over het gebruik van de CLI van de ServiceFabric met behulp van de [voorbeeldscripts](/azure/service-fabric/scripts/sfctl-upgrade-application).