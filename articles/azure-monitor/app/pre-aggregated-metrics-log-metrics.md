---
title: Op logboek gebaseerde en vooraf geaggregeerde metrische gegevens in Azure-toepassing inzichten | Microsoft Docs
description: Waarom het gebruik van op logboek gebaseerd versus vooraf geaggregeerde metrische gegevens in Azure-toepassing inzichten
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 384e3c9032b324ee92762db9156c628a05e5e862
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75406596"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Vooraf samengevoegde metrische gegevens op basis van logboeken in Application Insights

In dit artikel wordt uitgelegd wat het verschil is tussen ' traditionele ' Application Insights metrische gegevens die zijn gebaseerd op Logboeken en vooraf geaggregeerde metrische gegevens die momenteel beschikbaar zijn in de open bare preview. Beide soorten metrische gegevens zijn beschikbaar voor de gebruikers van Application Insights en elk een unieke waarde voor het controleren van de status van toepassingen, diagnostische gegevens en analyses. De ontwikkel aars die toepassingen maken, kunnen bepalen welk type metrische gegevens het meest geschikt zijn voor een bepaald scenario, afhankelijk van de grootte van de toepassing, het verwachte volume van de telemetrie en zakelijke vereisten voor de precisie van metrische gegevens en waarschuwingen.

## <a name="log-based-metrics"></a>Metrische gegevens op basis van een logboek

Tot die tijd is de toepassing voor het telemetrie-gegevens model in Application Insights alleen gebaseerd op een klein aantal vooraf gedefinieerde typen gebeurtenissen, zoals aanvragen, uitzonde ringen, afhankelijkheids aanroepen, pagina weergaven, enzovoort. Ontwikkel aars kunnen de SDK gebruiken om deze gebeurtenissen hand matig te verzenden (door code te schrijven die de SDK expliciet aanroept) of ze kunnen vertrouwen op het automatisch verzamelen van gebeurtenissen van automatische instrumentatie. In beide gevallen slaat de back-end van de Application Insights alle verzamelde gebeurtenissen op als logboeken en de Application Insights-blades in de Azure Portal fungeren als een analytisch en diagnostisch hulp programma voor het visualiseren van op gebeurtenissen gebaseerde gegevens uit Logboeken.

Het gebruik van Logboeken voor het bewaren van een complete set gebeurtenissen kan een uitstekende analytische en diagnose waarde bieden. U kunt bijvoorbeeld een exacte telling van aanvragen naar een bepaalde URL verkrijgen met het aantal afzonderlijke gebruikers dat deze aanroepen heeft gedaan. U kunt ook gedetailleerde diagnostische traceringen ophalen, inclusief uitzonde ringen en afhankelijkheids aanroepen voor elke gebruikers sessie. Met dit type informatie kan de zicht baarheid van de toepassings status en het gebruik aanzienlijk worden verbeterd, waardoor de tijd die nodig is om de oorzaak van problemen met een app op te sporen, te verminderen. 

Op hetzelfde moment kan het verzamelen van een complete set gebeurtenissen niet praktisch zijn (of zelfs onmogelijk) voor toepassingen die veel telemetrie genereren. Voor situaties waarin het volume van gebeurtenissen te hoog is, Application Insights de implementatie van verschillende telemetrie-volume reductie technieken, zoals [steek proeven](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) en [filters](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) , waarmee het aantal verzamelde en opgeslagen gebeurtenissen wordt verminderd. Het verlagen van het aantal opgeslagen gebeurtenissen vermindert ook de nauw keurigheid van de metrische gegevens die, achter de schermen, query's moeten uitvoeren op de gebeurtenissen die in Logboeken zijn opgeslagen.

> [!NOTE]
> In Application Insights worden de metrische gegevens die zijn gebaseerd op de query-tijd aggregatie van gebeurtenissen en metingen die in Logboeken zijn opgeslagen, metrische gegevens op basis van een logboek genoemd. Deze metrische gegevens hebben doorgaans veel dimensies van de gebeurtenis eigenschappen, waardoor ze voor analyses worden gemaakt, maar de nauw keurigheid van deze metrische gegevens wordt negatief beïnvloed door steek proeven en filters.

## <a name="pre-aggregated-metrics"></a>Vooraf samengestelde metrische gegevens

Naast de metrische gegevens op basis 2018 van een logboek, heeft het Application Insights team een open bare preview van metrische gegevens ontvangen die zijn opgeslagen in een gespecialiseerde opslag plaats die is geoptimaliseerd voor tijd reeksen. De nieuwe metrische gegevens worden niet langer bewaard als afzonderlijke gebeurtenissen met een groot aantal eigenschappen. In plaats daarvan worden ze opgeslagen als vooraf geaggregeerde time series en alleen met belang rijke dimensies. Dit zorgt ervoor dat de nieuwe metrische gegevens zich op het moment van de query kunnen voordoen: het ophaal proces verloopt veel sneller en vereist minder reken kracht. Hierdoor kunnen nieuwe scenario's, zoals [bijna realtime waarschuwingen over dimensies van metrische gegevens](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts), snellere [Dash boards](https://docs.microsoft.com/azure/azure-monitor/app/overview-dashboard)en meer, worden ingeschakeld.

