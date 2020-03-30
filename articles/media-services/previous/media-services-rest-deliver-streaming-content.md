---
title: Azure Media Services-inhoud publiceren met REST
description: Meer informatie over het maken van een locator die wordt gebruikt om een streaming-URL op te bouwen. De code maakt gebruik van REST API.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: ff332c30-30c6-4ed1-99d0-5fffd25d4f23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 787336f00a83d9403e3069754787743b9be6c5b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77050009"
---
# <a name="publish-azure-media-services-content-using-rest"></a>Azure Media Services-inhoud publiceren met REST 
> [!div class="op_single_selector"]
> * [.NET](media-services-deliver-streaming-content.md)
> * [Rest](media-services-rest-deliver-streaming-content.md)
> * [Portal](media-services-portal-publish.md)
> 
> 

U een adaptieve bitrate MP4-set streamen door een OnDemand-streaminglocator te maken en een streaming-URL te bouwen. Het [coderingsartikel laat](media-services-rest-encode-asset.md) zien hoe u coderen in een adaptieve bitrate MP4-set. Als uw inhoud is versleuteld, configureert u het beleid voor het leveren van activa (zoals beschreven in [dit](media-services-rest-configure-asset-delivery-policy.md) artikel) voordat u een locator maakt. 

U ook een OnDemand-streaminglocator gebruiken om URL's te bouwen die wijzen op MP4-bestanden die geleidelijk kunnen worden gedownload.  

In dit artikel ziet u hoe u een OnDemand-streaminglocator maakt om uw asset te publiceren en een vloeiend, MPEG DASH- en HLS-streaming-URL's te maken. Het laat ook zien hoe je progressieve download URL's maken.

In de [volgende](#types) sectie worden de enumtypen weergegeven waarvan de waarden worden gebruikt in de REST-aanroepen.   

> [!NOTE]
> Wanneer u toegang krijgt tot entiteiten in Media Services, moet u specifieke koptekstvelden en -waarden instellen in uw HTTP-aanvragen. Zie [Setup for Media Services REST API Development voor](media-services-rest-how-to-use.md)meer informatie.
> 

## <a name="connect-to-media-services"></a>Verbinding met Media Services maken

Zie [Toegang tot de Azure Media Services API met Azure AD-verificatie](media-services-use-aad-auth-to-access-ams-api.md)voor informatie over hoe u verbinding maken met de AMS-API. 

>[!NOTE]
>Nadat u https://media.windows.netmet succes verbinding hebt gemaakt met , ontvangt u een 301 redirect met een andere Media Services URI. U moet volgende oproepen naar de nieuwe URI.

## <a name="create-an-ondemand-streaming-locator"></a>Een OnDemand streaming locator maken
Als u de OnDemand-streaminglocator wilt maken en URL's wilt krijgen, moet u het volgende doen:

1. Als de inhoud is versleuteld, definieert u een toegangsbeleid.
2. Maak een OnDemand streaming locator.
3. Als u van plan bent te streamen, krijgt u het streaming manifestbestand (.ism) in het item. 
   
   Als u van plan bent om geleidelijk te downloaden, krijgt u de namen van MP4-bestanden in het item. 
4. Maak URL's voor het manifestbestand of MP4-bestanden. 
5. U geen streaminglocator maken met behulp van een AccessPolicy dat schrijf- of verwijdermachtigingen bevat.

### <a name="create-an-access-policy"></a>Een toegangsbeleid maken

>[!NOTE]
>Er geldt een limiet van 1.000.000 beleidsregels voor verschillende AMS-beleidsitems (bijvoorbeeld voor Locator-beleid of ContentKeyAuthorizationPolicy). Gebruik dezelfde beleids-ID als u altijd dezelfde dagen / toegangsmachtigingen gebruikt, bijvoorbeeld beleid voor locators die bedoeld zijn om lang te blijven bestaan (niet-uploadbeleid). Zie voor meer informatie [dit](media-services-dotnet-manage-entities.md#limit-access-policies) artikel.

Aanvraag:

    POST https://media.windows.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
    Host: media.windows.net
    Content-Length: 68

    {"Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}

Reactie:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 311
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https:/media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3A69c80d98-7830-407f-a9af-e25f4b0d3e5f')
    Server: Microsoft-IIS/8.5
    request-id: a877528a-bdb4-4414-9862-273f8e64f882
    x-ms-request-id: a877528a-bdb4-4414-9862-273f8e64f882
    x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 18 Feb 2015 06:52:09 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#AccessPolicies/@Element","Id":"nb:pid:UUID:69c80d98-7830-407f-a9af-e25f4b0d3e5f","Created":"2015-02-18T06:52:09.8862191Z","LastModified":"2015-02-18T06:52:09.8862191Z","Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}

### <a name="create-an-ondemand-streaming-locator"></a>Een OnDemand streaming locator maken
Maak de locator voor het opgegeven asset- en assetbeleid.

Aanvraag:

    POST https://media.windows.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
    Host: media.windows.net
    Content-Length: 181

    {"AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872Z","Type":2}

Reactie:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 637
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Abe245661-2bbd-4fc6-b14f-9cf9a1492e5e')
    Server: Microsoft-IIS/8.5
    request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
    x-ms-request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
    x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 18 Feb 2015 06:58:37 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#Locators/@Element","Id":"nb:lid:UUID:be245661-2bbd-4fc6-b14f-9cf9a1492e5e","ExpirationDateTime":"2015-03-20T06:34:47.267872+00:00","Type":2,"Path":"https://amstest1.streaming.mediaservices.windows.net/be245661-2bbd-4fc6-b14f-9cf9a1492e5e/","BaseUri":"https://amstest1.streaming.mediaservices.windows.net","ContentAccessComponent":"be245661-2bbd-4fc6-b14f-9cf9a1492e5e","AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872+00:00","Name":null}

### <a name="build-streaming-urls"></a>Streaming-URL's maken
Gebruik de **padwaarde** die is geretourneerd na het maken van de locator om de URL's Smooth, HLS en MPEG DASH te bouwen. 

Smooth Streaming: **Pad** + manifest bestandsnaam + "/manifest"

voorbeeld:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest

HLS: **Pad** + manifestbestandsnaam + "/manifest(format=m3u8-aapl)"

voorbeeld:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)


DASH: **Path** + manifest bestandsnaam + "/manifest(format=mpd-time-csf)"

voorbeeld:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


### <a name="build-progressive-download-urls"></a>Progressieve download-URL's maken
Gebruik de **padwaarde** die is geretourneerd na het maken van de locator om de progressieve download-URL te bouwen.   

URL: Mp4-naam **pad** + assetbestand

voorbeeld:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

## <a name="enum-types"></a><a id="types"></a>Enum-typen
    [Flags]
    public enum AccessPermissions
    {
        None = 0,
        Read = 1,
        Write = 2,
        Delete = 4,
        List = 8,
    }

    public enum LocatorType
    {
        None = 0,
        Sas = 1,
        OnDemandOrigin = 2,
    }

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook
[Overzicht van REST API-beheer van Media Services](media-services-rest-how-to-use.md)

[Beleid voor het leveren van activa configureren](media-services-rest-configure-asset-delivery-policy.md)

