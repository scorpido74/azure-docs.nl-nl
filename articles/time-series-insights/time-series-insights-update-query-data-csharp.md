---
title: Gegevens opvragen uit een preview-omgeving C# met-Azure time series Insights | Microsoft Docs
description: Meer informatie over het opvragen van gegevens uit een Azure Time Series Insights omgeving met behulp van C#een app die is geschreven in.
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
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76980984"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Gegevens opvragen uit de Azure Time Series Insights voorbeeld omgeving met behulp vanC#

Dit C# voor beeld laat zien hoe u gegevens kunt opvragen uit de [Preview-Api's voor gegevens toegang](https://docs.microsoft.com/rest/api/time-series-insights/preview) in azure time series Insights-voorbeeld omgevingen.

> [!TIP]
> Voorbeeld C# code voorbeelden weer geven op [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample).

## <a name="summary"></a>Samenvatting

De voorbeeld code hieronder bevat de volgende functies:

* Ondersteuning voor het automatisch genereren van de SDK vanuit [Azure auto rest](https://github.com/Azure/AutoRest).
* Een toegangs token verkrijgen via Azure Active Directory met behulp van [micro soft. Identity model. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
* Het aangeschafte toegangs token door geven in de `Authorization` header van volgende API-aanvragen voor Data Access. 
* Het voor beeld biedt een console interface waarin wordt uitgelegd hoe HTTP-aanvragen worden gedaan:

    * [Voor beeld van omgevingen-API](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * De API voor de [Beschik baarheid van omgevingen ophalen](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) en de [Event schema-API ophalen](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [Preview-query-API](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [Get Events API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents), [Get Series API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)en [Get Total Series API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [Api's voor time series model](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * [Hiërarchieën ophalen API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get) en [hiërarchieën batch-API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch)
        * [Typen API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get) en [typen ophalen batch-API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch)
        * [Instances-API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get) en [instances-batch-API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch) ophalen
* Geavanceerde [Zoek](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features) -en [TSX](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) -mogelijkheden.

## <a name="prerequisites-and-setup"></a>Vereisten en installatie

Voer de volgende stappen uit voordat u de voorbeeld code compileert en uitvoert:

1. [Richt een preview-Azure time series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment) omgeving in.
1. Configureer uw Azure Time Series Insights-omgeving voor Azure Active Directory zoals beschreven in [verificatie en autorisatie](time-series-insights-authentication-and-authorization.md). 
1. Voer [GenerateCode. bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat) uit zoals opgegeven in de [Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md) om de time series Insights preview-client afhankelijkheden te genereren.
1. Open de `TSIPreviewDataPlaneclient.sln` oplossing en stel `DataPlaneClientSampleApp` in als het standaard project in Visual Studio.
1. Installeer de vereiste project afhankelijkheden met behulp van de stappen die [hieronder](#project-dependencies) worden beschreven en compileer het voor beeld naar een uitvoerbaar `.exe` bestand.
1. Voer het `.exe`-bestand uit door erop te dubbel klikken.

## <a name="project-dependencies"></a>Projectafhankelijkheden

Het is raadzaam om de nieuwste versie van Visual Studio te gebruiken:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -versie 16.4.2 +

De voorbeeld code bevat verschillende vereiste afhankelijkheden die kunnen worden weer gegeven in het bestand [packages. config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config) .

Down load de pakketten in Visual Studio 2019 door de optie **build** > **Build-oplossing** te selecteren. 

U kunt ook elk pakket toevoegen met behulp van [NuGet 2.12 +](https://www.nuget.org/). Bijvoorbeeld:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C#voorbeeld code

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * Het code voorbeeld kan worden uitgevoerd zonder de standaard omgevings variabelen te wijzigen.
> * Het code voorbeeld wordt gecompileerd naar een .NET-uitvoer bare console-app.

## <a name="next-steps"></a>Volgende stappen

- Lees de [query-API-verwijzing](https://docs.microsoft.com/rest/api/time-series-insights/preview-query)voor meer informatie over het uitvoeren van query's.

- Lees hoe u [met de client-SDK verbinding maakt met een Java script-app](https://github.com/microsoft/tsiclient) om te time series Insights.
