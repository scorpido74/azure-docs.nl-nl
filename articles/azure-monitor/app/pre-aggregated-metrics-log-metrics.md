---
title: Op logboeken gebaseerde en vooraf geaggregeerde statistieken in Azure Application Insights | Microsoft Documenten
description: Waarom log-based versus vooraf geaggregeerde statistieken gebruiken in Azure Application Insights
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 30487eebed361e5b010df023a9b1a44f96590b14
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271077"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Vooraf samengevoegde metrische gegevens op basis van logboeken in Application Insights

In dit artikel wordt het verschil uitgelegd tussen 'traditionele' Application Insights-statistieken die zijn gebaseerd op logboeken en vooraf geaggregeerde statistieken die momenteel in openbare preview staan. Beide typen statistieken zijn beschikbaar voor de gebruikers van Application Insights en elk brengt een unieke waarde in het bewaken van applicatiestatus, diagnostiek en analyses. De ontwikkelaars die toepassingen instrumenteren, kunnen bepalen welk type statistiek het meest geschikt is voor een bepaald scenario, afhankelijk van de grootte van de toepassing, het verwachte volume telemetrie en zakelijke vereisten voor precisie en waarschuwingen voor metrische gegevens.

## <a name="log-based-metrics"></a>Statistieken op basis van logboeken

Tot voor kort was het telemetriegegevensmodel voor het bewaken van toepassingen in Application Insights uitsluitend gebaseerd op een klein aantal vooraf gedefinieerde typen gebeurtenissen, zoals aanvragen, uitzonderingen, afhankelijkheidsoproepen, paginaweergaven, enz. Ontwikkelaars kunnen de SDK gebruiken om deze gebeurtenissen handmatig uit te zenden (door code te schrijven die expliciet een beroep doet op de SDK) of ze kunnen vertrouwen op de automatische verzameling gebeurtenissen van automatische instrumentatie. In beide gevallen worden in de backend van Application Insights alle verzamelde gebeurtenissen opgeslagen als logboeken en fungeren de toepassingsbladen van De Inzichten in de Azure-portal als een analytisch en diagnostisch hulpmiddel voor het visualiseren van gebeurtenisgebaseerde gegevens uit logboeken.

Het gebruik van logboeken om een complete set gebeurtenissen te behouden kan grote analytische en diagnostische waarde opleveren. U bijvoorbeeld een exact aantal aanvragen voor een bepaalde URL krijgen met het aantal verschillende gebruikers dat deze oproepen heeft uitgevoerd. U ook gedetailleerde diagnostische sporen krijgen, inclusief uitzonderingen en afhankelijkheidsoproepen voor elke gebruikerssessie. Het hebben van dit soort informatie kan de zichtbaarheid van de toepassingsstatus en het gebruik aanzienlijk verbeteren, waardoor de tijd die nodig is om problemen met een app te diagnosticeren, kan worden verkort.

Tegelijkertijd kan het verzamelen van een complete set gebeurtenissen onpraktisch (of zelfs onmogelijk) zijn voor toepassingen die een groot volume telemetrie genereren. Voor situaties waarin het volume van gebeurtenissen te hoog is, implementeert Application Insights verschillende telemetrievolumereductietechnieken, zoals [bemonstering](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) en [filtering](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) die het aantal verzamelde en opgeslagen gebeurtenissen verminderen. Helaas verlaagt het verlagen van het aantal opgeslagen gebeurtenissen ook de nauwkeurigheid van de statistieken die achter de schermen query-time-aggregaties van de gebeurtenissen moeten uitvoeren die in logboeken zijn opgeslagen.

> [!NOTE]
> In Application Insights worden de statistieken die zijn gebaseerd op de querytijdaggregatie van gebeurtenissen en metingen die in logboeken zijn opgeslagen, op logboeken gebaseerde statistieken genoemd. Deze statistieken hebben doorgaans veel dimensies van de gebeurtenis-eigenschappen, waardoor ze superieur zijn voor analyses, maar de nauwkeurigheid van deze statistieken wordt negatief beïnvloed door steekproeven en filtering.

## <a name="pre-aggregated-metrics"></a>Vooraf geaggregeerde statistieken

Naast op logboeken gebaseerde statistieken, heeft het Team Application Insights eind 2018 een openbare preview verzonden van statistieken die zijn opgeslagen in een gespecialiseerde opslagplaats die is geoptimaliseerd voor tijdreeksen. De nieuwe statistieken worden niet langer bijgehouden als afzonderlijke gebeurtenissen met veel eigenschappen. In plaats daarvan worden ze opgeslagen als vooraf geaggregeerde tijdreeksen, en alleen met belangrijke dimensies. Dit maakt de nieuwe metrics superieur op querytijd: het ophalen van gegevens gaat veel sneller en vereist minder rekenkracht. Dit maakt het mogelijk nieuwe scenario's, zoals [near real-time alerting op dimensies van statistieken,](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)meer responsieve [dashboards](https://docs.microsoft.com/azure/azure-monitor/app/overview-dashboard)en meer.

> [!IMPORTANT]
> Beide, log-based en vooraf geaggregeerde metrics naast elkaar bestaan in Application Insights. Om de twee te differentiëren, worden in de APPLICATION Insights UX de vooraf geaggregeerde statistieken nu "Standard metrics (preview)" genoemd, terwijl de traditionele statistieken van de gebeurtenissen werden omgedoopt tot 'Log-based metrics'.

