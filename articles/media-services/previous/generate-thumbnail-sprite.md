---
title: Een miniatuur maken voor de sprite met Azure Media Services | Microsoft Docs
description: In dit onderwerp wordt uitgelegd hoe u een miniatuur sprite met Azure Media Services genereert.
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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/26/2020
ms.locfileid: "61229047"
---
# <a name="generate-a-thumbnail-sprite"></a>Een miniatuursprite genereren  

U kunt Media Encoder Standard gebruiken voor het genereren van een miniatuur sprite, een JPEG-bestand met meerdere miniaturen voor kleine oplossingen die samen worden gecombineerd in één (grote) installatie kopie, samen met een VTT-bestand. Dit VTT-bestand bevat het tijds bereik in de invoer video dat elke miniatuur vertegenwoordigt, samen met de grootte en coördinaten van die miniatuur binnen het grote JPEG-bestand. Video spelers gebruiken de VTT-bestand en de sprite-afbeelding om een ' Visual ' SeekBar weer te geven, met een viewer met visuele feedback bij het reinigen en door sturen van de video tijdlijn.

Als u Media Encoder Standard wilt gebruiken om miniatuur sprite te genereren, wordt de voor instelling:

1. Indeling van JPG-miniatuur afbeelding moet worden gebruikt
2. U moet waarden voor begin/stap/bereik opgeven als tijds tempels of% values (en geen frame aantallen) 
    
    1. U kunt tijds tempels en% waarden combi neren

3. Moet de SpriteColumn-waarde hebben, als een niet-negatief getal dat groter is dan of gelijk is aan 1

    1. Als SpriteColumn is ingesteld op M >= 1, is de uitvoer afbeelding een rechthoek met M kolommen. Als het aantal miniaturen dat is gegenereerd via #2 geen exact veelvoud van M is, is de laatste rij onvolledig en links met zwarte pixels.  

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

1.  Het is niet mogelijk om een sprite-installatie kopie met één rij met installatie kopieën te genereren (SpriteColumn = 1 resulteert in een afbeelding met één kolom).
2.  Het delen van de sprite-afbeeldingen in JPEG-afbeeldingen met een gemiddelde grootte wordt nog niet ondersteund. Daarom moet worden gezorgd voor het beperken van het aantal miniaturen en de grootte ervan, zodat de resulterende gehechte miniatuur van de miniaturen ongeveer 8 min. pixels of minder is.
3.  Azure Media Player ondersteunt sprites op micro soft Edge-, Chrome-en Firefox-browsers. Het parseren van VTT wordt niet ondersteund in IE11.

## <a name="next-steps"></a>Volgende stappen

[Inhoud coderen](media-services-encode-asset.md)
