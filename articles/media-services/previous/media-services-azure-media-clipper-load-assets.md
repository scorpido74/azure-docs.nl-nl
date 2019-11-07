---
title: Assets in azure media Clipper laden | Microsoft Docs
description: Stappen voor het laden van assets in azure media Clipper
services: media-services
keywords: clip; subclip; code ring; media
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 66b4ca5b2859dd306f6eb1c669a07840189f53d5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685037"
---
# <a name="loading-assets-into-azure-media-clipper"></a>Assets in azure media Clipper laden  

Activa kunnen op twee manieren in de Azure media Clipper worden geladen:
1. Statisch door geven in een bibliotheek met assets
2. Dynamisch genereren van een lijst met assets via API

## <a name="statically-load-videos-into-clipper"></a>Video's statisch laden in Clipper
U kunt Video's statisch laden in de Clipper zodat eind gebruikers clips kunnen bouwen zonder dat ze Video's hoeven te selecteren in het deel venster activa selecteren.

In dit geval geeft u een statische set met assets door aan de Clipper. Elk activum bevat een AMS Asset/filter-ID, naam, gepubliceerde streaming-URL. Indien van toepassing, kan een verificatie token voor inhouds beveiliging of matrix met miniaturen-Url's worden door gegeven. Als dit wordt door gegeven, worden de miniaturen in de interface ingevuld. In scenario's waarin de activa bibliotheek statisch en klein is, kunt u het activa contract voor elk activum in de bibliotheek door geven.

> [!NOTE]
> Bij het statisch laden van assets in de Clipper worden assets **rechtstreeks aan de tijd lijn** toegevoegd en **wordt het deel venster activa niet weer gegeven**. Het eerste activum wordt toegevoegd aan de tijd lijn en de rest van de activa worden aan de rechter kant van de tijd lijn gestapeld.

Als u een statische activa bibliotheek wilt laden, gebruikt u de methode **Load** om door te geven in een JSON-weer gave van elke Asset. In het volgende code voorbeeld ziet u de JSON-weer gave voor één Asset.

```javascript
var assets = [
    {
      /* Required: represents the Azure Media Services asset Id when "type" === "asset"; otherwise, represents the dynamic manifest asset filter Id ("type" === "filter")  */
      "id": "my-asset-or-dynamic-manifest-asset-filter-id",
    
      /* Required: represents the asset name as shown in the Clipper interface */
      "name": "My Asset or Dynamic Manifest Asset Filter Name",
    
      /* Required: must be one of the following values: "asset" or "filter" */
      /* NOTE: "asset" type represents a rendered asset; "filter" type represents a dynamic manifest asset filter */
      "type": "asset",
    
      /* Required */
      "source": {
    
        /* Required: represents the asset streaming locator, the base Smooth Streaming URL */
        "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    
        /* Optional: default value "application/vnd.ms-sstr+xml" */
        "type": "application/vnd.ms-sstr+xml",
    
        /* Required: If the asset has content protection applied, then you must include an array with the different protection types along with the token to request the license/key; otherwise, provide an empty array */
        "protectionInfo": [{
            "type": "AES",
            "authenticationToken": "Bearer aes-token-placeholder"
          },
          {
            "type": "PlayReady",
            "authenticationToken": "Bearer playready-token-placeholder"
          },
          {
            "type": "Widevine",
            "authenticationToken": "Bearer widevine-token-placeholder"
          },
          {
            "type": "FairPlay",
            "certificateUrl": "//example/path/to/myfairplay.der",
            "authenticationToken": "Bearer fairplay-token-placeholder"
          }
        ]
      },
    
      /* Optional: array containing thumbnail URLs for the video. */
      /* NOTE: For the thumbnail URLs to work as expected in the Clipper timeline they must be evenly distributed across the video (based on the duration) and in chronological order within the array. */
      "thumbnails": [
        "//example/path/thumbnail_001.jpg",
        "//example/path/thumbnail_002.jpg",
        "//example/path/thumbnail_003.jpg",
        "//example/path/thumbnail_004.jpg",
        "//example/path/thumbnail_005.jpg"
        ]
    }
];

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
subclipper.ready(function () {
    subclipper.load(assets);
});

```

