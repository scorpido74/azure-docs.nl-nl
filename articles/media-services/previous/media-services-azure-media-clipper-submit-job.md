---
title: Knip taken voor Azure media Clipper verzenden | Microsoft Docs
description: Stappen voor het verzenden van knip taken vanuit Azure media Clipper
services: media-services
keywords: clip; subclip; code ring; media
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 04d0d2bb8939c8036ec6817c58f9ac2fbb3acd72
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684981"
---
# <a name="submit-clipping-jobs-from-azure-media-clipper"></a>Knip taken verzenden vanuit Azure media Clipper 

Voor Azure media Clipper is een **submitSubclipCallback** -methode vereist die moet worden geïmplementeerd voor het verzenden van knip taken. Deze functie is voor het implementeren van een HTTP POST van de uitvoer van Clipper naar een webservice. Met deze webservice kunt u de coderings taak verzenden. De uitvoer van de Clipper is een Media Encoder Standard coderings voorinstelling voor gerenderde taken of de REST API Payload voor dynamische manifest filter-aanroepen. Dit Pass-Through-model is nodig omdat de referenties van het Media Services-account niet worden beveiligd in de browser van de client.

In het volgende sequentie diagram ziet u de werk stroom tussen de browser-client, uw web-service en Azure Media Services: ![diagram van Azure media Clipper](media/media-services-azure-media-clipper-submit-job/media-services-azure-media-clipper-sequence-diagram.PNG)

In het voor gaande diagram zijn de vier entiteiten: de browser van de eind gebruiker, uw webservice, het CDN-eind punt dat als host fungeert voor de Clipper-resources en Azure Media Services. Wanneer de eind gebruiker naar uw webpagina navigeert, ontvangt de pagina de Java script-en CSS-resources van het host-CDN-eind punt. De eind gebruiker configureert de aanroep voor het maken van een knip taak of dynamische oproep van manifest filters vanuit de browser. Wanneer de eind gebruiker de taak of oproep voor het maken van filters verzendt, wordt de taak lading door de browser naar een webservice die u moet implementeren. Deze webservice verzendt uiteindelijk de knip taak of oproep voor het maken van filters naar Azure Media Services met de referenties van uw Media Services-account.

In het volgende code voorbeeld ziet u een voor beeld van een **submitSubclipCallback** -methode. In deze methode implementeert u het HTTP-bericht van de vooraf ingestelde Media Encoder Standard-Code ring. Als het bericht is voltooid (**resultaat**), wordt de **belofte** opgelost, anders wordt het item afgewezen met fout gegevens.

```javascript
// Submit Subclip Callback
//
// Parameter:
// - subclip: object that represents the subclip (output contract).
//
// Returns: a Promise object that, when resolved, returns true if the operation was accept in the back-end; otherwise, returns false.
var onSubmitSubclip = function (subclip) {
    var promise = new Promise(function (resolve, reject) {
        // TODO: perform the back-end AJAX request to submit the subclip job.
        var result = true;

        if (/* everything turned out fine */ result) {
            resolve(result);
        }
        else {
            reject(Error("error details"));
        }
    });

    return promise;
};

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
```
De uitvoer van het verzenden van taken is de Media Encoder Standard encoding-voor instelling voor een gerenderde taak of de REST API Payload voor dynamische manifest filters.

## <a name="submitting-encoding-job-to-create-video"></a>De coderings taak voor het maken van video verzenden
U kunt een Media Encoder Standard encoding-taak verzenden om een video clip met een frame nauw keurig te maken. De coderings taak produceert gerenderde Video's, een nieuw gefragmenteerd MP4-bestand.

Het taak uitvoer contract voor rendered uitknippad is een JSON-object met de volgende eigenschappen:

```json
{
  /* Required */
  "name": "My New Subclip Output Name",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: The REST API version, currently 2.0 is supported
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id-1",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }, {
    /* Required */
    "id": "my-asset-id-2",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "job",

    /* Required if "type" === "job" */
    /* NOTE: This is the preset for the Media Encoder Standard (MES) processor that can be used in the back-end to submit the subclip job.
    The encoding profile ("Codecs" property) depends on the "singleBitrateMp4Profile" and "multiBitrateMp4Profile" option parameters
    specified when creating the widget instance. */
    /* REFERENCE: https://docs.microsoft.com/azure/media-services/media-services-advanced-encoding-with-mes */
    "job": {
      "Version": 1.0,
      "Codecs": [{
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [{
            "Profile": "Auto",
            "Level": "auto",
            "Bitrate": 6750,
            "MaxBitrate": 6750,
            "BufferWindow": "00:00:05",
            "Width": 1920,
            "Height": 1080,
            "BFrames": 3,
            "ReferenceFrames": 3,
            "AdaptiveBFrame": true,
            "Type": "H264Layer",
            "FrameRate": "0/1"
          }],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [{
        "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
        "Format": {
          "Type": "MP4Format"
        }
      }],
      "Sources": [{
        "AssetId": "my-asset-id-1",
        "StartTime": "0.00:01:15.000",
        "Duration": "0.00:00:25.000"
      }, {
        "AssetId": "my-asset-id-2",
        "StartTime": "0.00:20:04.000",
        "Duration": "0.00:33:07.500"
      }]
    }
  }
}
```

Als u de coderings taak wilt uitvoeren, moet u de Media Encoder Standard encoding-taak verzenden met de gekoppelde voor instelling. Raadpleeg dit artikel voor meer informatie over het verzenden van coderings taken met behulp van de [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-encode-with-media-encoder-standard) of [rest API](https://docs.microsoft.com/azure/media-services/media-services-rest-encode-asset).

## <a name="quickly-creating-video-clips-without-encoding"></a>Snel video clips maken zonder code ring
Als alternatief voor het maken van een coderings taak kunt u Azure media Clipper gebruiken om dynamische manifest filters te maken. Filters hoeven niet te worden gecodeerd en kunnen snel worden gemaakt, omdat er geen nieuwe Asset wordt gemaakt. Het uitvoer contract voor een filter knipsel is een JSON-object met de volgende eigenschappen:

```json
{
  /* Required: Filter name (Alphanumeric characters and hyphens only, no whitespace) */
  "name": "FilterName",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: This subclipper version always returns '2.0' in this field.
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "filter",

    /* Required if "type" === "filter" */
    /* REFERENCE: https://docs.microsoft.com/rest/api/media/operations/assetfilter */
    "filter": {
      "PresentationTimeRange": {
        "StartTimestamp": "340000000",
        "EndTimestamp": "580000000",
        "Timescale": "10000000"
      },
      "Tracks": [{
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "video",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "audio",
          "Operator": "Equal"
        }, {
          "Property": "Name",
          "Value": "audio-track-1",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "text",
          "Operator": "Equal"
        }, {
          "Property": "Language",
          "Value": "en",
          "Operator": "Equal"
        }]
      }]
    }
  }
}
```

Als u de REST-aanroep wilt indienen om een dynamisch manifest filter te maken, moet u de bijbehorende Payload verzenden met behulp van de [rest API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest).