> [!IMPORTANT]
> Beide, op logboek gebaseerde en vooraf geaggregeerde metrische gegevens bestaan naast elkaar in Application Insights. Om de twee te onderscheiden, worden in de Application Insights UX de vooraf samengestelde metrische gegevens nu ' standaard meet waarden (preview) ' genoemd, terwijl de traditionele metrische gegevens van de gebeurtenissen zijn gewijzigd in metrische gegevens op basis van een logboek.

De nieuwere Sdk's ([Application Insights 2,7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK of hoger voor .net) vooraf samengestelde metrische gegevens tijdens de verzameling voordat de reductie technieken van het telemetrie-volume worden gestart. Dit betekent dat de nauw keurigheid van de nieuwe metrische gegevens niet wordt beïnvloed door steek proeven en filters bij het gebruik van de meest recente Application Insights Sdk's.

Voor de Sdk's die geen vooraf aggregatie implementeren (dat wil zeggen, oudere versies van Application Insights Sdk's of voor browser instrumentatie), vult de Application Insights back-end nog steeds de nieuwe metrische gegevens in door het samen voegen van de gebeurtenissen die door de toepassing worden ontvangen Het eind punt voor het verzamelen van Insights-gebeurtenissen. Dit betekent dat u niet profiteert van de gereduceerde hoeveelheid gegevens die via de kabel wordt verzonden, maar u kunt nog steeds gebruikmaken van de vooraf samengestelde metrieken en betere prestaties en ondersteuning bieden voor de nabije real-time driedimensionale waarschuwingen met Sdk's die niet statistische metrische gegevens tijdens het verzamelen.

Het is belang rijk dat het verzamelings eindpunt gebeurtenissen vóór opname samenvoegt, wat betekent dat de [steek proeven voor opname](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) nooit van invloed zijn op de nauw keurigheid van vooraf geaggregeerde metrische gegevens, ongeacht de SDK-versie die u met uw toepassing gebruikt.  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Vooraf aggregatie gebruiken met Application Insights aangepaste metrische gegevens

U kunt vooraf aggregatie met aangepaste metrische gegevens gebruiken. De twee belangrijkste voor delen zijn de mogelijkheid om een dimensie van een aangepaste metriek te configureren en te waarschuwen en de hoeveelheid gegevens die vanuit de SDK wordt verzonden naar het eind punt van de Application Insights verzameling te verminderen.

Er zijn verschillende [manieren om aangepaste metrische gegevens van de Application INSIGHTS SDK te verzenden](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Als uw versie van de SDK de [GetMetric-en TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) -methoden biedt, is dit de voorkeurs methode voor het verzenden van aangepaste metrische gegevens, omdat in dit geval vooraf aggregatie plaatsvindt binnen de SDK, niet alleen het volume van de in azure opgestelde Data, maar ook het volume van de gegevens die van de sdk naar Application Insights worden verzonden. Als dat niet het geval is, gebruikt u de methode [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) , waarmee metrische gebeurtenissen vooraf worden geaggregeerd tijdens gegevens opname.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Aangepaste dimensies voor metrische gegevens en vooraf aggregatie

Alle metrische gegevens die u verzendt met behulp van [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) -of [GetMetric-en TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) -API-aanroepen, worden automatisch opgeslagen in Logboeken en metrische archieven. Terwijl de op Logboeken gebaseerde versie van uw aangepaste metrische gegevens altijd alle dimensies behoudt, wordt de vooraf geaggregeerde versie van de metriek standaard zonder dimensies opgeslagen. U kunt het verzamelen van dimensies met aangepaste metrische gegevens inschakelen op het tabblad [gebruik en geschatte kosten](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) door het selectie vakje waarschuwingen op aangepaste metrische dimensies inschakelen in te scha kelen. 

![Gebruik en geraamde kosten](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Waarom is het verzamelen van dimensies met aangepaste metrische gegevens standaard uitgeschakeld?

Het verzamelen van dimensies met aangepaste metrische gegevens is standaard uitgeschakeld omdat in de toekomst aangepaste metrische gegevens worden opgeslagen met dimensies die afzonderlijk van Application Insights worden gefactureerd, terwijl de opslag van de niet-dimensionale aangepaste metrische gegevens gratis blijft (tot een quotum) . Op onze officiële [prijs pagina](https://azure.microsoft.com/pricing/details/monitor/)vindt u meer informatie over de aanstaande wijzigingen in het prijs model.

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Grafieken maken en op logboek gebaseerde en standaard vooraf samengestelde metrische gegevens verkennen

Gebruik [Azure Monitor Metrics Explorer](../platform/metrics-getting-started.md) voor het uitzetten van grafieken van vooraf geaggregeerde en op Logboeken gebaseerde metrische gegevens en om Dash boards te ontwerpen met grafieken. Nadat u de gewenste Application Insights resource hebt geselecteerd, gebruikt u de naam ruimte kiezer om te scha kelen tussen Standard (preview) en metrische gegevens op basis van een logboek of selecteert u een aangepaste metrische naam ruimte:

![Metrische naamruimte](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="next-steps"></a>Volgende stappen

* [Bijna realtime waarschuwingen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)
* [GetMetric en TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric)
