---
title: Beheer van mediadiensten met RUST | Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe u Media Services-entiteiten beheert met REST API.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 95262a32-0f2a-4286-b9e2-1a1ca6399b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: a03bc24b689df342be40536c26149a7611fc5176
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283314"
---
# <a name="managing-media-services-entities-with-rest"></a>Media Services-entiteiten beheren met REST  

> [!div class="op_single_selector"]
> * [Rest](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

Microsoft Azure Media Services is een restservice die is gebaseerd op OData v3. U entiteiten op vrijwel dezelfde manier toevoegen, opvragen, bijwerken en verwijderen als op elke andere OData-service. Uitzonderingen worden indien van toepassing genoemd. Zie Documentatie van het [Open Data Protocol](https://www.odata.org/documentation/)voor meer informatie over OData.

In dit onderwerp ziet u hoe u Azure Media Services-entiteiten beheert met REST.

>[!NOTE]
> Vanaf 1 april 2017 wordt elke taakrecord in uw account die ouder is dan 90 dagen, automatisch verwijderd, samen met de bijbehorende onderliggende taakrecords, zelfs als het totale aantal records lager is dan het maximale quotum. Op 1 april 2017 wordt bijvoorbeeld elke vacature in uw account ouder dan 31 december 2016 automatisch verwijderd. Als u de taak-/taakgegevens wilt archiveren, u de in dit onderwerp beschreven code gebruiken.

## <a name="considerations"></a>Overwegingen  

Wanneer u toegang krijgt tot entiteiten in Media Services, moet u specifieke koptekstvelden en -waarden instellen in uw HTTP-aanvragen. Zie [Setup for Media Services REST API Development voor](media-services-rest-how-to-use.md)meer informatie.

## <a name="connect-to-media-services"></a>Verbinding met Media Services maken

Zie [Toegang tot de Azure Media Services API met Azure AD-verificatie](media-services-use-aad-auth-to-access-ams-api.md)voor informatie over hoe u verbinding maken met de AMS-API. 

## <a name="adding-entities"></a>Entiteiten toevoegen
Elke entiteit in Media Services wordt via een HTTP-verzoek voor berichten toegevoegd aan een entiteitsset, zoals Assets.

In het volgende voorbeeld ziet u hoe u een accesspolicy maakt.

    POST https://media.windows.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

## <a name="querying-entities"></a>Entiteiten opvragen
Het opvragen en aanbieden van entiteiten is eenvoudig en omvat alleen een GET HTTP-aanvraag en optionele OData-bewerkingen.
In het volgende voorbeeld wordt een lijst met alle MediaProcessor-entiteiten opgehaald.

    GET https://media.windows.net/API/MediaProcessors HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

U ook een specifieke entiteit of alle entiteitssets ophalen die zijn gekoppeld aan een specifieke entiteit, zoals in de volgende voorbeelden:

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c')/TaskTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

In het volgende voorbeeld wordt alleen de eigenschap Status van alle taken geretourneerd.

    GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

In het volgende voorbeeld worden alle JobTemplates geretourneerd met de naam 'SampleTemplate'.

    GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

> [!NOTE]
> De $expand-bewerking wordt niet ondersteund in Media Services en de niet-ondersteunde LINQ-methoden die worden beschreven in LINQ Considerations (WCF Data Services).
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>Opsommen door middel van grote collecties van entiteiten
Bij het opvragen van entiteiten is er een limiet van 1000 entiteiten die tegelijk worden geretourneerd omdat openbare REST v2 queryresultaten beperkt tot 1000 resultaten. Gebruik **overslaan** en **top** om de grote verzameling entiteiten op te sommen. 

In het volgende voorbeeld ziet u hoe **u overslaan** en **top** gebruiken om de eerste 2000 banen over te slaan en de volgende 1000 banen te krijgen.  

    GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net

## <a name="updating-entities"></a>Entiteiten bijwerken
Afhankelijk van het entiteitstype en de status waarin deze zich bevindt, u de eigenschappen van die entiteit bijwerken via een HTTP-aanvraag patch, put of merge. Zie [PATCH/PUT/MERGE](https://msdn.microsoft.com/library/dd541276.aspx)voor meer informatie over deze bewerkingen.

In het volgende codevoorbeeld ziet u hoe u de eigenschap Name op een entiteit Asset bijwerkt.

    MERGE https://media.windows.net/API/Assets('nb:cid:UUID:80782407-3f87-4e60-a43e-5e4454232f60') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 21
    Expect: 100-continue

    {"Name" : "NewName" }

## <a name="deleting-entities"></a>Entiteiten verwijderen
Entiteiten kunnen worden verwijderd in Media Services met behulp van een HTTP-verzoek verwijderen. Afhankelijk van de entiteit kan de volgorde waarin u entiteiten verwijdert, belangrijk zijn. Entiteiten zoals assets vereisen bijvoorbeeld dat u alle locators die verwijzen naar dat specifieke actief intrekt (of verwijdert) voordat u het actief verwijdert.

In het volgende voorbeeld ziet u hoe u een locator verwijdert die is gebruikt om een bestand te uploaden naar blobopslag.

    DELETE https://media.windows.net/API/Locators('nb:lid:UUID:76dcc8e8-4230-463d-97b0-ce25c41b5c8d') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 0

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

