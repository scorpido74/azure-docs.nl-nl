---
title: Tumbling window trigger dependencies maken
description: Meer informatie over het maken van afhankelijkheid van een tuimelende venstertrigger in Azure Data Factory.
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
ms.openlocfilehash: 39ea8dda0fd823d3061b2cb29e1c548f99281c82
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418793"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Een afhankelijkheid voor een tumblingvenstertrigger maken
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel vindt u stappen om een afhankelijkheid van een tuimelende venstertrigger te maken. Zie [Hoe u de overtuimingvan het venster activeert](how-to-create-tumbling-window-trigger.md)voor algemene informatie over de triggers voor het tumblingwindow.

Gebruik deze geavanceerde functie om een afhankelijkheidsketen te bouwen en ervoor te zorgen dat een trigger pas wordt uitgevoerd na de succesvolle uitvoering van een andere trigger in de gegevensfabriek, deze geavanceerde functie om een tuimelende vensterafhankelijkheid te creëren.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Een afhankelijkheid maken in de gebruikersinterface van de gegevensfabriek

Als u afhankelijkheid van een trigger wilt maken, selecteert u **Trigger > Geavanceerd > Nieuw**en kiest u vervolgens de trigger waarop u afhankelijk bent van de juiste verschuiving en grootte. Selecteer **Voltooien** en publiceer de wijzigingen in de gegevensfabriek om de afhankelijkheden van kracht te laten worden.

![Afhankelijkheidscreatie](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "Afhankelijkheidscreatie")

## <a name="tumbling-window-dependency-properties"></a>Tumbling venster afhankelijkheid eigenschappen

Een tuimelende venstertrigger met een afhankelijkheid heeft de volgende eigenschappen:

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

In de volgende tabel vindt u de lijst met kenmerken die nodig zijn om een tumbling window-afhankelijkheid te definiëren.

| **Naam van eigenschap** | **Beschrijving**  | **Type** | **Vereist** |
|---|---|---|---|
| type  | Alle bestaande tuimelvenstertriggers worden weergegeven in deze vervolgkeuzelijst. Kies de trigger om afhankelijk te worden.  | TumblingWindowTriggerDependencyReference of SelfDependencyTumblingWindowTriggerReference TumblingWindowTrigger | Ja |
| offset | Verschuiving van de afhankelijkheidstrigger. Geef een waarde in tijdspanneformaat op en zowel negatieve als positieve verschuivingen zijn toegestaan. Deze eigenschap is verplicht als de trigger afhankelijk is van zichzelf en in alle andere gevallen optioneel. Zelfafhankelijkheid moet altijd een negatieve compensatie zijn. Als er geen waarde is opgegeven, is het venster hetzelfde als de trigger zelf. | Periode<br/>(hh:mm:ss) | Zelfafhankelijkheid: Ja<br/>Overig: Nee |
| grootte | Grootte van het tumbling venster van de afhankelijkheid. Zorg voor een positieve tijdswaarde. Deze eigenschap is optioneel. | Periode<br/>(hh:mm:ss) | Nee  |

> [!NOTE]
> Een tuimelende venster trigger kan afhangen van een maximum van twee andere triggers.

## <a name="tumbling-window-self-dependency-properties"></a>Zelfafhankelijkheidseigenschappen van tumbling window

In scenario's waarin de trigger niet naar het volgende venster moet gaan totdat het vorige venster is voltooid, bouwt u een zelfafhankelijkheid. Een zelfafhankelijkheidstrigger die afhankelijk is van het succes van eerdere runs van zichzelf binnen de vorige hr, heeft de onderstaande eigenschappen:

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
## <a name="usage-scenarios-and-examples"></a>Gebruiksscenario's en voorbeelden

Hieronder vindt u illustraties van scenario's en het gebruik van tumbling window dependency properties.

### <a name="dependency-offset"></a>Afhankelijkheidsverschuiving

![Voorbeeld van verschuiving](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "Voorbeeld van verschuiving")

### <a name="dependency-size"></a>Afhankelijkheidsgrootte

![Voorbeeld van grootte](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "Voorbeeld van grootte")

### <a name="self-dependency"></a>Zelfafhankelijkheid

![Zelfafhankelijkheid](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "Zelfafhankelijkheid")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Afhankelijkheid van een andere tuimelende venstertrigger

Een dagelijkse telemetrieverwerkingstaak, afhankelijk van een andere dagelijkse taak die de afgelopen zeven dagen uitvoer aggregeren en zeven dagen rollende vensterstromen genereert:

![Voorbeeld van afhankelijkheid](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "Voorbeeld van afhankelijkheid")

### <a name="dependency-on-itself"></a>Afhankelijkheid van zichzelf

Een dagelijkse taak zonder hiaten in de uitvoerstromen van de taak:

![Voorbeeld van zelfafhankelijkheid](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "Voorbeeld van zelfafhankelijkheid")

Bekijk de volgende video voor een demonstratie voor het maken van afhankelijke pijplijnen in uw Azure Data Factory met behulp van een tuimelvenstertrigger:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="monitor-dependencies"></a>Afhankelijkheden controleren

U de afhankelijkheidsketen en de bijbehorende vensters vanaf de monitorpagina triggerrun controleren. Navigeer naar **monitoring > triggerruns**. Onder de kolom Acties u de trigger opnieuw uitvoeren of de afhankelijkheden ervan weergeven.

![Triggeruitvoeringen controleren](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "Triggeruitvoeringen controleren")

Als u op 'Afhankelijkheid van triggermaken weergeven' klikt, u de status van de afhankelijkheden zien. Als een van de afhankelijkheidstriggers mislukt, moet u deze opnieuw uitvoeren om de afhankelijke trigger uit te voeren. Een tuimelende venster trigger zal wachten op afhankelijkheden voor zeven dagen voordat de timing uit.

![Afhankelijkheden controleren](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "Afhankelijkheden controleren")

Selecteer de Gantt-weergave voor een meer visual om de gebruiksschema voor de trigger weer te geven.

![Afhankelijkheden controleren](media/tumbling-window-trigger-dependency/tumbling-window-dependency09.png "Afhankelijkheden controleren")

## <a name="next-steps"></a>Volgende stappen

* Bekijken [Hoe maak je een tuimelende venstertrigger](how-to-create-tumbling-window-trigger.md)
