---
title: Azure Service Fabric CLI-implementatie van sfctl-net
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten voor het maken van Service Fabric netresources.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 108389407221779ed20e81310f084b7b5c23b8c7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76906036"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Service Fabric netresources maken.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| maken | Hiermee maakt u een implementatie van Service Fabric netresources. |

## <a name="sfctl-mesh-deployment-create"></a>implementatie van sfctl-net maken
Hiermee maakt u een implementatie van Service Fabric netresources.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --input-YAML-files [required] | Door komma's gescheiden of absolute bestands paden van alle YAML-bestanden of relatief of absoluut pad van de directory (recursieve) die YAML-bestanden bevatten. |
| --para meters | Een relatief of absoluut pad naar een yaml-bestand of een JSON-object dat de para meters bevat die moeten worden overschreven. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --fouten opsporen | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

### <a name="examples"></a>Voorbeelden

Consolideert en implementeert alle resources naar het cluster door de para meters die worden vermeld in het yaml-bestand te overschrijven.
``` 
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters  
./param.yaml    
```

Consolideert en implementeert alle resources in een directory naar het cluster door de para meters die worden vermeld in het yaml-bestand te overschrijven.

``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Consolideert en implementeert alle resources in een directory naar een cluster door de para meters die rechtstreeks worden door gegeven als JSON-object te overschrijven
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>Volgende stappen
- [Stel](service-fabric-cli.md) de service Fabric cli in.
- Meer informatie over het gebruik van de Service Fabric CLI met behulp van de [voorbeeld scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).
