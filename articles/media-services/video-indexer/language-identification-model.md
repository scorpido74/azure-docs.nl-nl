---
title: Automatisch de gesp roken taal identificeren met Video Indexer-Azure
titlesuffix: Azure Media Services
description: In dit artikel wordt beschreven hoe het Video Indexer language Identification model wordt gebruikt om automatisch de gesp roken taal in een video te identificeren.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/12/2019
ms.author: ellbe
ms.openlocfilehash: 729ac9fc7c8ec6a85b3497c51888d3b5a6fc8558
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147199"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Automatische identificatie van de gesp roken taal met taal identificatie model

Video Indexer ondersteunt automatische taal identificatie (deksel). Dit is het proces van het automatisch identificeren van de gesp roken taal van de audio en het verzenden van het Media bestand dat moet worden getranscribeerd in de taal die als dominant is geïdentificeerd. De huidige deksel ondersteunt Engels, Spaans, Frans, Duits, Italiaans, Chinees (vereenvoudigd), Japans, Russisch en Portugees (Brazilië). 

## <a name="choosing-auto-language-identification-on-indexing"></a>Automatische taal identificatie voor indexering kiezen

Wanneer u een video met behulp van de API indexeert of [opnieuw indexeert](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) , `sourceLanguage` kiest u de `auto detect` optie in de para meter.

Wanneer u Portal gebruikt, gaat u naar uw **account Video's** op de start pagina van [video indexer](https://www.videoindexer.ai/) en houdt u de muis aanwijzer over de naam van de video die u opnieuw wilt indexeren. Klik in de rechter bovenhoek op de knop opnieuw indexeren. Kies in het dialoog venster **video opnieuw indexeren** de optie *Automatische detectie* in de vervolg keuzelijst **video bron taal** .

![automatische detectie](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Model uitvoer

Video Indexer transcribeert de video aan de hand van de meest waarschijnlijke taal als het vertrouwen voor `> 0.6`die taal is. Als de taal niet met vertrouwen kan worden geïdentificeerd, wordt ervan uitgegaan dat de gesp roken taal Engels is. 

De dominante taal van het model is beschikbaar in de `sourceLanguage` JSON Insights als het kenmerk (onder hoofdmap/Video's/inzichten). Er is ook een bijbehorende betrouwbaarheids Score beschikbaar `sourceLanguageConfidence` onder het-kenmerk.

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

* Ondersteunde talen zijn Engels, Spaans, Frans, Duits, Italiaans, Chinees (vereenvoudigd), Japans, Russisch en Portugees (Brazilië).
* Als de audio andere talen dan de hierboven vermelde lijst bevat, is het resultaat onverwacht.
* Als video indexer de taal niet kan identificeren met een hoge mate van`>0.6`betrouw baarheid (), is de terugval taal Engels.
* Er is geen huidige ondersteuning voor een bestand met audio voor gemengde talen. Als de audio gemengde talen bevat, is het resultaat onverwacht. 
* Audio van lage kwaliteit kan van invloed zijn op de model resultaten.
* Het model vereist ten minste één minuut spraak op de audio.
* Het model is ontworpen om een spontane spreek spraak te herkennen (geen spraak opdrachten, zingen, enzovoort).

## <a name="next-steps"></a>Volgende stappen

* [Overzicht](video-indexer-overview.md)
* [Meerdere talen inhoud automatisch identificeren en transcriberen](multi-language-identification-transcription.md)
