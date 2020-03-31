---
title: Aangepaste transformatie coderen met Media Services v3 REST - Azure | Microsoft Documenten
description: In dit onderwerp wordt uitgelegd hoe u Azure Media Services v3 gebruiken om een aangepaste transformatie te coderen met REST.
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
ms.openlocfilehash: 30e22cb786e5dc2a667fe41ca8edf398cf0b7613
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65761798"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>Hoe te coderen met een aangepaste transformatie - REST

Wanneer u codeert met Azure Media Services, u snel aan de slag met een van de aanbevolen ingebouwde voorinstellingen, op basis van aanbevolen procedures in de branche, zoals aangetoond in de zelfstudie [voor streamingbestanden.](stream-files-tutorial-with-rest.md#create-a-transform) U ook een aangepaste voorinstelling maken om uw specifieke scenario of apparaatvereisten te targeten.

## <a name="considerations"></a>Overwegingen

Bij het maken van aangepaste voorinstellingen zijn de volgende overwegingen van toepassing:

* Alle waarden voor hoogte en breedte op AVC-inhoud moeten een veelvoud van 4 zijn.
* In Azure Media Services v3 zijn alle coderingsbitrates in bits per seconde. Dit is anders dan de presets met onze v2 API's, die kilobits per seconde als eenheid gebruikten. Als de bitrate in v2 bijvoorbeeld is opgegeven als 128 (kilobits/seconde), wordt deze in v3 ingesteld op 128000 (bits/seconde).

## <a name="prerequisites"></a>Vereisten 

- [Een Azure Media Services-account maken](create-account-cli-how-to.md). <br/>Zorg ervoor dat u de naam van de brongroep en de naam van het Media Services-account onthoudt. 
- [Postman configureren voor API-aanroepen van Azure Media Services REST](media-rest-apis-with-postman.md).<br/>Volg de laatste stap in het onderwerp [Azure AD-token opvragen](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-custom-preset"></a>Een aangepaste voorinstelling definiëren

In het volgende voorbeeld wordt de aanvraagtekst van een nieuwe transformatie gedefinieerd. We definiëren een reeks uitvoerdie we willen genereren wanneer deze transformatie wordt gebruikt. 

In dit voorbeeld voegen we eerst een AacAudio-laag toe voor de audiocodering en twee H264Video-lagen voor de videocodering. In de videolagen wijzen we labels toe, zodat ze kunnen worden gebruikt in de namen van uitvoerbestanden. Vervolgens willen we dat de uitvoer ook miniaturen bevat. In het onderstaande voorbeeld geven we afbeeldingen op in PNG-indeling, gegenereerd bij 50% van de resolutie van de invoervideo, en bij drie tijdstempels - {25%, 50%, 75} van de lengte van de invoervideo. Ten slotte specificeren we het formaat voor de uitvoerbestanden - een voor video + audio en een andere voor de miniaturen. Aangezien we meerdere H264Layers hebben, moeten we macro's gebruiken die unieke namen per laag produceren. We kunnen een `{Label}` `{Bitrate}` of macro gebruiken, het voorbeeld toont het eerste.

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
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
            }
        ]
    }
}

```

## <a name="create-a-new-transform"></a>Een nieuwe transformatie maken  

In dit voorbeeld maken we een **transformatie** die is gebaseerd op de aangepaste voorinstelling die we eerder hebben gedefinieerd. Wanneer u een transformatie maakt, moet u [eerst Get](https://docs.microsoft.com/rest/api/media/transforms/get) gebruiken om te controleren of er al een bestaat. Als de transformatie bestaat, u deze opnieuw gebruiken. 

Selecteer **transformeren en transformeren**->in de verzameling van de postbode die u hebt**gedownload.**

De **METHODE VOOR HET PUT** HTTP-verzoek is vergelijkbaar met:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Selecteer het tabblad **Hoofdlichaam** en vervang de hoofdtekst door de eerder [gedefinieerde](#define-a-custom-preset)JSON-code. Als Media Services de transformatie wilt toepassen op de opgegeven video of audio, moet u een taak indienen onder die transformatie.

Selecteer **Verzenden**. 

Als Media Services de transformatie wilt toepassen op de opgegeven video of audio, moet u een taak indienen onder die transformatie. Zie [Zelfstudie: Videobestanden streamen - REST](stream-files-tutorial-with-rest.md)voor een compleet voorbeeld waarin wordt uitgelegd hoe u een taak onder een transformatie indient.

## <a name="next-steps"></a>Volgende stappen

Bekijk [andere REST-bewerkingen](https://docs.microsoft.com/rest/api/media/)
