---
title: Een miniatuursprite genereren met Azure Media Services | Microsoft Documenten
description: In dit onderwerp wordt uitgelegd hoe u een miniatuursprite genereert met Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: e5d32d1bc3bd704b03e58c62251a323ed3f4662c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61229047"
---
# <a name="generate-a-thumbnail-sprite"></a>Een miniatuursprite genereren  

U Media Encoder Standard gebruiken om een miniatuursprite te genereren, een JPEG-bestand dat meerdere miniaturen met kleine resolutie bevat die zijn samengestikt in één (grote) afbeelding, samen met een VTT-bestand. Dit VTT-bestand geeft het tijdsbereik op in de invoervideo die elke miniatuur vertegenwoordigt, samen met de grootte en coördinaten van die miniatuur in het grote JPEG-bestand. Videospelers gebruiken het VTT-bestand en de sprite-afbeelding om een 'visuele' zoekbalk weer te geven, waardoor een kijker visuele feedback krijgt bij het schrobben naar achteren en vooruit langs de videotijdlijn.

Om Media Encoder Standard te gebruiken om Thumbnail Sprite te genereren, de preset:

1. Moet jpg-miniatuurafbeeldingsindeling gebruiken
2. Moet start-/stap-/bereikwaarden opgeven als tijdstempels of %waarden (en geen frametellingen) 
    
    1. Het is goed om tijdstempels en % waarden te mengen

3. Moet de waarde SpriteColumn hebben, als een niet-negatief getal dat groter is dan of gelijk is aan 1

    1. Als SpriteColumn is ingesteld op M >= 1, is de uitvoerafbeelding een rechthoek met M-kolommen. Als het aantal miniaturen dat via #2 wordt gegenereerd geen exact veelvoud van M is, is de laatste rij onvolledig en blijft de zwarte pixels achter.  

Hier volgt een voorbeeld:

```json
{
    "Version": 1.0,
    "Codecs": [
    {
      "Start": "00:00:01",
      "Type": "JpgImage",
      "Step": "5%",
      "Range": "100%",
      "JpgLayers": [
        {
          "Type": "JpgLayer",
          "Width": "10%",
          "Height": "10%",
          "Quality": 90
        }
      ],
      "SpriteColumn": 10
    }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
   ]
}
```

## <a name="known-issues"></a>Bekende problemen

1.  Het is niet mogelijk om een sprite-afbeelding te genereren met één rij afbeeldingen (SpriteColumn = 1 resulteert in een afbeelding met één kolom).
2.  Chunking van de sprite-afbeeldingen in jpeg-afbeeldingen met een bescheiden formaat wordt nog niet ondersteund. Daarom moet ervoor worden gezorgd dat het aantal miniaturen en hun grootte wordt beperkt, zodat de resulterende gestikte miniatuur sprite ongeveer 8M pixels of minder is.
3.  Azure Media Player ondersteunt sprites in Microsoft Edge-, Chrome- en Firefox-browsers. VTT parsing wordt niet ondersteund in IE11.

## <a name="next-steps"></a>Volgende stappen

[Inhoud coderen](media-services-encode-asset.md)
