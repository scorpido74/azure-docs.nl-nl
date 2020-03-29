---
title: Azure Service Fabric CLI- sfctl-mesh-implementatie
description: Meer informatie over sfctl, de Azure Service Fabric-opdrachtregelinterface. Bevat een lijst met opdrachten voor het maken van Service Fabric Mesh-bronnen.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 108389407221779ed20e81310f084b7b5c23b8c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906036"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Maak mesh-bronnen voor servicefabric.Create Service Fabric Mesh resources.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| maken | Hiermee maakt u een implementatie van mesh-bronnen voor servicestructuur. |

## <a name="sfctl-mesh-deployment-create"></a>sfctl mesh-implementatie maken
Hiermee maakt u een implementatie van mesh-bronnen voor servicestructuur.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --input-yaml-bestanden [Vereist] | Door komma's gescheiden relatieve of absolute bestandspaden van alle yaml-bestanden of relatief of absoluut pad van de map (recursieve) die yaml-bestanden bevatten. |
| --parameters | Een relatief of absoluut pad naar een yaml-bestand of een json-object dat de parameters bevat die moeten worden overschreven. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

### <a name="examples"></a>Voorbeelden

Consolideert en implementeert alle resources om te clusteren door de parameters in het yaml-bestand te overschrijven
``` 
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters  
./param.yaml    
```

Consolideert en implementeert alle resources in een map om te clusteren door de parameters in het yaml-bestand te overschrijven

``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Hiermee worden alle resources in een map geconsolideerd en geïmplementeerd om te clusteren door de parameters die rechtstreeks als json-object worden doorgegeven, over te schrijven
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>Volgende stappen
- Stel de SERVICE Fabric CLI [in.](service-fabric-cli.md)
- Meer informatie over het gebruik van de CLI van de ServiceFabric met behulp van de [voorbeeldscripts](/azure/service-fabric/scripts/sfctl-upgrade-application).
