---
title: Aangepaste transformatie coderen met Media Services v3 Azure CLI | Microsoft Documenten
description: In dit onderwerp wordt uitgelegd hoe u Azure Media Services v3 gebruiken om een aangepaste transformatie te coderen met Azure CLI.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/14/2019
ms.author: juliako
ms.openlocfilehash: 7c1b446ccf04199449f012e738f6a03660735f50
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382950"
---
# <a name="how-to-encode-with-a-custom-transform---azure-cli"></a>Coderen met een aangepaste transformatie - Azure CLI

Wanneer u codeert met Azure Media Services, u snel aan de slag met een van de aanbevolen ingebouwde voorinstellingen, op basis van aanbevolen procedures in de branche, zoals aangetoond in de snel aanstart [van streamingbestanden.](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) U ook een aangepaste voorinstelling maken om uw specifieke scenario of apparaatvereisten te targeten.

## <a name="considerations"></a>Overwegingen

Bij het maken van aangepaste voorinstellingen zijn de volgende overwegingen van toepassing:

* Alle waarden voor hoogte en breedte op AVC-inhoud moeten een veelvoud van 4 zijn.
* In Azure Media Services v3 zijn alle coderingsbitrates in bits per seconde. Dit is anders dan de presets met onze v2 API's, die kilobits per seconde als eenheid gebruikten. Als de bitrate in v2 bijvoorbeeld is opgegeven als 128 (kilobits/seconde), wordt deze in v3 ingesteld op 128000 (bits/seconde).

## <a name="prerequisites"></a>Vereisten

[Een Azure Media Services-account maken](create-account-cli-how-to.md).

Zorg ervoor dat u de naam van de brongroep en de naam van het Media Services-account onthoudt.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>Een aangepaste voorinstelling definiëren

In het volgende voorbeeld wordt de aanvraagtekst van een nieuwe transformatie gedefinieerd. We definiëren een reeks uitvoerdie we willen genereren wanneer deze transformatie wordt gebruikt.

In dit voorbeeld voegen we eerst een AacAudio-laag toe voor de audiocodering en twee H264Video-lagen voor de videocodering. In de videolagen wijzen we labels toe, zodat ze kunnen worden gebruikt in de namen van uitvoerbestanden. Vervolgens willen we dat de uitvoer ook miniaturen bevat. In het onderstaande voorbeeld geven we afbeeldingen op in PNG-indeling, gegenereerd bij 50% van de resolutie van de invoervideo, en bij drie tijdstempels - {25%, 50%, 75} van de lengte van de invoervideo. Ten slotte specificeren we het formaat voor de uitvoerbestanden - een voor video + audio en een andere voor de miniaturen. Aangezien we meerdere H264Layers hebben, moeten we macro's gebruiken die unieke namen per laag produceren. We kunnen een `{Label}` `{Bitrate}` of macro gebruiken, het voorbeeld toont het eerste.

We gaan deze transformatie opslaan in een bestand. In dit voorbeeld geven `customPreset.json`we een naam aan het bestand .

```json
{
    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
    "codecs": [
        {
            "@odata.type": "#Microsoft.Media.AacAudio",
            "channels": 2,
            "samplingRate": 48000,
            "bitrate": 128000,
            "profile": "AacLc"
        },
        {
            "@odata.type": "#Microsoft.Media.H264Video",
            "keyFrameInterval": "PT2S",
            "stretchMode": "AutoSize",
            "sceneChangeDetection": false,
            "complexity": "Balanced",
            "layers": [
                {
                    "width": "1280",
                    "height": "720",
                    "label": "HD",
                    "bitrate": 3400000,
                    "maxBitrate": 3400000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                },
                {
                    "width": "640",
                    "height": "360",
                    "label": "SD",
                    "bitrate": 1000000,
                    "maxBitrate": 1000000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Media.PngImage",
            "stretchMode": "AutoSize",
            "start": "25%",
            "step": "25%",
            "range": "80%",
            "layers": [
                {
                    "width": "50%",
                    "height": "50%"
                }
            ]
        }
    ],
    "formats": [
        {
            "@odata.type": "#Microsoft.Media.Mp4Format",
            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
            "outputFiles": []
        },
        {
            "@odata.type": "#Microsoft.Media.PngFormat",
            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
        }
    ]
}
```

## <a name="create-a-new-transform"></a>Een nieuwe transformatie maken  

In dit voorbeeld maken we een **transformatie** die is gebaseerd op de aangepaste voorinstelling die we eerder hebben gedefinieerd. Bij het maken van een transformatie moet u eerst controleren of er al een bestaat. Als de transformatie bestaat, u deze opnieuw gebruiken. Met `show` de volgende `customTransformName` opdracht wordt de transformatie geretourneerd als deze bestaat:

```azurecli-interactive
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

Met de volgende opdracht Azure CLI wordt de transformatie gemaakt op basis van de aangepaste voorinstelling (eerder gedefinieerd).

```azurecli-interactive
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

Als Media Services de transformatie wilt toepassen op de opgegeven video of audio, moet u een taak indienen onder die transformatie. Zie [Quickstart: Videobestanden streamen - Azure CLI](stream-files-cli-quickstart.md)voor een compleet voorbeeld waarin wordt weergegeven hoe u een taak onder een transformatie verzenden.

## <a name="see-also"></a>Zie ook

[Azure-CLI](/cli/azure/ams)
