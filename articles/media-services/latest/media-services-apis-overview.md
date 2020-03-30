---
title: Ontwikkelen met v3 API's
titleSuffix: Azure Media Services
description: Meer informatie over regels die van toepassing zijn op entiteiten en API's bij het ontwikkelen met Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: eacdfe8211c97e75b6609f5e11b681f84ae55846
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472081"
---
# <a name="develop-with-media-services-v3-apis"></a>Ontwikkelen met Media Services v3 API's

Als een ontwikkelaar kunt u de [REST-API](https://docs.microsoft.com/rest/api/media/) van Media Services gebruiken, of clientbibliotheken waarmee u kunt communiceren met de REST-API, om eenvoudig aangepaste mediawerkstromen te maken, beheren en onderhouden. De [Media Services v3](https://aka.ms/ams-v3-rest-sdk) API is gebaseerd op de OpenAPI-specificatie (voorheen bekend als een Swagger).

In dit artikel worden regels besproken die van toepassing zijn op entiteiten en API's wanneer u zich ontwikkelt met Media Services v3.

## <a name="accessing-the-azure-media-services-api"></a>Toegang tot de API voor Azure Media Services

Als u toestemming wilt krijgen om toegang te krijgen tot Media Services-bronnen en de API voor Media Services, moet u eerst worden geverifieerd. Media Services ondersteunt [Azure Active Directory (Azure AD)-gebaseerde](../../active-directory/fundamentals/active-directory-whatis.md) verificatie. Twee veelvoorkomende verificatieopties zijn:
 
* **Servicehoofdverificatie**: wordt gebruikt om een service te verifiëren (bijvoorbeeld: web-apps, functie-apps, logische apps, API en microservices). Toepassingen die deze verificatiemethode vaak gebruiken, zijn apps die daemonservices, services op het middenniveau of geplande taken uitvoeren. Voor web-apps moet er bijvoorbeeld altijd een mid-tier zijn die verbinding maakt met Media Services met een Service Principal.
* **Gebruikersverificatie:** wordt gebruikt om een persoon te verifiëren die de app gebruikt om te communiceren met mediaservicesbronnen. De interactieve app moet eerst de gebruiker om de referenties van de gebruiker vragen. Een voorbeeld is een beheerconsole-app die door geautoriseerde gebruikers wordt gebruikt om coderingstaken of live streaming te controleren.

De Media Services API vereist dat de gebruiker of app die de REST API-aanvragen maakt toegang heeft tot de Media Services-accountbron en een **rol inzender** of **eigenaar** gebruikt. De API is toegankelijk met de **readerrol,** maar alleen **get-** of **lijstbewerkingen** zijn beschikbaar.Zie [Op rollen gebaseerd toegangscontrole voor Media Services-accounts voor](rbac-overview.md)meer informatie .

In plaats van een serviceprincipal te maken, u overwegen beheerde identiteiten te gebruiken voor Azure-bronnen om toegang te krijgen tot de Media Services-API via Azure Resource Manager. Zie [Wat wordt beheerd identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten voor Azure-bronnen.

### <a name="azure-ad-service-principal"></a>Azure AD-serviceprincipal

Als u een Azure AD-app en serviceprincipal maakt, moet de app in de eigen tenant staan. Nadat u de app hebt gemaakt, geeft u de rol inzender van de app **inzender** of **eigenaar** toegang tot het Media Services-account.

Zie [Vereiste machtigingen](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)als u niet zeker weet of u machtigingen hebt om een Azure AD-app te maken.

In de volgende afbeelding geven de getallen de stroom van de aanvragen in chronologische volgorde weer:

![Verificatie van apps op het middenniveau met AAD vanuit een web-API](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Een app op de middelste laag vraagt een Azure AD-toegangstoken met de volgende parameters:  

   * Eindpunt azure AD-tenant.
   * Media Services resource URI.
   * Resource URI voor REST Media Services.
   * Azure AD-appwaarden: de client-id en het clientgeheim.

   Zie Access Azure Media [Services API met de Azure CLI](access-api-cli-how-to.md)voor alle benodigde waarden.

2. Het Azure AD-toegangstoken wordt naar de middelste laag verzonden.
4. De middelste laag verzendt aanvraag naar de Azure Media REST API met het Azure AD-token.
5. De middelste laag krijgt de gegevens terug van Media Services.

### <a name="samples"></a>Voorbeelden

Bekijk de volgende voorbeelden die laten zien hoe u verbinding maken met de principal van azure AD-service:

* [Maak verbinding met REST](media-rest-apis-with-postman.md)  
* [Verbinding maken met Java](configure-connect-java-howto.md)
* [Verbinding maken met .NET](configure-connect-dotnet-howto.md)
* [Verbinding maken met Node.js](configure-connect-nodejs-howto.md)
* [Verbinding maken met Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Naamconventies

Namen van Azure Media Services v3-resources (bijvoorbeeld activa, taken, transformaties) zijn onderhevig aan de naamgevingsbeperkingen van Azure Resource Manager. In overeenstemming met Azure Resource Manager zijn de resourcenamen altijd uniek. U kunt dus alle unieke id-strings (bijvoorbeeld GUID's) gebruiken voor uw resourcenamen.

Media Services-resourcenamen kunnen niet bestaan uit: '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', het enkele aanhalingsteken of een controleteken. Alle andere tekens zijn toegestaan. De maximale lengte van een resourcenaam is 260 tekens.

Zie [Naamgevingsvereisten](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) en [Naamgevingsconventies](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)voor meer informatie over de naamgeving van Azure Resource Manager .

### <a name="names-of-filesblobs-within-an-asset"></a>Namen van bestanden/blobs binnen een asset

De namen van bestanden/blobs binnen een asset moeten zowel de [vereisten voor blobnamen](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) als de [NTFS-naamvereisten](https://docs.microsoft.com/windows/win32/fileio/naming-a-file)volgen. De reden voor deze vereisten is dat de bestanden kunnen worden gekopieerd van blob-opslag naar een lokale NTFS-schijf voor verwerking.

## <a name="long-running-operations"></a>Langlopende operaties

De bewerkingen `x-ms-long-running-operation` die zijn gemarkeerd met de [braniebestanden van](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) Azure Media Services, zijn langlopende bewerkingen. 

Zie [Async-bewerkingen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation)voor meer informatie over het bijhouden van asynchrone Azure-bewerkingen.

Media Services heeft de volgende langlopende bewerkingen:

* [Live-evenementen maken](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Live-evenementen bijwerken](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [Live-evenement verwijderen](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Live-evenement starten](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [LiveEvent stoppen](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  Gebruik `removeOutputsOnStop` de parameter om alle bijbehorende Live-uitvoer te verwijderen bij het stoppen van de gebeurtenis.  
* [LiveEvent opnieuw instellen](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [LiveOutput maken](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [LiveOutput verwijderen](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [StreamingEndpoint maken](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [StreamingEndpoint bijwerken](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [StreamingEndpoint verwijderen](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [StreamingEndpoint starten](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [StreamingEndpoint stoppen](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [Streamingeindpunt schalen](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

Bij het succesvol indienen van een lange bewerking ontvangt u een '202 Accepted' en moet u een poll voor voltooiing van de bewerking met behulp van de geretourneerde bewerkings-ID.

In het artikel [over asynchrone Azure-bewerkingen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) wordt uitgebreid uitgelegd hoe u de status van asynchrone Azure-bewerkingen bijhouden via waarden die in het antwoord worden geretourneerd.

Er wordt slechts één langdurige bewerking ondersteund voor een bepaald Live Event of een van de bijbehorende Live-uitvoer. Eenmaal gestart, moet een langlopende bewerking worden voltooid voordat een volgende langlopende bewerking wordt gestart op dezelfde LiveEvent of een bijbehorende Live Output. Voor Live-gebeurtenissen met meerdere live-uitvoer moet u wachten op de voltooiing van een langdurige bewerking op één live-uitvoer voordat u een langdurige bewerking op een andere live-uitvoer activeert. 

## <a name="sdks"></a>SDK's

> [!NOTE]
> De Azure Media Services v3 SDK's zijn niet gegarandeerd thread-safe. Wanneer u een multithread-app ontwikkelt, moet u uw eigen threadsynchronisatielogica toevoegen om de client te beschermen of een nieuw AzureMediaServicesClient-object per thread te gebruiken. U moet ook voorzichtig zijn met multi-threading problemen geïntroduceerd door optionele objecten die door uw code aan de client (zoals een HttpClient-instantie in .NET).

|SDK|Naslaginformatie|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[.NET-ref](https://aka.ms/ams-v3-dotnet-ref)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Java-ref](https://aka.ms/ams-v3-java-ref)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Python-ref](https://aka.ms/ams-v3-python-ref)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Node.js-ref](/javascript/api/overview/azure/mediaservices/management)| 
|[Go-SDK](https://aka.ms/ams-v3-go-sdk) |[Go-ref](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Zie ook

- [EventGrid .NET SDK met gebeurtenissen in Media Service](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definities van gebeurtenissen in Media Services](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) is een hulpprogramma beschikbaar voor Windows-klanten die kennis willen maken met Media Services. AMSE is een Winforms/C#-toepassing voor het uploaden, downloaden, coderen en streamen van VOD en live-inhoud met Media Services. Het AMSE-hulpprogramma is voor clients die Media Services willen testen zonder code te schrijven. De AMSE-code wordt geleverd als een bron voor klanten die willen ontwikkelen met Media Services.

AMSE is een Open-Source-project, en dus wordt er ondersteuning geboden door de community (problemen kunnen worden gemeld aan https://github.com/Azure/Azure-Media-Services-Explorer/issues). Op dit project is de [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) (Microsoft Open Source-gedragscode) van toepassing. Zie voor meer informatie de [veelgestelde vragen over](https://opensource.microsoft.com/codeofconduct/faq/) de gedragscode of neem contact op opencode@microsoft.com met andere vragen of opmerkingen.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filteren, bestellen, paging van Media Services entiteiten

Zie [Entiteiten filteren, bestellen en paging van Azure Media Services](entities-overview.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Stel vragen, geef feedback, ontvang updates

Bekijk het communityartikel [van Azure Media Services](media-services-community.md) om verschillende manieren te zien waarop u vragen stellen, feedback geven en updates ontvangen over Media Services.

## <a name="see-also"></a>Zie ook

[Azure-CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met Media Services met Java](configure-connect-java-howto.md)
* [Verbinding maken met Media Services met .NET](configure-connect-dotnet-howto.md)
* [Verbinding maken met Media Services met Node.js](configure-connect-nodejs-howto.md)
* [Verbinding maken met Media Services met Python](configure-connect-python-howto.md)
