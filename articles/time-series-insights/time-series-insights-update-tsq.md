---
title: Gegevens query's in Preview-Azure Time Series Insights | Microsoft Docs
description: Het overzicht van gegevens query's en HTTP-REST API in Azure Time Series Insights preview-versie.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/16/2019
ms.custom: seodec18
ms.openlocfilehash: 53693c64ff03146619e17b276a1f817e69f5ed6e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75374575"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Gegevens query's in Azure Time Series Insights preview

Met Azure Time Series Insights Preview kunnen gegevens query's worden doorzocht op gebeurtenissen en meta gegevens die zijn opgeslagen in de omgeving via open bare Surface-Api's. Deze Api's worden ook gebruikt in de [Time Series Insights preview Explorer](./time-series-insights-update-explorer.md).

Er zijn drie primaire API-categorieën beschikbaar in Time Series Insights:

* **Omgevings-api's**: met deze api's worden query's op de time series Insights omgeving zelf ingeschakeld. Voor beelden van query's zijn de lijst met omgevingen waarin de aanroeper toegang heeft tot de meta gegevens van de omgeving.
* **Time Series model-query (TSM-Q) api's**: maakt maken, lezen, bijwerken en verwijderen van meta gegevens die zijn opgeslagen in het omgevings deel van het time series-model. Voor beelden zijn instanties, typen en hiërarchieën.
* **TSQ-api's (Time Series query)** : Hiermee wordt het ophalen van telemetriegegevens of gebeurtenis gegevens mogelijk gemaakt wanneer deze worden vastgelegd van de bron provider of door het verminderen van de gegevens met behulp van scalaire en statistische functies die deel uitmaken van variabelen. Deze Api's kunnen bewerkingen uitvoeren om berekeningen voor tijdreeks gegevens te transformeren, te combi neren en toe te passen.

Time Series Insights maakt gebruik van een uitgebreide op teken reeks gebaseerde expressie taal, [Time Series-expressie (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx), voor het uitdrukken van berekeningen.

## <a name="azure-time-series-insights-preview-core-apis"></a>Preview-kern-Api's Azure Time Series Insights

De volgende kern-Api's worden ondersteund.

[overzicht van ![time series-query](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Omgevings-Api's

De volgende omgevings-Api's zijn beschikbaar:

* [Omgevings-API ophalen](/rest/api/time-series-insights/management/environments/get): retourneert de lijst met omgevingen waarvoor de aanroeper gemachtigd is om toegang te krijgen.
* [Beschikbaarheids-API voor omgevingen ophalen](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability): retourneert de verdeling van het aantal gebeurtenissen over de tijds tempel van de gebeurtenis `$ts`. Met deze API kunt u bepalen of er gebeurtenissen in de tijds tempel zijn door het aantal gebeurtenissen te retour neren, indien aanwezig.
* [Event schema-API ophalen](/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema): retourneert de meta gegevens van het gebeurtenis schema voor een opgegeven zoek reeks. Deze API helpt bij het ophalen van alle meta gegevens en eigenschappen die beschikbaar zijn in het schema voor de opgegeven zoek reeks.

## <a name="time-series-model-query-tsm-q-apis"></a>Time Series model-query (TSM-Q)-Api's

De volgende time series-model-query-Api's zijn beschikbaar. De meeste van deze Api's ondersteunen de batch-uitvoerings bewerking om batch-ruwe bewerkingen in te scha kelen op meerdere time series-model entiteiten:

* [API voor model instellingen](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): maakt *Get* en *patch* mogelijk voor het standaard type en de model naam van de omgeving.
* [Type-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): Hiermee worden ruwe serie typen en de bijbehorende variabelen ingeschakeld.
* [Hiërarchieën API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): maakt ruwe op de tijdreeks hiërarchieën en de bijbehorende veld paden.
* [Instances-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): maakt ruwe op de tijd reeks instanties en de bijbehorende exemplaar velden. Daarnaast ondersteunt de instances-API de volgende bewerkingen:
  * [Search](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search): haalt een gedeeltelijke lijst met treffers op bij het zoeken naar Time Series-exemplaren op basis van instantie kenmerken.
  * [Suggereren](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest): zoekt naar een gedeeltelijke lijst met treffers op basis van instantie kenmerken en stelt suggesties op voor het zoeken naar Time Series-exemplaren.

## <a name="time-series-query-tsq-apis"></a>TSQ-Api's (Time Series query)

De volgende time series query-Api's zijn beschikbaar. Deze Api's zijn beschikbaar op alle ondersteunde meerdere lagen opslag in Time Series Insights. Query-URL-para meters worden gebruikt om het [Store-type](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) op te geven waarop de query moet worden uitgevoerd:

* [Get Events API](/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents): maakt het mogelijk om gegevens op te vragen en op te time series Insights halen uit gebeurtenissen die zijn vastgelegd in time series Insights van de bron provider. Met deze API kunt u onbewerkte gebeurtenissen ophalen voor een bepaalde tijd reeks-ID en zoek reeks. Deze API ondersteunt paginering om de volledige gegevensset voor de geselecteerde invoer op te halen. 

* [Get Series API](/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries): maakt het mogelijk om gegevens op te vragen en op te time series Insights halen uit vastgelegde gebeurtenissen met behulp van gegevens die zijn vastgelegd op de kabel. De geretourneerde waarden zijn gebaseerd op de variabelen die zijn gedefinieerd in het model of die inline zijn opgegeven. Deze API ondersteunt paginering om de volledige gegevensset voor de geselecteerde invoer op te halen. Deze API helpt bij het definiëren van berekende eigenschappen of kolommen.

    >[!NOTE]
    > De component Aggregation wordt genegeerd, zelfs als deze is opgegeven in een model of inline.

  De Get-reeks-API retourneert een time series-waarde voor elke variabele voor elk interval. Een time series-waarde is een indeling die Time Series Insights gebruikt voor het uitvoeren van JSON van een query. De geretourneerde waarden zijn gebaseerd op de tijd reeks-ID en de set variabelen die zijn opgegeven.

* [Aggregatie-API](/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregatevariable): maakt het mogelijk om gegevens op te vragen en op te time series Insights halen uit vastgelegde gebeurtenissen door steek proeven te nemen en opgenomen gegevens samen te voegen. Deze API ondersteunt continue uitvoering met behulp van [vervolg tokens](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#queryresultpage).

  De verzamelings-API voor samen voegen retourneert een time series-waarde voor elke variabele voor elk interval. De waarden zijn gebaseerd op de tijd reeks-ID en de set variabelen die zijn opgegeven. De verzamelings-API voor samen voegen behaalt de reductie met behulp van variabelen die zijn opgeslagen in het time series-model of die inline zijn voor aggregatie of voorbeeld gegevens.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [opslag en](./time-series-insights-update-storage-ingress.md) inkomend verkeer in azure time series Insights preview.
- Lees het artikel Time Series Insights preview [Data Modeler](./time-series-insights-update-tsm.md) .
- [Aanbevolen procedures voor het kiezen van een tijd reeks-id](./time-series-insights-update-how-to-id.md)ontdekken.