De nieuwere SDK's[(Application Insights 2.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK of hoger voor .NET) pre-aggregaat metrics tijdens het verzamelen voordat telemetrie volume reductie technieken kick in. Dit betekent dat de nauwkeurigheid van de nieuwe statistieken niet wordt beïnvloed door bemonstering en filtering bij het gebruik van de nieuwste Application Insights SDKs.

Voor de SDK's die geen pre-aggregatie implementeren (dat wil zeggen oudere versies van Application Insights SDKs of voor browserinstrumentatie) vult de backend van Application Insights nog steeds de nieuwe statistieken door de gebeurtenissen die zijn ontvangen door het eindpunt van de Application Insights-gebeurtenisverzameling te aggregeren. Dit betekent dat u weliswaar niet profiteert van het verminderde volume van gegevens dat via de draad wordt verzonden, maar dat u nog steeds de vooraf geaggregeerde statistieken gebruiken en betere prestaties en ondersteuning ervaren van de bijna realtime dimensionale waarschuwingen met SDK's die geen vooraf geaggregeerde statistieken tijdens het verzamelen gebruiken.

Het is vermeldenswaard dat het verzameleindpunt gebeurtenissen vooraf verzamelt vóór opnamebemonstering, wat betekent dat [innamebemonstering](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) nooit van invloed zal zijn op de nauwkeurigheid van vooraf geaggregeerde statistieken, ongeacht de SDK-versie die u met uw toepassing gebruikt.  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Pre-aggregatie gebruiken met aangepaste statistieken van Application Insights

U pre-aggregatie gebruiken met aangepaste statistieken. De twee belangrijkste voordelen zijn de mogelijkheid om te configureren en te waarschuwen op een dimensie van een aangepaste statistiek en het verminderen van het volume van de gegevens die van de SDK naar het eindpunt van de Application Insights-verzameling worden verzonden.

Er zijn verschillende [manieren om aangepaste statistieken te verzenden vanuit de Application Insights SDK.](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) Als uw versie van de SDK de [Methoden GetMetric en TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) biedt, is dit de voorkeursmanier om aangepaste statistieken te verzenden, omdat in dit geval pre-aggregatie plaatsvindt binnen de SDK, waarbij niet alleen het volume van gegevens die in Azure is opgeslagen, wordt verminderd, maar ook het volume van gegevens die van de SDK naar Application Insights worden verzonden. Gebruik anders de [trackMetric-methode,](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) die metrische gebeurtenissen vooraf samenvoegt tijdens het innemen van gegevens.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Aangepaste metrische gegevens afmetingen en pre-aggregatie

Alle statistieken die u verzendt met behulp van [trackMetric-](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) of [GetMetric- en TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) API-aanroepen worden automatisch opgeslagen in zowel logboeken als metrische gegevens. Hoewel de op logboeken gebaseerde versie van uw aangepaste statistiek altijd alle dimensies behoudt, wordt de vooraf geaggregeerde versie van de statistiek standaard opgeslagen zonder afmetingen. U verzameling dimensies van aangepaste statistieken inschakelen op het tabblad [gebruik en geschatte kosten](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) door 'Waarschuwing inschakelen voor aangepaste metrische dimensies inschakelen': 

![Gebruik en geschatte kosten](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Waarom is het verzamelen van aangepaste metrische dimensies standaard uitgeschakeld?

Het verzamelen van aangepaste metrische gegevens wordt standaard uitgeschakeld, omdat in de toekomst het opslaan van aangepaste statistieken met afmetingen afzonderlijk wordt gefactureerd van Application Insights, terwijl het opslaan van de niet-dimensionale aangepaste statistieken gratis blijft (tot een quotum). Op onze officiële [prijspagina](https://azure.microsoft.com/pricing/details/monitor/)vindt u meer informatie over de komende prijswijzigingen.

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Grafieken maken en log-gebaseerde en standaard vooraf geaggregeerde statistieken verkennen

Gebruik [Azure Monitor Metrics Explorer](../platform/metrics-getting-started.md) om grafieken uit vooraf geaggregeerde en op logboeken gebaseerde statistieken te plotten en dashboards met grafieken te maken. Nadat u de gewenste Application Insights-bron hebt geselecteerd, gebruikt u de naamruimtekiezer om te schakelen tussen standaardstatistieken (voorbeeld) en op logboeken gebaseerde statistieken, of selecteert u een aangepaste metrische naamruimte:

![Metrische naamruimte](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="pricing-models-for-application-insights-metrics"></a>Prijsmodellen voor Application Insights-statistieken

Door statistieken in te nemen in Application Insights, of het nu op logboeken is gebaseerd of vooraf is samengevoegd, worden kosten gegenereerd op basis van de grootte van de opgenomen gegevens, zoals [hier](https://docs.microsoft.com/azure/azure-monitor/app/pricing#pricing-model)beschreven. Uw aangepaste statistieken, inclusief alle afmetingen, worden altijd opgeslagen in de logboekopslag Application Insights; Bovendien wordt standaard een vooraf geaggregeerde versie van uw aangepaste statistieken (zonder afmetingen) doorgestuurd naar het waardenarchief.

Als [u](#custom-metrics-dimensions-and-pre-aggregation) de optie Waarschuwing inschakelen voor aangepaste metrische dimensies selecteert om alle dimensies van de vooraf samengevoegde metrische gegevens op te slaan in het metrische archief, kunnen **extra** kosten worden gegenereerd op basis van de prijzen van aangepaste [metrische gegevens](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="next-steps"></a>Volgende stappen

* [Bijna real-time waarschuwingen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)
* [GetMetric en TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric)