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
ms.openlocfilehash: 0b2f89a846747eaf78c60077372b48802506731e
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553381"
---
# <a name="data-querying"></a>Query's uitvoeren op gegevens

Met Azure Time Series Insights Preview kunnen gegevens query's worden doorzocht op gebeurtenissen en meta gegevens die zijn opgeslagen in de omgeving via open bare Surface-Api's. Deze Api's worden ook gebruikt in de [Time Series Insights preview Explorer](./time-series-insights-update-explorer.md).

Er zijn drie primaire API-categorieën beschikbaar in Time Series Insights:

* **Omgevings-api's**: Hiermee worden query's van de time series Insights omgeving zelf ingeschakeld. Voor beelden van query's zijn de lijst met omgevingen waarin de aanroeper toegang heeft tot de meta gegevens van de omgeving.

* **Time Series model-query (TSM-Q) api's**: maakt maken, lezen, bijwerken en verwijderen van meta gegevens die zijn opgeslagen in het omgevings deel van het time series-model. Voor beelden zijn instanties, typen en hiërarchieën.

* **Api's voor time series query (TSQ)** : Hiermee wordt het ophalen van gebeurtenis gegevens mogelijk gemaakt, terwijl deze worden vastgelegd van de bron provider. Deze Api's kunnen bewerkingen uitvoeren voor het transformeren, combi neren en uitvoeren van berekeningen voor tijdreeks gegevens.

De [tijd reeks expressie (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) is een krachtige vierde categorie. Het maakt gebruik van Time Series-modellen om samen stelling van geavanceerde berekeningen mogelijk te maken.

## <a name="azure-time-series-insights-preview-core-apis"></a>Preview-kern-Api's Azure Time Series Insights

De volgende kern-Api's worden ondersteund.

[overzicht van de query ![Time serie](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Omgevings-Api's

De volgende omgevings-Api's zijn beschikbaar:

* [Omgevings-API ophalen](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api): retourneert de lijst met omgevingen waarvoor de aanroeper gemachtigd is om toegang te krijgen.
* [API voor omgevings beschikbaarheid ophalen](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api): retourneert de verdeling van het aantal gebeurtenissen over de tijds tempel van de gebeurtenis `$ts`. Met deze API kunt u bepalen of er gebeurtenissen in de tijds tempel zijn door het aantal gebeurtenissen te retour neren, indien aanwezig.
* [Event schema-API ophalen](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api): retourneert de meta gegevens van het gebeurtenis schema voor een opgegeven zoek reeks. Deze API helpt bij het ophalen van alle meta gegevens en eigenschappen die beschikbaar zijn in het schema voor de opgegeven zoek reeks.

## <a name="time-series-model-query-tsm-q-apis"></a>Time Series model-query (TSM-Q)-Api's

De volgende time series-model-query-Api's zijn beschikbaar:

* [API voor model instellingen](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): maakt Get en patch mogelijk voor het standaard type en de model naam van de omgeving.
* [Type-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): Hiermee worden ruwe serie typen en de bijbehorende variabelen ingeschakeld.
* [Hiërarchieën API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): maakt ruwe op de tijdreeks hiërarchieën en de bijbehorende veld paden.
* [Instances-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): maakt ruwe op de tijd reeks instanties en de bijbehorende exemplaar velden.

## <a name="time-series-query-tsq-apis"></a>TSQ-Api's (Time Series query)

De volgende time series query-Api's zijn beschikbaar:

* [Get Events API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api): maakt het mogelijk om gegevens op te vragen en op te time series Insights halen uit gebeurtenissen die zijn vastgelegd in time series Insights van de bron provider.

* [Get Series API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api): maakt het mogelijk om gegevens op te vragen en op te time series Insights halen uit vastgelegde gebeurtenissen met behulp van gegevens die zijn vastgelegd op de kabel. De geretourneerde waarden zijn gebaseerd op de variabelen die zijn gedefinieerd in het model of die inline zijn opgegeven.

    >[!NOTE]
    > De component Aggregation wordt genegeerd, zelfs als deze is opgegeven in een model of inline.

  De Get-reeks-API retourneert een time series-waarde voor elke variabele voor elk interval. Een time series-waarde is een indeling die Time Series Insights gebruikt voor het uitvoeren van JSON van een query. De geretourneerde waarden zijn gebaseerd op de tijd reeks-ID en de set variabelen die zijn opgegeven.

* [Aggregatie-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api): maakt het mogelijk om gegevens op te vragen en op te time series Insights halen uit vastgelegde gebeurtenissen door steek proeven te nemen en opgenomen gegevens samen te voegen.

  De verzamelings-API voor samen voegen retourneert een time series-waarde voor elke variabele voor elk interval. De waarden zijn gebaseerd op de tijd reeks-ID en de set variabelen die zijn opgegeven. De verzamelings-API voor samen voegen behaalt de reductie met behulp van variabelen die zijn opgeslagen in het time series-model of die inline zijn voor aggregatie of voorbeeld gegevens.

  Ondersteunde statistische typen: `Min`, `Max`, `Sum`, `Count`, `Average`

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [opslag en](./time-series-insights-update-storage-ingress.md) inkomend verkeer vindt u in de preview-versie van Azure time series Insights.

- Lees het artikel Time Series Insights preview [Data Modeler](./time-series-insights-update-tsm.md) .

- [Aanbevolen procedures detecteren bij het kiezen van een tijd reeks-id](./time-series-insights-update-how-to-id.md).
