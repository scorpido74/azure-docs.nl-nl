---
title: Systeemvariabelen in Azure Data Factory
description: In dit artikel worden systeemvariabelen beschreven die worden ondersteund door Azure Data Factory. U deze variabelen gebruiken in expressies bij het definiëren van entiteiten in Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: 2690ded0ac45719cb1082c85ab535c91ad491172
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417960"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Systeemvariabelen ondersteund door Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden systeemvariabelen beschreven die worden ondersteund door Azure Data Factory. U deze variabelen gebruiken in expressies bij het definiëren van entiteiten in Data Factory.

## <a name="pipeline-scope"></a>Pijplijnbereik
Naar deze systeemvariabelen kan overal in de JSON van de pijplijn worden verwezen.

| Naam variabele | Beschrijving |
| --- | --- |
| @pipeline(). Datafactory |Naam van de gegevensfabriek waarin de pijplijn wordt uitgevoerd |
| @pipeline(). Pijpleiding |Naam van de pijplijn |
| @pipeline(). RunId (RunId) | ID van de specifieke pijplijnrun |
| @pipeline(). TriggerType | Type van de trigger die de pijplijn aanriep (Handmatig, scheduler) |
| @pipeline(). TriggerId (TriggerId)| ID van de trigger die de pijplijn aanroept |
| @pipeline(). TriggerName| Naam van de trigger die de pijplijn aanroept |
| @pipeline(). TriggerTime| Tijd waarop de trigger die de pijplijn aanriep. De triggertijd is de werkelijke vuurtijd, niet de geplande tijd. Wordt bijvoorbeeld `13:20:08.0149599Z` geretourneerd in plaats van`13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Triggerbereik plannen
Deze systeemvariabelen kunnen overal in de trigger JSON worden verwezen als de trigger van het type is: 'ScheduleTrigger'.

| Naam variabele | Beschrijving |
| --- | --- |
| @trigger().scheduledTime |Het tijdstip waarop de trigger was gepland om de pijplijn run aan te roepen. Bijvoorbeeld, voor een trigger die elke 5 min `2017-06-01T22:20:00Z` `2017-06-01T22:25:00Z`branden, zou deze variabele terugkeren , `2017-06-01T22:30:00Z` respectievelijk.|
| @trigger().startTime |Tijd waarop de trigger **daadwerkelijk** afgevuurd om de pijplijn aan te roepen lopen. Bijvoorbeeld, voor een trigger die elke 5 min branden, `2017-06-01T22:20:00.4061448Z` `2017-06-01T22:25:00.7958577Z`kan `2017-06-01T22:30:00.9935483Z` deze variabele iets als dit terug te keren, respectievelijk. (Opmerking: de tijdstempel is standaard in de ISO 8601-indeling)|

## <a name="tumbling-window-trigger-scope"></a>Tumbling Window Trigger scope
Deze systeemvariabelen kunnen overal in de trigger JSON worden verwezen als de trigger van het type is: "TumblingWindowTrigger."
(Opmerking: de tijdstempel is standaard in de ISO 8601-indeling)

| Naam variabele | Beschrijving |
| --- | --- |
| @trigger().uitvoer.windowStartTime |Begin van het venster toen de trigger was gepland om de pijplijnrun aan te roepen. Als de tuimelende venster trigger heeft een frequentie van "per uur" zou dit de tijd aan het begin van het uur.|
| @trigger().uitvoer.windowEndTime |Einde van het venster toen de trigger was gepland om de pijplijn run aan te roepen. Als de tuimelende venster trigger heeft een frequentie van "per uur" zou dit de tijd aan het einde van het uur.|
## <a name="next-steps"></a>Volgende stappen
Zie [Taal & functies voor](control-flow-expression-language-functions.md)informatie over hoe deze variabelen in expressies worden gebruikt.
