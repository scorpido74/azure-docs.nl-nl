---
title: Kies een real-time en stream processing oplossing op Azure
description: Meer informatie over het kiezen van de juiste real-time analyse- en streamingverwerkingstechnologie om uw toepassing op Azure te bouwen.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 2146b1bd782aba5d98729a2d37d956744e469ba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75860245"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Kies een realtime analyse- en streamingverwerkingstechnologie op Azure

Er zijn verschillende services beschikbaar voor realtime analyse en streamingverwerking op Azure. Dit artikel biedt de informatie die u nodig hebt om te beslissen welke technologie het beste geschikt is voor uw toepassing.

## <a name="when-to-use-azure-stream-analytics"></a>Wanneer Azure Stream Analytics gebruiken

Azure Stream Analytics is de aanbevolen service voor streamanalyses op Azure. Het is bedoeld voor een breed scala aan scenario's die omvatten, maar zijn niet beperkt tot:

* Dashboards voor gegevensvisualisatie
* Real-time [waarschuwingen](stream-analytics-set-up-alerts.md) van temporele en ruimtelijke patronen of afwijkingen
* Extraction, Transformation, Loading (ETL)
* [Event Sourcing patroon](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

Het toevoegen van een Azure Stream Analytics-taak aan uw toepassing is de snelste manier om streaming-analyses in Azure te gebruiken met behulp van de SQL-taal die u al kent. Azure Stream Analytics is een jobservice, zodat u geen tijd hoeft te besteden aan het beheren van clusters en u zich geen zorgen hoeft te maken over downtime met een SLA van 99,9% op taakniveau. Facturering gebeurt ook op taakniveau waardoor de opstartkosten laag zijn (één streamingeenheid), maar schaalbaar (tot 192 streamingeenheden). Het is veel kosteneffectiever om een paar Stream Analytics-taken uit te voeren dan om een cluster uit te voeren en te onderhouden.

Azure Stream Analytics heeft een rijke out-of-the-box ervaring. U onmiddellijk profiteren van de volgende functies zonder extra setup:

* Ingebouwde tijdelijke operatoren, zoals [gevensterde aggregaten,](stream-analytics-window-functions.md)temporele joins en tijdelijke analytische functies.
* Native [Azure-invoer-](stream-analytics-add-inputs.md) en [uitvoeradapters](stream-analytics-define-outputs.md)
* Ondersteuning voor langzaam veranderende [referentiegegevens](stream-analytics-use-reference-data.md) (ook wel opzoektabellen genoemd), inclusief het samenvoegen met georuimtelijke referentiegegevens voor geofencing.
* Geïntegreerde oplossingen, zoals [anomaliedetectie](stream-analytics-machine-learning-anomaly-detection.md)
* Meerdere tijdvensters in dezelfde query
* Mogelijkheid om meerdere tijdelijke operatoren samen te stellen in willekeurige sequenties.
* Minder dan 100 ms end-to-end latentie van input die aankomt bij Event Hubs, naar de landing in Event Hubs, inclusief de netwerkvertraging van en naar Event Hubs, bij een aanhoudende hoge doorvoer

## <a name="when-to-use-other-technologies"></a>Wanneer andere technologieën gebruiken

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>UdF's, UDA's en aangepaste deserialisators wilt schrijven in een andere taal dan JavaScript of C #

Azure Stream Analytics ondersteunt door de gebruiker gedefinieerde functies (UDF) of door de gebruiker gedefinieerde aggregaten (UDA) in JavaScript voor cloudtaken en C# voor IoT Edge-taken. C# door de gebruiker gedefinieerde deserializers worden ook ondersteund. Als u een deserializer, een UDF of een UDA in andere talen, zoals Java of Python, wilt implementeren, u Spark Structured Streaming gebruiken. U de Event Hubs **EventProcessorHost** ook uitvoeren op uw eigen virtuele machines om willekeurige streamingverwerking uit te voeren.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>Uw oplossing bevindt zich in een multi-cloud- of on-premises omgeving

Azure Stream Analytics is de eigen technologie van Microsoft en is alleen beschikbaar op Azure. Als u uw oplossing wilt portable zijn in Clouds of on-premises, moet u open-source technologieën zoals Spark Structured Streaming of Storm overwegen.

## <a name="next-steps"></a>Volgende stappen

* [Een Stream Analytics-taak maken met behulp van de Azure-portal](stream-analytics-quick-create-portal.md)
* [Een Stream Analytics-taak maken met behulp van Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Een Stream Analytics-taak maken met Visual Studio](stream-analytics-quick-create-vs.md)
* [Een Stream Analytics-taak maken met Visual Studio Code](quick-create-vs-code.md)
