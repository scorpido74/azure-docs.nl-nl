---
title: Gegevens query's in Preview-Azure Time Series Insights | Microsoft Docs
description: Het overzicht van gegevens query's en HTTP-REST API in Azure Time Series Insights preview-versie.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/25/2020
ms.custom: seodec18
ms.openlocfilehash: 23094ec71dac5780def10e16b90de0b818ef3c68
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80284888"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Gegevens query's in Azure Time Series Insights preview

Azure Time Series Insights maakt gegevens query's op gebeurtenissen en meta gegevens die zijn opgeslagen in de omgeving via open bare Surface-Api's. Deze Api's worden ook gebruikt door de [Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).

Er zijn drie primaire API-categorieën beschikbaar in Time Series Insights:

* **Omgevings-api's**: met deze api's worden query's op de time series Insights omgeving zelf ingeschakeld. Deze kunnen worden gebruikt voor het verzamelen van de lijst met omgevingen waartoe de aanroeper toegang heeft en de meta gegevens van de omgeving.
* **Time Series model-query-api's (TSM-Q)**: maakt maken, lezen, bijwerken en verwijderen van meta gegevens die zijn opgeslagen in het tijdreeks model van de omgeving. Deze kunnen worden gebruikt om de instanties, typen en hiërarchieën te openen en te bewerken.
* **Api's voor time series query (TSQ)**: Hiermee wordt het ophalen van gegevens over telemetrie of gebeurtenissen mogelijk gemaakt wanneer deze worden vastgelegd van de bron provider en kunnen er berekeningen en aggregaties worden uitgevoerd op de gegevens met behulp van geavanceerde scalaire en statistische functies.

Time Series Insights maakt gebruik van een uitgebreide op teken reeks gebaseerde expressie taal, [Time Series-expressie (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax), voor het uitdrukken van berekeningen.

## <a name="azure-time-series-insights-core-apis"></a>Azure Time Series Insights core-Api's

De volgende kern-Api's worden ondersteund.

[![Overzicht van de time series-query](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Omgevings-Api's

* [Omgevings-API ophalen](https://docs.microsoft.com/rest/api/time-series-insights/management/environments/get): retourneert de lijst met omgevingen waarvoor de aanroeper gemachtigd is om toegang te krijgen.
* [Beschikbaarheids-API voor omgevingen ophalen](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability): retourneert de verdeling van het aantal gebeurtenissen over het tijds tempel van de gebeurtenis `$ts` . Met deze API kunt u bepalen of er gebeurtenissen in de omgeving zijn door het aantal gebeurtenissen te retour neren dat is opgesplitst in tijds intervallen, indien aanwezig.
* [Event schema-API ophalen](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema): retourneert de meta gegevens van het gebeurtenis schema voor een opgegeven zoek reeks. Deze API helpt bij het ophalen van alle meta gegevens en eigenschappen die beschikbaar zijn in het schema voor de opgegeven zoek reeks.

## <a name="time-series-model-query-tsm-q-apis"></a>Time Series model-query (TSM-Q)-Api's

De meeste van deze Api's ondersteunen de batch-uitvoerings bewerking om batch-ruwe bewerkingen in te scha kelen op meerdere time series-model entiteiten:

* [API voor model instellingen](https://docs.microsoft.com/rest/api/time-series-insights/preview#model-settings-api): maakt *Get* en *patch* mogelijk voor het standaard type en de model naam van de omgeving.
* [Type-API](https://docs.microsoft.com/rest/api/time-series-insights/preview#types-api): Hiermee worden ruwe serie typen en de bijbehorende variabelen ingeschakeld.
* [Hiërarchieën API](https://docs.microsoft.com/rest/api/time-series-insights/preview#hierarchies-api): maakt ruwe op de tijdreeks hiërarchieën en de bijbehorende veld paden.
* [Instances-API](https://docs.microsoft.com/rest/api/time-series-insights/preview#instances-api): maakt ruwe op de tijd reeks instanties en de bijbehorende exemplaar velden. Daarnaast ondersteunt de instances-API de volgende bewerkingen:
  * [Search](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search): haalt een gedeeltelijke lijst met treffers op bij het zoeken naar Time Series-exemplaren op basis van instantie kenmerken.
  * [Suggereren](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest): zoekt naar een gedeeltelijke lijst met treffers op basis van instantie kenmerken en stelt suggesties op voor het zoeken naar Time Series-exemplaren.

## <a name="time-series-query-tsq-apis"></a>TSQ-Api's (Time Series query)

Deze Api's zijn beschikbaar in alle winkels in onze oplossing voor opslag op meerdere lagen in Time Series Insights. Query-URL-para meters worden gebruikt om het [Store-type](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) op te geven waarop de query moet worden uitgevoerd:

* [Get Events API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents): maakt het mogelijk om de query op te halen en het ophalen van onbewerkte gebeurtenissen en de bijbehorende gebeurtenis tijds tempels, zoals deze worden vastgelegd in time series Insights van de bron provider. Met deze API kunt u onbewerkte gebeurtenissen ophalen voor een bepaalde tijd reeks-ID en zoek reeks. Deze API biedt ondersteuning voor paginering voor het ophalen van de volledige reactie gegevensset voor de geselecteerde invoer. 

* [Get Series API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries): maakt het mogelijk om query's en het ophalen van berekende waarden en de bijbehorende gebeurtenis tijds tempels in te stellen door berekeningen toe te passen die worden gedefinieerd door variabelen op onbewerkte gebeurtenissen. Deze variabelen kunnen worden gedefinieerd in het time series-model of in inline van de query. Deze API biedt ondersteuning voor paginering voor het ophalen van de volledige reactie gegevensset voor de geselecteerde invoer. 

* [Aggregatie](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)van de-serie: maakt het mogelijk om query's en het ophalen van geaggregeerde waarden en de bijbehorende interval tijds tempels in te stellen door berekeningen toe te passen die worden gedefinieerd door variabelen op onbewerkte gebeurtenissen. Deze variabelen kunnen worden gedefinieerd in het time series-model of in inline van de query. Deze API biedt ondersteuning voor paginering voor het ophalen van de volledige reactie gegevensset voor de geselecteerde invoer. 
  
  Voor een opgegeven zoek reeks en-interval retourneert deze API een geaggregeerde respons per variabele per interval voor een tijd reeks-ID. Het aantal intervallen in de reactie gegevensset wordt berekend door epoche Ticks te tellen (het aantal milliseconden dat is verstreken sinds UNIX-epoche 1 januari, 1970) en de maat streepjes te delen door de grootte van het interval dat is opgegeven in de query.

  De tijds tempels die zijn geretourneerd in de antwoordset, zijn de grenzen van het linker interval, niet van de voor beelden van gebeurtenissen uit het interval. 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over verschillende variabelen die kunnen worden gedefinieerd in het [Time Series-model](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm).
- Meer informatie over het opvragen van gegevens uit de [Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).
