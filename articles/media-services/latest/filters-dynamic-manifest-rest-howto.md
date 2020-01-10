---
title: Filters maken met Azure Media Services v3 REST API
description: In dit onderwerp wordt beschreven hoe u filters maken, zodat de client voor het specifieke secties van de stroom van een stroom gebruiken kunt. Media Services wordt gemaakt dynamische manifesten voor het bereiken van deze selectief streaming.
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
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780331"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Filters maken met Media Services REST API

Wanneer uw inhoud levert aan klanten (streaming Live gebeurtenissen of Video on Demand) is de client mogelijk meer flexibiliteit dan wat wordt beschreven in het manifestbestand van de standaard-asset. Azure Media Services kunt u accountfilters en filters actief voor uw inhoud definiëren. 

Zie [dynamische manifesten](filters-dynamic-manifest-overview.md) en [filters](filters-concept.md)voor een gedetailleerde beschrijving van deze functie en scenario's waarin deze wordt gebruikt.

Dit onderwerp wordt beschreven hoe u een filter te definiëren voor een Video op aanvraag asset en REST-API's gebruiken om te maken [Accountfilters](https://docs.microsoft.com/rest/api/media/accountfilters) en [Asset Filters](https://docs.microsoft.com/rest/api/media/assetfilters). 

> [!NOTE]
> Zorg ervoor dat u de [presentationTimeRange](filters-concept.md#presentationtimerange)controleert.

## <a name="prerequisites"></a>Vereisten 

Als u wilt de in dit onderwerp beschreven stappen hebt voltooid, hebt u naar:

- Beoordeling [Filters en dynamische manifesten](filters-dynamic-manifest-overview.md).
- [Postman configureren voor Azure Media Services REST API-aanroepen](media-rest-apis-with-postman.md).

    Zorg ervoor dat u de laatste stap in het onderwerp [Azure AD-Token ophalen](media-rest-apis-with-postman.md#get-azure-ad-token)volgt. 

## <a name="define-a-filter"></a>Definieer een filter  

Hieronder volgt de **aanvraagtekst** voorbeeld waarin de track selectie voorwaarden die zijn toegevoegd aan het manifest. Dit filter bevat audio tracks die EC-3 zijn en video tracks met bitsnelheid in het 0-1000000-bereik.

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

Selecteer in van de Postman-verzameling die u hebt gedownload, **Accountfilters**->**maken of bijwerken van een Account-Filter**.

De **plaatsen** HTTP-aanvraagmethode is vergelijkbaar met:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Selecteer de **hoofdtekst** tabblad en plak de json-code u [eerder hebt gedefinieerd](#define-a-filter).

Selecteer **Verzenden**. 

Het filter is gemaakt.

Zie voor meer informatie, [maken of bijwerken](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate). Zie ook [JSON-voorbeelden voor filters](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Asset-filters maken  

Selecteer in de Postman-verzameling Media Services v3 die u hebt gedownload **activa**->**activa filter maken of bijwerken**.

De **plaatsen** HTTP-aanvraagmethode is vergelijkbaar met:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Selecteer de **hoofdtekst** tabblad en plak de json-code u [eerder hebt gedefinieerd](#define-a-filter).

Selecteer **Verzenden**. 

Het filter asset is gemaakt.

Zie voor meer informatie over het maken of bijwerken van de asset filters [maken of bijwerken](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Zie ook [JSON-voorbeelden voor filters](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter). 

## <a name="associate-filters-with-streaming-locator"></a>Filters koppelen aan streaming-Locator

U kunt een lijst opgeven met activa of account filters die van toepassing zijn op uw streaming-Locator. Met de [dynamische pakket (streaming-eind punt)](dynamic-packaging-overview.md) wordt deze lijst met filters toegepast, samen met de gegevens die door uw client zijn opgegeven in de URL. Deze combi natie genereert een [dynamisch manifest](filters-dynamic-manifest-overview.md)dat is gebaseerd op filters in de URL + filters die u opgeeft in de streaming-Locator. U wordt aangeraden deze functie te gebruiken als u filters wilt Toep assen, maar niet de filter namen in de URL wilt weer geven.

Als u filters met een streaming-Locator wilt maken en koppelen met behulp van REST, gebruikt u de [streams](https://docs.microsoft.com/rest/api/media/streaminglocators/create) voor het maken van een API en geeft u `properties.filters` op in de [aanvraag tekst](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body).
                                
## <a name="stream-using-filters"></a>Streamen met filters

Zodra u filters hebt gedefinieerd, kunnen uw clients deze gebruiken in de streaming-URL. Filters kunnen worden toegepast op Adaptive Bitrate Streaming protocollen: Apple HTTP Live Streaming (HLS), MPEG-DASH en Smooth Streaming.

In de volgende tabel ziet u enkele voor beelden van Url's met filters:

|Protocol|Voorbeeld|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Volgende stappen

[Stream-video 's](stream-files-tutorial-with-rest.md) 
