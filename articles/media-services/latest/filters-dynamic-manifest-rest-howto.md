---
title: Filters maken met Azure Media Services v3 REST API
description: In dit onderwerp wordt beschreven hoe u filters maakt, zodat uw client deze kan gebruiken om specifieke secties van een stream te streamen. Media Services creëert dynamische manifesten om deze selectieve streaming te bereiken.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: f9134dd3bc926e6e2f454e5187e03365e91ed22a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780331"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Filters maken met Media Services REST API

Wanneer uw klant uw inhoud aan klanten levert (live-gebeurtenissen streamen of Video on Demand) heeft uw klant mogelijk meer flexibiliteit nodig dan wat wordt beschreven in het manifestbestand van het standaardassetbestand. Met Azure Media Services u accountfilters en assetfilters voor uw inhoud definiëren. 

Zie [Dynamische manifesten](filters-dynamic-manifest-overview.md) en [filters](filters-concept.md)voor een gedetailleerde beschrijving van deze functie en scenario's waar deze wordt gebruikt.

In dit onderwerp wordt uitgelegd hoe u een filter voor een Video on Demand-asset definieert en REST-API's gebruikt om [accountfilters](https://docs.microsoft.com/rest/api/media/accountfilters) en [-activafilters te](https://docs.microsoft.com/rest/api/media/assetfilters)maken. 

> [!NOTE]
> Controleer de [presentatieTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Vereisten 

Als u de in dit onderwerp beschreven stappen wilt voltooien, moet u het volgende doen:

- [Filters en dynamische manifesten bekijken](filters-dynamic-manifest-overview.md).
- [Postman configureren voor API-aanroepen van Azure Media Services REST](media-rest-apis-with-postman.md).

    Volg de laatste stap in het onderwerp [Azure AD-token opvragen](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Een filter definiëren  

Het volgende is het voorbeeld van de **instantie Van Aanvraag** dat de trainingsselectievoorwaarden definieert die aan het manifest worden toegevoegd. Dit filter bevat alle audiotracks die EC-3 zijn en alle videotracks die bitrate hebben in het 0-1000000-bereik.

```json
{
    "properties": {
        "tracks": [
          {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Audio",
                        "operation": "Equal"
                    },
                    {
                        "property": "FourCC",
                        "value": "EC-3",
                        "operation": "Equal"
                    }
                ]
            },
            {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Video",
                        "operation": "Equal"
                    },
                    {
                        "property": "Bitrate",
                        "value": "0-1000000",
                        "operation": "Equal"
                    }
                ]
            }
        ]
     }
}
```

## <a name="create-account-filters"></a>Accountfilters maken

Selecteer **accountfilters**->**maken of bijwerken van een accountfilter**in de verzameling van de postbode die u hebt gedownload.

De **METHODE VOOR HET PUT** HTTP-verzoek is vergelijkbaar met:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Selecteer het tabblad **Hoofdlichaam** en plak de json-code die u [eerder hebt gedefinieerd.](#define-a-filter)

Selecteer **Verzenden**. 

Het filter is gemaakt.

Zie [Maken of bijwerken voor](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate)meer informatie. Zie ook [JSON-voorbeelden voor filters](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Assetfilters maken  

Selecteer **Assets**->**Create of update**Asset Filter in de postmanverzameling 'Media Services v3' die u hebt gedownload.

De **METHODE VOOR HET PUT** HTTP-verzoek is vergelijkbaar met:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Selecteer het tabblad **Hoofdlichaam** en plak de json-code die u [eerder hebt gedefinieerd.](#define-a-filter)

Selecteer **Verzenden**. 

Het assetfilter is gemaakt.

Zie [Assetfilters](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate)maken of bijwerken voor meer informatie over het maken of bijwerken van assetfilters. Zie ook [JSON-voorbeelden voor filters](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter). 

## <a name="associate-filters-with-streaming-locator"></a>Filters koppelen aan Streaming Locator

U een lijst met activa- of accountfilters opgeven, die van toepassing zou zijn op uw streaminglocator. De [Dynamic Packager (Streaming Endpoint)](dynamic-packaging-overview.md) past deze lijst met filters toe samen met de filters die uw client in de URL opgeeft. Deze combinatie genereert een [dynamisch manifest](filters-dynamic-manifest-overview.md), dat is gebaseerd op filters in de URL + filters die u opgeeft op Streaming Locator. We raden u aan deze functie te gebruiken als u filters wilt toepassen, maar de filternamen in de URL niet wilt blootleggen.

Als u filters wilt maken en koppelen aan een streaminglocator met REST, gebruikt u de [Streaming Locators - API maken](https://docs.microsoft.com/rest/api/media/streaminglocators/create) en opgeven `properties.filters` in de [aanvraaginstantie.](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body)
                                
## <a name="stream-using-filters"></a>Streamen met filters

Zodra u filters hebt gedefinieerd, kunnen uw klanten deze gebruiken in de streaming-URL. Filters kunnen worden toegepast op adaptieve bitrate streaming protocollen: Apple HTTP Live Streaming (HLS), MPEG-DASH en Smooth Streaming.

In de volgende tabel worden enkele voorbeelden van URL's met filters weergegeven:

|Protocol|Voorbeeld|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Volgende stappen

[Video's streamen](stream-files-tutorial-with-rest.md) 
