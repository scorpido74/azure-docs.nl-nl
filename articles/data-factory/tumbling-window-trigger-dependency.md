---
title: Afhankelijkheden voor tumblingvenstertriggers-venster maken
description: Meer informatie over het maken van afhankelijkheden voor een tumblingvenstertriggers-venster trigger in Azure Data Factory.
services: data-factory
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/29/2019
ms.openlocfilehash: 3a4d31cb6986f8fc841a6afe20388e40e9f28c9b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926677"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Een afhankelijkheid voor een tumblingvenstertrigger maken

In dit artikel worden de stappen beschreven voor het maken van een afhankelijkheid van een tumblingvenstertriggers-venster trigger. Zie [How to Create tumblingvenstertriggers Window trigger](how-to-create-tumbling-window-trigger.md)(Engelstalig) voor algemene informatie over Tumblingvenstertriggers-venster triggers.

Als u een afhankelijkheids keten wilt maken en ervoor wilt zorgen dat een trigger pas wordt uitgevoerd nadat een andere trigger in de data factory is uitgevoerd, gebruikt u deze geavanceerde functie om een tumblingvenstertriggers-venster afhankelijkheid te maken.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Een afhankelijkheid maken in de Data Factory-gebruikers interface

Als u afhankelijkheid wilt maken voor een trigger, selecteert u **trigger > geavanceerd > nieuw**en kiest u vervolgens de trigger die afhankelijk is van de juiste offset en grootte. Selecteer **volt ooien** en publiceer de Data Factory wijzigingen voordat de afhankelijkheden van kracht worden.

![Afhankelijkheid maken](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "Afhankelijkheid maken")

## <a name="tumbling-window-dependency-properties"></a>Eigenschappen van afhankelijkheids venster tumblingvenstertriggers

Een tumblingvenstertriggers-venster trigger met een afhankelijkheid heeft de volgende eigenschappen:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        }
    }
}
```

De volgende tabel bevat de lijst met kenmerken die nodig zijn voor het definiëren van een Tumblingvenstertriggers-venster afhankelijkheid.

| **Eigenschaps naam** | **Beschrijving**  | **Type** | **Vereist** |
|---|---|---|---|
| type  | Alle bestaande tumblingvenstertriggers-venster triggers worden weer gegeven in deze vervolg keuzelijst. Kies de trigger voor het maken van afhankelijkheden.  | TumblingWindowTriggerDependencyReference of SelfDependencyTumblingWindowTriggerReference | Ja |
| offset | Verschuiving van de afhankelijkheids trigger. Geef een waarde op in de indeling van de tijd en zowel negatieve als positieve verschuivingen zijn toegestaan. Deze eigenschap is verplicht als de trigger afhankelijk is van zichzelf en in alle andere gevallen optioneel is. De Self-afhankelijkheid moet altijd een negatieve verschuiving zijn. Als er geen waarde is opgegeven, is het venster hetzelfde als de trigger. | Periode<br/>(UU: mm: SS) | Zelf afhankelijkheid: Ja<br/>Overige: Nee |
| size | Grootte van het tumblingvenstertriggers-venster van de afhankelijkheid. Geef een positieve time span-waarde op. Deze eigenschap is optioneel. | Periode<br/>(UU: mm: SS) | Nee  |

> [!NOTE]
> Een trigger voor een tumblingvenstertriggers-venster kan afhankelijk zijn van een maximum van twee andere triggers.

## <a name="tumbling-window-self-dependency-properties"></a>Eigenschappen van tumblingvenstertriggers-venster met eigen afhankelijkheid

In scenario's waarin de trigger niet moet door gaan naar het volgende venster totdat het vorige venster is voltooid, bouwt u een self-afhankelijkheid. Een trigger voor zelf afhankelijkheid die afhankelijk is van het succes van eerdere versies van zichzelf in de vorige werk plaats, heeft de volgende eigenschappen:

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```
## <a name="usage-scenarios-and-examples"></a>Gebruiks scenario's en voor beelden

Hieronder ziet u een aantal voor beelden van scenario's en het gebruik van eigenschappen van tumblingvenstertriggers-venster afhankelijkheid.

### <a name="dependency-offset"></a>Offset van afhankelijkheid

![Offset-voor beeld](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "Offset-voor beeld")

### <a name="dependency-size"></a>Grootte van afhankelijkheid

![Voor beeld van grootte](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "Voor beeld van grootte")

### <a name="self-dependency"></a>Zelf afhankelijkheid

![Zelf afhankelijkheid](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "Zelf afhankelijkheid")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Afhankelijkheid van een andere tumblingvenstertriggers-venster trigger

Een dagelijkse telemetrie-verwerkings taak, afhankelijk van een andere dagelijkse taak waarmee de uitvoer van de laatste zeven dagen wordt geaggregeerd en die zeven dagen worden gegenereerd:

![Voor beeld van afhankelijkheid](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "Voor beeld van afhankelijkheid")

### <a name="dependency-on-itself"></a>Afhankelijkheid op zichzelf

Een dagelijkse taak zonder hiaten in de uitvoer stromen van de taak:

![Voor beeld van Self-afhankelijkheid](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "Voor beeld van Self-afhankelijkheid")

## <a name="monitor-dependencies"></a>Afhankelijkheden controleren

U kunt de afhankelijkheids keten en de bijbehorende Vensters bewaken vanaf de controle pagina voor het uitvoeren van triggers. Navigeer naar **bewaking > trigger uitvoeringen**. In de kolom acties kunt u de trigger opnieuw uitvoeren of de afhankelijkheden ervan weer geven.

![Trigger uitvoeringen controleren](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "Triggeruitvoeringen controleren")

Als u op trigger afhankelijkheden weer geven klikt, ziet u de status van de afhankelijkheden. Als een van de afhankelijkheids triggers mislukt, moet u deze opnieuw uitvoeren om de afhankelijke trigger uit te voeren. Een trigger voor een tumblingvenstertriggers-venster wacht op afhankelijkheden voor zeven dagen voordat een time-out optreedt.

![Afhankelijkheden controleren](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "Afhankelijkheden controleren")

Als u een visueel element wilt weer geven, selecteert u de Gantt-weer gave.

![Afhankelijkheden controleren](media/tumbling-window-trigger-dependency/tumbling-window-dependency09.png "Afhankelijkheden controleren")

## <a name="next-steps"></a>Volgende stappen

* Lees [hoe u een tumblingvenstertriggers-venster trigger maakt](how-to-create-tumbling-window-trigger.md)
