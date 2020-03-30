---
title: Analyse van Azure Service Fabric-gebeurtenissen met toepassingsinzichten
description: Meer informatie over het visualiseren en analyseren van gebeurtenissen met behulp van Application Insights voor monitoring en diagnostiek van Azure Service Fabric-clusters.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 5c80dc9f350fec30469a9bce2ed836c276ef3a7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464749"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Gebeurtenisanalyse en visualisatie met Application Insights

Application Insights is onderdeel van Azure Monitor en is een uitbreidbaar platform voor toepassingsbewaking en -diagnostiek. Het bevat een krachtige analyse- en querytool, aanpasbare dashboard- en visualisaties en andere opties, waaronder geautomatiseerde waarschuwingen. De integratie van Application Insights met Service Fabric omvat tooling-ervaringen voor Visual Studio en Azure-portal, evenals servicefabric-specifieke statistieken, die een uitgebreide out-of-the-box-logging-ervaring bieden. Hoewel veel logboeken automatisch voor u worden gemaakt en verzameld met Application Insights, raden we u aan om verdere aangepaste logboekregistratie toe te voegen aan uw toepassingen om een rijkere diagnostische ervaring te creëren.

In dit artikel worden de volgende veelgestelde vragen beantwoord:

* Hoe weet ik wat er gaande is in mijn applicatie en diensten en het verzamelen van telemetrie?
* Hoe los ik mijn toepassing op, met name services die met elkaar communiceren?
* Hoe krijg ik statistieken over hoe mijn services presteren, bijvoorbeeld laadtijd van pagina's, HTTP-aanvragen?

