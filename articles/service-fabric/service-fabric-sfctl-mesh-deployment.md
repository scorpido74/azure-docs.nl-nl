---
title: Azure Service Fabric CLI-implementatie van sfctl-net | Microsoft Docs
description: Hierin worden de implementatie-opdrachten voor de Service Fabric CLI sfctl-net beschreven.
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
ms.openlocfilehash: dbecf8e96c6cb5d0d6d873309d848304fe33eaaa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901256"
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
| --input-YAML-files [required] | Door komma's gescheiden relatieve/absolute bestands paden van alle YAML-bestanden of relatief/absoluut pad van de directory (recursieve) die YAML-bestanden bevatten. |
| --para meters | Een relatief/absoluut pad naar het yaml-bestand of een JSON-object dat de para meters bevat die moeten worden overschreven. |

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