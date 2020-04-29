---
title: Gegevens opvragen uit een GA-omgeving met C#-code-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over het opvragen van gegevens uit een Azure Time Series Insights omgeving met behulp van een aangepaste app die is geschreven in C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: 754d1b80236d138693987cccee7a218ccd96b16b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81383888"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Gegevens opvragen uit de Azure Time Series Insights GA-omgeving met C #

Dit C#-voor beeld laat zien hoe u de [Ga query-api's](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) kunt gebruiken om gegevens uit Azure time series Insights ga-omgevingen op te vragen.

> [!TIP]
> GA C#-code voorbeelden weer [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample)geven op.

## <a name="summary"></a>Samenvatting

De voorbeeld code hieronder bevat de volgende functies:

* Een toegangs token verkrijgen via Azure Active Directory met behulp van [micro soft. Identity model. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Het aangeschafte toegangs token in de `Authorization` koptekst van volgende query-API-aanvragen door geven. 

* In het voor beeld wordt elk van de GA query-Api's gedemonstreerd hoe HTTP-aanvragen worden gedaan voor:
    * De [omgevings-API ophalen](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api) om de omgevingen te retour neren waartoe de gebruiker toegang heeft
    * [API voor omgevings beschikbaarheid ophalen](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * [API voor omgevings gegevens ophalen](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api) om de meta gegevens van de omgeving op te halen
    * [API voor omgevings gebeurtenissen ophalen](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [API voor samen stellen van omgeving ophalen](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* Hoe u kunt communiceren met de GA query-Api's met behulp van WSS om het volgende te doen:

   * [Gestreamde API voor de omgevings gebeurtenissen ophalen](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [Gestreamde API voor de omgeving ophalen](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Vereisten en installatie

Voer de volgende stappen uit voordat u de voorbeeld code compileert en uitvoert:

1. [Richt een GA Azure time series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started) -omgeving in.
1. Configureer uw Azure Time Series Insights-omgeving voor Azure Active Directory zoals beschreven in [verificatie en autorisatie](time-series-insights-authentication-and-authorization.md). 
1. Installeer de vereiste project afhankelijkheden.
1. Bewerk de voorbeeld code hieronder door elk **#DUMMY #** te vervangen door de juiste omgevings-id.
1. Voer de code in Visual Studio uit.

## <a name="project-dependencies"></a>Projectafhankelijkheden

Het is raadzaam om de nieuwste versie van Visual Studio te gebruiken:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -versie 16.4.2 +

De voorbeeld code bevat twee vereiste afhankelijkheden:

* Het pakket [micro soft. Identity model. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -3.13.9.
* [Newton soft. json](https://www.nuget.org/packages/Newtonsoft.Json) -9.0.1-pakket.

Down load de pakketten in Visual Studio 2019 **door de** > optie build**Build Solution** te selecteren.

U kunt de pakketten ook toevoegen met behulp van [NuGet 2.12 +](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>C#-voorbeeld code

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Volgende stappen

- Lees de [query-API-verwijzing](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)voor meer informatie over het uitvoeren van query's.

- Lees hoe u [met de client-SDK verbinding maakt met een Java script-app](https://github.com/microsoft/tsiclient) om te time series Insights.
