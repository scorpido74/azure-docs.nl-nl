---
title: Gegevens uit een GA-omgeving opvragen met C#-code - Azure Time Series Insights | Microsoft Documenten
description: Meer informatie over het opvragen van gegevens uit een Azure Time Series Insights-omgeving met behulp van een aangepaste app die is geschreven in C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 9f7819974e3548baf5e10f0bf9a2d656d9412beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987968"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Querygegevens uit de GA-omgeving met Inzichten in Azure Time Series met C #

In dit c#-voorbeeld wordt uitgelegd hoe u de [GA-query-API's](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) gebruiken om gegevens uit GA-omgevingen met azure-time-reeksen te bevragen.

> [!TIP]
> Bekijk GA C# [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample)code samples op .

## <a name="summary"></a>Samenvatting

De onderstaande voorbeeldcode toont de volgende kenmerken:

* Een toegangstoken aanschaffen via Azure Active Directory met [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Hoe dat verkregen toegangstoken `Authorization` door te geven in de header van volgende Query API-aanvragen. 

* De voorbeeldaanroepen elk van de GA Query API's laten zien hoe HTTP-aanvragen worden gedaan om de:
    * [Get Environments API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api) to return the environments the user has access to
    * [API voor beschikbaarheid van omgeving en beschikbaarheid](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * [Api voor omgevingmetagegevens](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api) ophalen om metagegevens van omgevingop te halen
    * [API voor gebeurtenissen voor omgevingen](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [API voor omgevingsaggregaten verzamelen](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* Hoe te communiceren met de GA Query API's met behulp van WSS om een bericht:

   * [Gestreamde API voor omgevingsgebeurtenissen](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [Gestreamde API voor omgevingsaggregaten](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Voorwaarden en installatie

Voer de volgende stappen uit voordat u de voorbeeldcode compileert en uitvoert:

1. [Inrichten van een GA Azure Time Series Insights-omgeving.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)
1. Configureer uw Azure Time Series Insights-omgeving voor Azure Active Directory zoals beschreven in [Verificatie en autorisatie.](time-series-insights-authentication-and-authorization.md) 
1. Installeer de vereiste projectafhankelijkheden.
1. Bewerk de onderstaande voorbeeldcode door elke **#DUMMY#** te vervangen door de juiste omgevings-id.
1. Voer de code uit in Visual Studio.

## <a name="project-dependencies"></a>Projectafhankelijkheden

Het wordt aanbevolen om de nieuwste versie van Visual Studio te gebruiken:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - Versie 16.4.2+

De voorbeeldcode heeft twee vereiste afhankelijkheden:

* [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) - 3.13.9-pakket.
* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) - 9.0.1 pakket.

Download de pakketten in Visual Studio 2019 door de optie **Build** > **Build Solution te** selecteren.

U ook de pakketten toevoegen met [NuGet 2.12+](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>C#voorbeeldcode

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Volgende stappen

- Lees de [query-API-verwijzing](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)voor meer informatie over query's.

- Lees hoe u [een JavaScript-app aansluit met de SDK van de client](https://github.com/microsoft/tsiclient) met Time Series Insights.
