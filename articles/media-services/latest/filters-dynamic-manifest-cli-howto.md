---
title: CLI gebruiken om filters te maken met Azure Media Services| Microsoft Documenten
description: In dit artikel ziet u hoe u CLI gebruikt om filters te maken met Azure Media Services v3.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 74516aa921e45917f327a193a1c972b021c9c8ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896075"
---
# <a name="creating-filters-with-cli"></a>Filters maken met CLI 

Wanneer uw klant uw inhoud aan klanten levert (live-gebeurtenissen streamen of Video on Demand), heeft uw klant mogelijk meer flexibiliteit nodig dan wat wordt beschreven in het manifestbestand van het standaardassetbestand. Met Azure Media Services u accountfilters en assetfilters voor uw inhoud definiëren. 

Zie [Dynamische manifesten](filters-dynamic-manifest-overview.md) en [filters](filters-concept.md)voor een gedetailleerde beschrijving van deze functie en scenario's waar deze wordt gebruikt.

In dit onderwerp wordt uitgelegd hoe u een filter configureert voor een video on-demand-asset en cli voor Media Services v3 gebruikt om [accountfilters](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) en [activafilters te](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)maken. 

> [!NOTE]
> Controleer de [presentatieTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Vereisten 

- [Een Azure Media Services-account maken](create-account-cli-how-to.md). Zorg ervoor dat u de naam van de brongroep en de naam van het Media Services-account onthoudt. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Een filter definiëren 

In het volgende voorbeeld worden de voorwaarden voor trackselectie gedefinieerd die aan het definitieve manifest zijn toegevoegd. Dit filter bevat alle audiotracks die EC-3 zijn en alle videotracks die bitrate hebben in het 0-1000000-bereik.

> [!TIP]
> Als u **filters** in REST wilt definiëren, moet u het JSON-object 'Eigenschappen' opnemen.  

```json
[
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
                "operation": "NotEqual"
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
```

## <a name="create-account-filters"></a>Accountfilters maken

Met de volgende opdracht [az ams-accountfilter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) wordt een accountfilter gemaakt met eerder [gedefinieerde](#define-a-filter)filtertrackselecties. 

Met de opdracht `--tracks` u een optionele parameter doorgeven die JSON bevat die de trackselecties weergeeft.  Gebruik @{file} om JSON uit een bestand te laden. Als u de Azure CLI lokaal gebruikt, geeft u het hele bestandspad op:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Zie ook [JSON-voorbeelden voor filters](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Assetfilters maken

Met de volgende opdracht [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) wordt een assetfilter gemaakt met eerder [gedefinieerde](#define-a-filter)filtertrackselecties. 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Zie ook [JSON-voorbeelden voor filters](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter).

## <a name="associate-filters-with-streaming-locator"></a>Filters koppelen aan Streaming Locator

U een lijst met activa- of accountfilters opgeven, die van toepassing zou zijn op uw streaminglocator. De [Dynamic Packager (Streaming Endpoint)](dynamic-packaging-overview.md) past deze lijst met filters toe samen met de filters die uw client in de URL opgeeft. Deze combinatie genereert een [dynamisch manifest](filters-dynamic-manifest-overview.md), dat is gebaseerd op filters in de URL + filters die u opgeeft op Streaming Locator. We raden u aan deze functie te gebruiken als u filters wilt toepassen, maar de filternamen in de URL niet wilt blootleggen.

De volgende CLI-code laat zien hoe `filters`u een streaminglocator maakt en opgeeft . Dit is een optionele eigenschap die een lijst met namen en/of accountfilternamen met ruimtescheidingen maakt.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Streamen met filters

Zodra u filters hebt gedefinieerd, kunnen uw klanten deze gebruiken in de streaming-URL. Filters kunnen worden toegepast op adaptieve bitrate streaming protocollen: Apple HTTP Live Streaming (HLS), MPEG-DASH en Smooth Streaming.

In de volgende tabel worden enkele voorbeelden van URL's met filters weergegeven:

|Protocol|Voorbeeld|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Volgende stap

[Video's streamen](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Zie ook

[Azure-CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
