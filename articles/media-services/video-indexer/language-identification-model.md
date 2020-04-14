---
title: Video-indexeren gebruiken om gesproken talen automatisch te identificeren - Azure
titleSuffix: Azure Media Services
description: In dit artikel wordt beschreven hoe het taalidentificatiemodel van video-indexer wordt gebruikt om de gesproken taal in een video automatisch te identificeren.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: ba1521581316d559eb4e67bafba0061c31cc666b
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272947"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Identificeer de gesproken taal automatisch met taalidentificatiemodel

Video Indexer ondersteunt automatische taalidentificatie (LID), het proces van het automatisch identificeren van de gesproken taalinhoud van audio en het verzenden van het mediabestand dat moet worden getranscribeerd in de dominante geïdentificeerde taal. 

Momenteel ondersteunt LID: Engels, Spaans, Frans, Duits, Italiaans, Mandarijn Chines, Japans, Russisch en Portugees (Braziliaans). 

Bekijk hieronder de [sectie Richtlijnen en beperkingen.](#guidelines-and-limitations)

## <a name="choosing-auto-language-identification-on-indexing"></a>Automatische taalidentificatie kiezen bij indexering

Wanneer u een video indexeert of [opnieuw indexeert](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) met de API, kiest u de `auto detect` optie in de `sourceLanguage` parameter.

Wanneer u portal gebruikt, gaat u naar uw **accountvideo's** op de startpagina [van Video Indexer](https://www.videoindexer.ai/) en plaatst u de plaats boven de naam van de video die u opnieuw wilt indexeren. Klik rechtsonder op de knop opnieuw indexeren. Kies in het dialoogvenster **Video opnieuw indexeren** de optie *Automatisch detecteren in* het vervolgkeuzevak **Videobrontaal.**

![automatisch detecteren](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Uitvoer van model

Video Indexer transcribeert de video volgens de meest waarschijnlijke `> 0.6`taal als het vertrouwen voor die taal is . Als de taal niet met vertrouwen kan worden geïdentificeerd, gaat het ervan uit dat de gesproken taal Engels is. 

Model dominante taal is beschikbaar in `sourceLanguage` de inzichten JSON als het attribuut (onder root / video's / inzichten). Een bijbehorende betrouwbaarheidsscore `sourceLanguageConfidence` is ook beschikbaar onder het attribuut.

```json
"insights": {
        "version": "1.0.0.0",
        "duration": "0:05:30.902",
        "sourceLanguage": "fr-FR",
        "language": "fr-FR",
        "transcript": [...],
        . . .
        "sourceLanguageConfidence": 0.8563
      },
```

## <a name="guidelines-and-limitations"></a>Richtlijnen en beperkingen

* Automatische taalidentificatie (LID) ondersteunt de volgende talen: 

    Engels, Spaans, Frans, Duits, Italiaans, Mandarijn Chines, Japans, Russisch en Portugees (Braziliaans).
* Hoewel Video Indexer Arabisch (Modern Standard en Levantine), Hindi en Koreaans ondersteunt, worden deze talen niet ondersteund in LID.
* Als de audio andere talen bevat dan de bovenstaande ondersteunde lijst, is het resultaat onverwacht.
* Als Video Indexer de taal niet kan`>0.6`identificeren met een voldoende hoog vertrouwen ( ), is de terugvaltaal Engels.
* Er is momenteel geen ondersteuning voor bestand met audio in gemengde talen. Als de audio gemengde talen bevat, is het resultaat onverwacht. 
* Audio van lage kwaliteit kan van invloed zijn op de modelresultaten.
* Het model vereist ten minste één minuut spraak in de audio.
* Het model is ontworpen om een spontane gesprekstoespraak te herkennen (geen spraakopdrachten, zang, enz.).

## <a name="next-steps"></a>Volgende stappen

* [Overzicht](video-indexer-overview.md)
* [Meertalige inhoud automatisch identificeren en transcriberen](multi-language-identification-transcription.md)