Het doel van dit artikel is om te laten zien hoe je inzichten krijgen en problemen oplossen vanuit Application Insights. Als u wilt weten hoe u Application Insights instellen en configureren met Service Fabric, raadpleegt u deze [zelfstudie.](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="monitoring-in-application-insights"></a>Monitoring in Application Insights

Application Insights heeft een rijke out-of-the-box ervaring bij het gebruik van Service Fabric. Op de overzichtspagina geeft Application Insights belangrijke informatie over uw service, zoals de reactietijd en het aantal verwerkte aanvragen. Door bovenaan op de knop Zoeken te klikken, ziet u een lijst met recente aanvragen in uw toepassing. Bovendien u hier mislukte aanvragen zien en vaststellen welke fouten mogelijk zijn opgetreden.

![Overzicht van toepassingsinzichten](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

In het rechterdeelvenster in de voorgaande afbeelding staan twee hoofdtypen vermeldingen in de lijst: aanvragen en gebeurtenissen. Verzoeken zijn oproepen naar de API van de app via HTTP-verzoeken in dit geval en gebeurtenissen zijn aangepaste gebeurtenissen, die fungeren als telemetrie die u overal in uw code toevoegen. U het instrumenteren van uw toepassingen verder verkennen in [Application Insights API voor aangepaste gebeurtenissen en statistieken.](../azure-monitor/app/api-custom-events-metrics.md) Als u op een verzoek klikt, worden verdere details weergegeven, zoals in de volgende afbeelding, inclusief gegevens die specifiek zijn voor Service Fabric, die worden verzameld in het Nuget-pakket van de Application Insights Service Fabric. Deze informatie is handig voor het oplossen van problemen en weten wat de status van uw toepassing is, en al deze informatie is doorzoekbaar binnen Application Insights

![Details van toepassingsinzichten aanvragen](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Application Insights heeft een aangewezen weergave voor het bevragen van alle gegevens die binnenkomen. Klik boven aan de pagina Overzicht op 'Metrics Explorer'. Hier u query's uitvoeren op aangepaste gebeurtenissen die eerder zijn vermeld, aanvragen, uitzonderingen, prestatiemeteritems en andere statistieken met behulp van de Kusto-querytaal. In het volgende voorbeeld worden alle aanvragen in het afgelopen 1 uur weergegeven.

![Details van toepassingsinzichten aanvragen](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Om de mogelijkheden van de Application Insights-portal verder te verkennen, gaat u naar de [portaldocumentatie van Application Insights.](../azure-monitor/app/app-insights-dashboards.md)

### <a name="configuring-application-insights-with-eventflow"></a>Toepassingsinzichten configureren met EventFlow

Als u EventFlow gebruikt om gebeurtenissen samen `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights`te voegen, moet u het NuGet-pakket importeren. De volgende code is vereist in de *uitvoersectie* van de *gebeurtenisFlowConfig.json:*

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Zorg ervoor dat u de vereiste wijzigingen in uw filters aanbrengt, evenals alle andere ingangen (samen met hun respectieve NuGet-pakketten).

## <a name="application-insights-sdk"></a>Application Insights SDK

Het wordt aanbevolen om EventFlow en WAD te gebruiken als aggregatieoplossingen, omdat ze een meer modulaire benadering van diagnostiek en monitoring mogelijk maken, d.w.z. als u uw uitgangen van EventFlow wilt wijzigen, vereist dit geen wijziging van uw werkelijke instrumentatie, slechts een eenvoudige wijziging van uw config-bestand. Als u echter besluit om te investeren in het gebruik van Application Insights en waarschijnlijk niet zal overschakelen naar een ander platform, moet u kijken naar het gebruik van de nieuwe SDK van Application Insights voor het aggregeren van gebeurtenissen en het verzenden ervan naar Application Insights. Dit betekent dat u EventFlow niet langer hoeft te configureren om uw gegevens naar Application Insights te verzenden, maar in plaats daarvan het ApplicationInsight's Service Fabric NuGet-pakket installeert. Details over het pakket vindt u [hier.](https://github.com/Microsoft/ApplicationInsights-ServiceFabric)

[Application Insights-ondersteuning voor Microservices en Containers](https://azure.microsoft.com/blog/app-insights-microservices/) toont u een aantal van de nieuwe functies waaraan wordt gewerkt (momenteel nog in bèta), waarmee u rijkere kant-en-klare bewakingsopties hebben met Application Insights. Deze omvatten afhankelijkheidstracking (gebruikt bij het bouwen van een AppMap van al uw services en toepassingen in een cluster en de communicatie tussen deze services) en een betere correlatie van traces afkomstig van uw services (helpt bij het beter lokaliseren van een probleem in de workflow van een toepassing of dienst).

Als u zich ontwikkelt in .NET en waarschijnlijk enkele programmeermodellen van Service Fabric gebruikt en bereid bent om Application Insights te gebruiken als uw platform voor het visualiseren en analyseren van gebeurtenis- en loggegevens, raden we u aan om via de Application Insights te gaan SDK-route als uw monitoring- en diagnostische workflow. Lees [dit](../azure-monitor/app/asp-net-more.md) en [dit](../azure-monitor/app/asp-net-trace-logs.md) om aan de slag te gaan met het gebruik van Application Insights om uw logboeken te verzamelen en weer te geven.

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Navigeren door de application Insights-bron in Azure-portal

Zodra u Application Insights hebt geconfigureerd als uitvoer voor uw gebeurtenissen en logboeken, moet informatie binnen enkele minuten worden weergegeven in uw Application Insights-bron. Navigeer naar de resource Application Insights, die u naar het resourcedashboard Application Insights brengt. Klik **op Zoeken** op de taakbalk Toepassingsinzichten om de nieuwste traces te zien die deze heeft ontvangen en om er doorheen te kunnen filteren.

*Metrics Explorer* is een handig hulpmiddel voor het maken van aangepaste dashboards op basis van statistieken die uw toepassingen, services en cluster mogelijk rapporteren. Zie [Statistieken verkennen in Application Insights](../azure-monitor/app/metrics-explorer.md) om een paar grafieken voor uzelf in te stellen op basis van de gegevens die u verzamelt.

Als u op **Analytics** klikt, gaat u naar de Portal Application Insights Analytics, waar u gebeurtenissen en traces met een groter bereik en mogelijkheden opvragen. Lees hier meer over bij [Analytics in Application Insights](../azure-monitor/app/analytics.md).

## <a name="next-steps"></a>Volgende stappen

* [Waarschuwingen in AI instellen](../azure-monitor/app/alerts.md) om op de hoogte te worden gesteld van wijzigingen in prestaties of gebruik
* [Smart Detection in Application Insights](../azure-monitor/app/proactive-diagnostics.md) voert een proactieve analyse uit van de telemetrie die naar Application Insights wordt verzonden om u te waarschuwen voor mogelijke prestatieproblemen