> [!NOTE]
> U wordt aangeraden de methode load () aan te roepen met de methode Ready (handler), zoals wordt weer gegeven in het vorige voor beeld. In het voor gaande voor beeld wordt gegarandeerd dat de widget gereed is voordat de assets worden geladen.

> [!NOTE]
> De miniaturen van de Url's werken zoals verwacht in de tijd lijn van Clipper, dan moeten ze gelijkmatig worden verdeeld over de video (op basis van de duur) en in chronologische volg orde binnen de matrix. U kunt de volgende JSON-standaard fragment gebruiken als voor beeld voor het genereren van installatie kopieën met de ' Media Encoder Standard-processor:

```json
{
  "Start": "0",
  "Step": "00:00:05",
  "Range": "100%",
  "Type": "PngImage",
  "PngLayers": [
    {
      "Type": "PngLayer",
      "Width": 48,
      "Height": 26
    }
  ]
}
```

## <a name="dynamically-load-videos-in-clipper"></a>Video's dynamisch laden in Clipper
Laad Video's dynamisch in de Clipper om eind gebruikers in staat te stellen Video's te selecteren in het deel venster activa selecteren om te knippen.

U kunt assets ook dynamisch laden via een call back. In scenario's waarin activa dynamisch worden gegenereerd of de tape wisselaar groot is, moet u de belasting via de retour aanroep. Als u de Asset dynamisch wilt laden, moet u de optionele functie onLoadAssets call back implementeren. Deze functie wordt tijdens de initialisatie door gegeven aan de Clipper. De opgeloste activa moeten voldoen aan hetzelfde contract als statische geladen activa. In het volgende code voorbeeld ziet u de methode handtekening, verwachte invoer en verwachte uitvoer.

> [!NOTE]
> Bij het dynamisch laden van assets in de Clipper worden assets weer gegeven in het **deel venster activa selecteren**.

```javascript
// Video Assets Pane Callback
    //
    // Filter Parameters:
    // - search: string value term that will be used in the back-end to filter assets by name.
    // - skip: int value used for pagination in the back-end that allows skipping a number of assets in the response.
    // - take: int value used for pagination in the back-end that allows defining the number of assets to include in the response.
    // - type: ('filter', 'asset') value that will be used in the back-end to filter assets by type.
    //
    // Returns: a Promise object that, when resolved, returns an object containing an array of assets (input contract)
    //          that satisfies the filter parameters, plus optionally the total types of files available:
    // {
    //  total: 100,
    //  assets: [{...}],
    // }
    var onLoadAssets = function (search, skip, take, type) {
        var promise = new Promise(function (resolve, reject) {
            // TODO: implement the getAssetsFromBackend method to get the assets from the back-end using the filter parameters (search, skip, take, type).
            getAssetsFromBackend(search, skip, take, type)
                .then(function (assets) {
                    resolve({
                        total: assets.length,
                        assets: assets
                    });
                }).catch(function () {
                    reject(Error("error details"));
                });
        });
    
        return promise;
    };

    // Create widget instance:
    // - using a root element selector
    // - enabling the Video Assets panel by registering a callback in the 'assetsPanelLoaderCallback' option parameter.
    var widget = new subclipper({
        selector: '#root',

        // Enable the Video Assets panel in the widget to automatically load assets (input contract)
        assetsPanelLoaderCallback: onLoadAssets
    });

    // ...
    // The widget will automatically invoke the 'assetsPanelLoaderCallback' callback with the filter parameters specified by the user 
    // and load assets returned by the Promise into the Video Assets panel.
    // ...
```
