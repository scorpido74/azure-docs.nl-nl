---
title: Querygegevens uit een preview-omgeving met C# - Azure Time Series Insights | Microsoft Documenten
description: Meer informatie over het opvragen van gegevens uit een Azure Time Series Insights-omgeving met behulp van een app die is geschreven in C#.
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
ms.openlocfilehash: 76e3ac85a6725976ebd14dac1805079613c94ec6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76980984"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Querygegevens uit de Azure Time Series Insights Preview-omgeving met C #

In dit c#-voorbeeld wordt uitgelegd hoe u gegevens opvragen via de [API's voor gegevenstoegang](https://docs.microsoft.com/rest/api/time-series-insights/preview) bekijken in Azure Time Series Insights Preview-omgevingen.

> [!TIP]
> Voorbeeld C#-codevoorbeelden [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)weergeven op .

## <a name="summary"></a>Samenvatting

De onderstaande voorbeeldcode toont de volgende kenmerken:

* Ondersteuning voor het genereren van SDK-auto's vanuit [Azure AutoRest.](https://github.com/Azure/AutoRest)
* Een toegangstoken aanschaffen via Azure Active Directory met [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
* Hoe dat verkregen toegangstoken `Authorization` door te geven in de header van volgende Api-aanvragen voor Gegevenstoegang. 
* Het voorbeeld biedt een console-interface die laat zien hoe HTTP-aanvragen worden gedaan naar:

    * [API voor voorbeeldomgevingen](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * [Beschikbaarheids-API voor omgevingen](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) en [API voor gebeurtenisschema's](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [Voorbeeld van query-API](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [Download Events API,](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents) [Get Series API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)en Download Aggregate Series [API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [Time Series Model API's](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * API en [Hierarchies Batch API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch) [oplaten](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get)
        * API en [typen batch-API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch) [oplaten](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get)
        * API en [Instances Batch API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch) [ophalen](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get)
* Geavanceerde [zoek-](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features) en [TSX-mogelijkheden.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

## <a name="prerequisites-and-setup"></a>Voorwaarden en installatie

Voer de volgende stappen uit voordat u de voorbeeldcode compileert en uitvoert:

1. [Informeer een Preview Azure Time Series Insights-omgeving.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment)
1. Configureer uw Azure Time Series Insights-omgeving voor Azure Active Directory zoals beschreven in [Verificatie en autorisatie.](time-series-insights-authentication-and-authorization.md) 
1. Voer de [GenerateCode.bat uit](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat) zoals opgegeven in de [Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md) om de clientafhankelijkheden van Time Series Insights Preview te genereren.
1. Open `TSIPreviewDataPlaneclient.sln` de oplossing `DataPlaneClientSampleApp` en stel in als het standaardproject in Visual Studio.
1. Installeer de vereiste projectafhankelijkheden met behulp van de [onderstaande](#project-dependencies) stappen en compileer het voorbeeld in een uitvoerbaar `.exe` bestand.
1. Voer `.exe` het bestand uit door erop te dubbelklikken.

## <a name="project-dependencies"></a>Projectafhankelijkheden

Het wordt aanbevolen om de nieuwste versie van Visual Studio te gebruiken:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - Versie 16.4.2+

De voorbeeldcode heeft verschillende vereiste afhankelijkheden die kunnen worden bekeken in het [bestand packages.config.](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config)

Download de pakketten in Visual Studio 2019 door de optie **Build** > **Build Solution te** selecteren. 

U ook elk pakket toevoegen met [NuGet 2.12+](https://www.nuget.org/). Bijvoorbeeld:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C#voorbeeldcode

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * Het codevoorbeeld kan worden uitgevoerd zonder de standaardomgevingsvariabelen te wijzigen.
> * Het codevoorbeeld wordt gecompileerd naar een .NET uitvoerbare console-app.

## <a name="next-steps"></a>Volgende stappen

- Lees de [query-API-verwijzing](https://docs.microsoft.com/rest/api/time-series-insights/preview-query)voor meer informatie over query's.

- Lees hoe u [een JavaScript-app aansluit met de SDK van de client](https://github.com/microsoft/tsiclient) met Time Series Insights.
