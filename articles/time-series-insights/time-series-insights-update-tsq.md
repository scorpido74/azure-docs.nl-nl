---
title: Azure Time Series Insights preview-gegevens query's weer geven | Microsoft Docs
description: Azure Time Series Insights voorbeeld gegevens opvragen.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: 7b7d041b678ccf2a476afc6d0744f6971349677e
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034877"
---
# <a name="data-querying"></a>Query's uitvoeren op gegevens

Met Azure Time Series Insights Preview kunnen gegevens query's worden doorzocht op gebeurtenissen en meta gegevens die zijn opgeslagen in de omgeving via open bare Surface-Api's. Deze Api's worden ook gebruikt in de [Time Series Insights preview Explorer](./time-series-insights-update-explorer.md).

Er zijn drie primaire API-categorieën beschikbaar in Time Series Insights:

* **Omgevings-api's**: Hiermee schakelt u query's in van de Time Series Insights omgeving zelf. Voor beelden van query's zijn de lijst met omgevingen waarin de aanroeper toegang heeft tot de meta gegevens van de omgeving.

* **Time Series model-query (TSM-Q)-api's**: Maakt maken, lezen, bijwerken en verwijderen van meta gegevens die zijn opgeslagen in het omgevings deel van het time series-model. Voor beelden zijn instanties, typen en hiërarchieën.

* **TSQ-api's (Time Series query)** : Hiermee wordt het ophalen van gebeurtenis gegevens in de bron provider vastgelegd. Deze Api's kunnen bewerkingen uitvoeren voor het transformeren, combi neren en uitvoeren van berekeningen voor tijdreeks gegevens.

De [tijd reeks expressie (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) is een krachtige vierde categorie. Het maakt gebruik van Time Series-modellen om samen stelling van geavanceerde berekeningen mogelijk te maken.

## <a name="azure-time-series-insights-preview-core-apis"></a>Preview-kern-Api's Azure Time Series Insights

De volgende kern-Api's worden ondersteund.

[overzicht van de query ![Time reeks](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Omgevings-Api's

De volgende omgevings-Api's zijn beschikbaar:

* [Omgevings-API ophalen](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api): Retourneert de lijst met omgevingen waarvoor de aanroeper gemachtigd is om toegang te krijgen.
* [API voor omgevings beschikbaarheid ophalen](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api): Retourneert de verdeling van het aantal gebeurtenissen over het tijds tempel van de gebeurtenis `$ts`. Met deze API kunt u bepalen of er gebeurtenissen in de tijds tempel zijn door het aantal gebeurtenissen te retour neren, indien aanwezig.
* [API voor gebeurtenis schema ophalen](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api): Retourneert de meta gegevens van het gebeurtenis schema voor een opgegeven zoek reeks. Deze API helpt bij het ophalen van alle meta gegevens en eigenschappen die beschikbaar zijn in het schema voor de opgegeven zoek reeks.

## <a name="time-series-model-query-tsm-q-apis"></a>Time Series model-query (TSM-Q)-Api's

De volgende time series-model-query-Api's zijn beschikbaar:

* [API voor model instellingen](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): Hiermee wordt Get en patch voor het standaard type en de model naam van de omgeving ingeschakeld.
* [Typen-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): Hiermee schakelt u ruw in voor tijdreeks typen en de bijbehorende variabelen.
* [API voor hiërarchieën](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): Maakt ruwe op de tijdreeks hiërarchieën en de bijbehorende veld paden.
* [API voor instanties](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): Hiermee schakelt u ruw in voor de time series-instanties en de bijbehorende exemplaar velden.

## <a name="time-series-query-tsq-apis"></a>TSQ-Api's (Time Series query)

De volgende time series query-Api's zijn beschikbaar:

* [API voor gebeurtenissen ophalen](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api): Maakt het mogelijk om gegevens op te vragen en op te Time Series Insights halen uit gebeurtenissen die zijn vastgelegd in Time Series Insights van de bron provider.

* [Get-reeks-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api): Maakt query's en het ophalen van Time Series Insights gegevens van vastgelegde gebeurtenissen mogelijk met behulp van gegevens die zijn vastgelegd op de kabel. De geretourneerde waarden zijn gebaseerd op de variabelen die zijn gedefinieerd in het model of die inline zijn opgegeven.

    >[!NOTE]
    > De component Aggregation wordt genegeerd, zelfs als deze is opgegeven in een model of inline.

  De Get-reeks-API retourneert een time series-waarde voor elke variabele voor elk interval. Een time series-waarde is een indeling die Time Series Insights gebruikt voor het uitvoeren van JSON van een query. De geretourneerde waarden zijn gebaseerd op de tijd reeks-ID en de set variabelen die zijn opgegeven.

* [Aggregatie](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api)van de Series-API: Maakt het mogelijk om gegevens op te vragen en op te Time Series Insights halen uit vastgelegde gebeurtenissen door de opgenomen gegevens te combi sampling en samen te voegen.

  De verzamelings-API voor samen voegen retourneert een time series-waarde voor elke variabele voor elk interval. De waarden zijn gebaseerd op de tijd reeks-ID en de set variabelen die zijn opgegeven. De verzamelings-API voor samen voegen behaalt de reductie met behulp van variabelen die zijn opgeslagen in het time series-model of die inline zijn voor aggregatie of voorbeeld gegevens.

  Ondersteunde aggregatie typen: `Min`, `Max`, `Sum`, `Count`, `Average`

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [opslag en](./time-series-insights-update-storage-ingress.md) inkomend verkeer vindt u in de preview-versie van Azure time series Insights.

- Lees het artikel Time Series Insights preview [Data Modeler](./time-series-insights-update-tsm.md) .

- [Aanbevolen procedures detecteren bij het kiezen van een tijd reeks-id](./time-series-insights-update-how-to-id.md).
