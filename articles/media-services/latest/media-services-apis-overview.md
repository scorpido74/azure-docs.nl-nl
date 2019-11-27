---
title: Ontwikkelen met v3-Api's
titleSuffix: Azure Media Services
description: Meer informatie over regels die van toepassing zijn op entiteiten en Api's bij het ontwikkelen met Media Services v3.
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
ms.openlocfilehash: 4a3b699c90e1fefb834f8ddfe3a23fc2a97354ec
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186132"
---
# <a name="develop-with-media-services-v3-apis"></a>Ontwikkelen met Media Services v3-Api's

Als een ontwikkelaar kunt u de [REST-API](https://aka.ms/ams-v3-rest-ref) van Media Services gebruiken, of clientbibliotheken waarmee u kunt communiceren met de REST-API, om eenvoudig aangepaste mediawerkstromen te maken, beheren en onderhouden. De API van [Media Services versie 3](https://aka.ms/ams-v3-rest-sdk) is gebaseerd op de OpenAPI-specificatie (voorheen bekend als een Swagger).

In dit artikel worden regels beschreven die van toepassing zijn op entiteiten en Api's bij het ontwikkelen met Media Services v3.

## <a name="accessing-the-azure-media-services-api"></a>Toegang tot de Azure Media Services-API

Als u toegang wilt hebben tot Media Services resources en de Media Services-API, moet u eerst worden geverifieerd. Media Services ondersteunt [Azure Active Directory (op Azure AD) gebaseerde](../../active-directory/fundamentals/active-directory-whatis.md) verificatie. Twee algemene verificatie opties zijn:
 
* **Service-Principal-verificatie**: wordt gebruikt voor het verifiëren van een service (bijvoorbeeld: Web apps, functie-apps, Logic apps, API en micro Services). Toepassingen die meestal gebruikmaken van deze verificatie methode zijn apps die daemon services, middelste laag Services of geplande taken uitvoeren. Voor web-apps moet er bijvoorbeeld altijd een mid-tier zijn die verbinding maakt met Media Services met een service-principal.
* **Gebruikers verificatie**: wordt gebruikt voor het verifiëren van een persoon die de app gebruikt om te communiceren met Media Services-resources. De interactieve app moet eerst de gebruiker vragen naar de referenties van de gebruiker. Een voor beeld is een beheer console-app die door geautoriseerde gebruikers wordt gebruikt voor het bewaken van coderings taken of live streamen.

De Media Services-API vereist dat de gebruiker of app die de REST API aanvragen maakt toegang tot de resource van het Media Services-account heeft en een rol voor **Inzender** of **eigenaar** kan gebruiken. De API kan worden geopend met de rol **lezer** , maar er zijn alleen **Get** -of **List** -bewerkingen beschikbaar. Zie [op rollen gebaseerd toegangs beheer voor Media Services accounts](rbac-overview.md)voor meer informatie.

In plaats van een service-principal te maken, kunt u overwegen beheerde identiteiten te gebruiken voor Azure-resources om toegang te krijgen tot de Media Services-API via Azure Resource Manager. Zie [Wat is beheerde identiteiten voor Azure](../../active-directory/managed-identities-azure-resources/overview.md)-resources voor meer informatie over beheerde identiteiten voor Azure-resources.

### <a name="azure-ad-service-principal"></a>Service-Principal van Azure AD

Als u een Azure AD-app en Service-Principal maakt, moet de app zich in een eigen Tenant bevindt. Nadat u de app hebt gemaakt, geeft u de rol app **Inzender** of **eigenaar** toegang tot het Media Services-account.

Zie [vereiste machtigingen](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)als u niet zeker weet of u gemachtigd bent om een Azure AD-app te maken.

In de volgende afbeelding vertegenwoordigen de cijfers de stroom van de aanvragen in chronologische volg orde:

![App-verificatie op middelste laag met AAD vanuit een web-API](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Een middelste laag-app vraagt een Azure AD-toegangs token met de volgende para meters:  

   * Azure AD-Tenant eindpunt.
   * Media Services resource-URI.
   * Resource-URI voor REST-Media Services.
   * Azure AD-App-waarden: de client-ID en het client geheim.

   Als u alle benodigde waarden wilt ophalen, raadpleegt u [de Access Azure Media Services-API met de Azure cli](access-api-cli-how-to.md).

2. Het Azure AD-toegangs token wordt verzonden naar de middelste laag.
4. De middelste laag verzendt een aanvraag naar de Azure media-REST API met het Azure AD-token.
5. De middelste laag krijgt een back-up van de gegevens van Media Services.

### <a name="samples"></a>Voorbeelden

Bekijk de volgende voor beelden die laten zien hoe u verbinding maakt met de Azure AD-Service-Principal:

* [Verbinding maken met REST](media-rest-apis-with-postman.md)  
* [Verbinding maken met Java](configure-connect-java-howto.md)
* [Verbinding maken met .NET](configure-connect-dotnet-howto.md)
* [Verbinding maken met Node.js](configure-connect-nodejs-howto.md)
* [Verbinding maken met Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Naamconventies

Namen van Azure Media Services v3-resources (bijvoorbeeld activa, taken, transformaties) zijn onderhevig aan de naamgevingsbeperkingen van Azure Resource Manager. In overeenstemming met Azure Resource Manager zijn de resourcenamen altijd uniek. U kunt dus alle unieke id-strings (bijvoorbeeld GUID's) gebruiken voor uw resourcenamen.

Media Services resource namen mogen niet de volgende tekens bevatten: ' < ', ' > ', '% ', ' & ', '&#92;: ', ' ', '? ', '/', ' * ', ' + ', '. ', het enkele aanhalings teken of de Stuur codes. Alle andere tekens zijn toegestaan. De maximale lengte van een resourcenaam is 260 tekens.

Zie [naamgevings vereisten](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) en [naam conventies](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)voor meer informatie over de naamgeving van Azure Resource Manager.

### <a name="names-of-filesblobs-within-an-asset"></a>Namen van bestanden/blobs in een Asset

De namen van bestanden/blobs in een Asset moeten de [vereisten voor de BLOB-naam](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) en de [NTFS-naam](https://docs.microsoft.com/windows/win32/fileio/naming-a-file)volgen. De reden voor deze vereisten is dat de bestanden kunnen worden gekopieerd van Blob Storage naar een lokale NTFS-schijf voor verwerking.

## <a name="long-running-operations"></a>Langlopende bewerkingen

De bewerkingen die zijn gemarkeerd met `x-ms-long-running-operation` in de Azure Media Services [Swagger-bestanden](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) zijn langlopende bewerkingen. 

Zie [asynchrone bewerkingen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation)voor meer informatie over het bijhouden van asynchrone Azure-bewerkingen.

Media Services heeft de volgende langlopende bewerkingen:

* [Live-gebeurtenissen maken](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Live-gebeurtenissen bijwerken](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [Live-gebeurtenis verwijderen](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Live-gebeurtenis starten](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [LiveEvent stoppen](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  Gebruik de para meter `removeOutputsOnStop` om alle gekoppelde live-uitvoer te verwijderen wanneer de gebeurtenis wordt gestopt.  
* [LiveEvent opnieuw instellen](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [LiveOutput maken](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [LiveOutput verwijderen](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [StreamingEndpoint maken](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [StreamingEndpoint bijwerken](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [StreamingEndpoint verwijderen](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [StreamingEndpoint starten](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [StreamingEndpoint stoppen](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [StreamingEndpoint schalen](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

Wanneer een lange bewerking is ingediend, ontvangt u een ' 202 accepted ' en moet u de bewerking volt ooien met de geretourneerde bewerkings-ID.

In het artikel [asynchrone Azure-bewerkingen bijhouden](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) wordt uitgelegd hoe u de status van asynchrone bewerkingen van Azure kunt volgen met de waarden die in het antwoord worden geretourneerd.

Er wordt slechts één langlopende bewerking ondersteund voor een bepaalde live gebeurtenis of een van de bijbehorende live-uitvoer bewerkingen. Eenmaal gestart, moet een langlopende bewerking worden voltooid voordat een volgende langlopende bewerking wordt gestart op dezelfde LiveEvent of aan de gekoppelde live uitvoer. Voor Live-gebeurtenissen met meerdere Live outputs moet u wachten op het volt ooien van een langlopende bewerking op één live uitvoer voordat een langlopende bewerking wordt geactiveerd op een andere live uitvoer. 

## <a name="sdks"></a>SDK's

> [!NOTE]
> De Azure Media Services v3 Sdk's zijn niet gegarandeerd thread-safe. Wanneer u een app met meerdere threads ontwikkelt, moet u uw eigen thread synchronisatie logica toevoegen om de client te beveiligen of een nieuw AzureMediaServicesClient-object per thread te gebruiken. Wees ook voorzichtig met het oplossen van problemen met meerdere threads die worden geïntroduceerd door de optionele objecten die door uw code worden verstrekt aan de client (zoals een httpclient maakt-exemplaar in .NET).

|SDK|Naslaginformatie|
|---|---|
|[.NET-SDK](https://aka.ms/ams-v3-dotnet-sdk)|[.NET-ref](https://aka.ms/ams-v3-dotnet-ref)|
|[Java-SDK](https://aka.ms/ams-v3-java-sdk)|[Java-ref](https://aka.ms/ams-v3-java-ref)|
|[Python-SDK](https://aka.ms/ams-v3-python-sdk)|[Python-ref](https://aka.ms/ams-v3-python-ref)|
|[Node.js-SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Node.js-ref](/javascript/api/overview/azure/mediaservices/management)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Go-ref](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Zie ook

- [EventGrid .NET SDK die media service-gebeurtenissen bevat](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definities van Media Services gebeurtenissen](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) is een hulpprogramma beschikbaar voor Windows-klanten die kennis willen maken met Media Services. AMSE is een Winforms/C#-toepassing voor het uploaden, downloaden, coderen en streamen van VOD en live-inhoud met Media Services. Het AMSE-hulpprogramma is voor clients die Media Services willen testen zonder code te schrijven. De AMSE-code wordt geleverd als een bron voor klanten die willen ontwikkelen met Media Services.

AMSE is een Open-Source-project, en dus wordt er ondersteuning geboden door de community (problemen kunnen worden gemeld aan https://github.com/Azure/Azure-Media-Services-Explorer/issues). Op dit project is de [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) (Microsoft Open Source-gedragscode) van toepassing. Voor meer informatie raadpleegt u de [Veelgestelde vragen over de gedrags code](https://opensource.microsoft.com/codeofconduct/faq/) of neemt u contact op met opencode@microsoft.com met andere vragen of opmerkingen.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filteren, ordenen, paginering van Media Services entiteiten

Zie [filteren, ordenen, pagineren van Azure Media Services entiteiten](entities-overview.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Bekijk het [Azure Media Services Community](media-services-community.md) -artikel voor verschillende manieren om vragen te stellen, feedback te geven en updates te ontvangen over Media Services.

## <a name="see-also"></a>Zie ook

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met Media Services met Java](configure-connect-java-howto.md)
* [Verbinding maken met Media Services met .NET](configure-connect-dotnet-howto.md)
* [Verbinding maken met Media Services met behulp van node. js](configure-connect-nodejs-howto.md)
* [Verbinding maken met Media Services met python](configure-connect-python-howto.md)
