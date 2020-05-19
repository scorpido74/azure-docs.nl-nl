---
title: Gedrukt, handgeschreven tekst herkenning-Computer Vision
titleSuffix: Azure Cognitive Services
description: Concepten met betrekking tot het herkennen van gedrukte en handgeschreven tekst in afbeeldingen met behulp van de Computer Vision-API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5d0a9771e5b999028996676ea72f8def3c5d63cf
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589853"
---
# <a name="recognize-printed-and-handwritten-text"></a>Afgedrukte en handgeschreven tekst herkennen

Computer Vision biedt een aantal services voor het detecteren en uitpakken van gedrukte of handgeschreven tekst die in afbeeldingen wordt weer gegeven. Dit is handig in verschillende scenario's, zoals het nemen van notities, medische records, beveiliging en bankieren. In de volgende drie secties worden drie verschillende tekstherkennings-Api's beschreven, die elk zijn geoptimaliseerd voor verschillende use cases.

## <a name="read-api"></a>API lezen

De Lees-API detecteert tekst inhoud in een afbeelding met onze meest recente herkennings modellen en zet de geïdentificeerde tekst om in een door een machine Lees bare teken stroom. Het is geoptimaliseerd voor tekst-zware afbeeldingen (zoals documenten die digitaal zijn gescand) en voor afbeeldingen met veel visuele ruis. Hiermee wordt bepaald welk herkennings model moet worden gebruikt voor elke tekst regel, met ondersteuning voor afbeeldingen met zowel gedrukte als handgeschreven tekst. De Lees-API wordt asynchroon uitgevoerd omdat grotere documenten enkele minuten kunnen duren om een resultaat te retour neren.

Met de Lees bewerking worden de oorspronkelijke regel groepen van herkende woorden in de uitvoer bijgehouden. Elke regel wordt geleverd met coördinaten van het selectie kader en elk woord in de regel heeft ook een eigen coördinaten. Als een woord is herkend met een lage betrouw baarheid, wordt die informatie ook overgebracht. Raadpleeg de naslag documentatie over [API v 2.0 lezen](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) of Raadpleeg [API v 3.0 referentie documenten](https://aka.ms/computer-vision-v3-ref) voor meer informatie.

De Lees bewerking kan tekst herkennen in het Engels, Spaans, Duits, Frans, Italiaans, Portugees en Nederlands.

### <a name="image-requirements"></a>Vereisten voor installatiekopieën

De Lees-API werkt met installatie kopieën die voldoen aan de volgende vereisten:

- De afbeelding moet worden weer gegeven in de indeling JPEG, PNG, BMP, PDF of TIFF.
- De afmetingen van de afbeelding moeten tussen 50 x 50 en 10000 x 10000 pixels liggen. PDF-pagina's moeten 17 x 17 inch of kleiner zijn.
- De bestands grootte van de afbeelding moet kleiner zijn dan 20 MB (Mega byte).

### <a name="limitations"></a>Beperkingen

Als u een abonnement met een gratis laag gebruikt, worden alleen de eerste twee pagina's van een PDF-of TIFF-document verwerkt met de Lees-API. Met een betaald abonnement worden er Maxi maal 200 pagina's verwerkt. Houd er ook rekening mee dat de API Maxi maal 300 regels per pagina detecteert.

## <a name="ocr-optical-character-recognition-api"></a>OCR (optische teken herkenning)-API

De API van de optische teken herkenning (OCR) van Computer Vision is vergelijkbaar met de Lees-API, maar wordt synchroon uitgevoerd en is niet geoptimaliseerd voor grote documenten. Er wordt een eerder herkennings model gebruikt, maar werkt met meer talen. Zie [taal ondersteuning](language-support.md#text-recognition) voor een volledige lijst met ondersteunde talen.

Als dat nodig is, corrigeert OCR de rotatie van de herkende tekst door de rotatie verschuiving in graden over de horizontale afbeeldings te retour neren. OCR levert ook de frame coördinaten van elk woord, zoals in de volgende afbeelding wordt weer gegeven.

![Een afbeelding die wordt gedraaid en de bijbehorende tekst wordt gelezen en afgebakend](./Images/vision-overview-ocr.png)

Raadpleeg de [documenten voor OCR-referentie](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) voor meer informatie.

### <a name="image-requirements"></a>Vereisten voor installatiekopieën

De OCR-API werkt op installatie kopieën die voldoen aan de volgende vereisten:

* De afbeelding moet worden weer gegeven in de indeling JPEG, PNG, GIF of BMP.
* De grootte van de invoer afbeelding moet tussen 50 x 50 en 4200 x 4200 pixels liggen.
* De tekst in de afbeelding kan worden gedraaid met een veelvoud van 90 graden plus een kleine hoek van Maxi maal 40 graden.

### <a name="limitations"></a>Beperkingen

Op Foto's waarbij de tekst dominant is, kunnen fout-positieven afkomstig zijn van gedeeltelijk herkende woorden. Op sommige Foto's, met name Foto's zonder tekst, kan de precisie variëren, afhankelijk van het type afbeelding.

## <a name="recognize-text-api"></a>Tekst herkennen-API

> [!NOTE]
> De Tekst herkennen-API wordt vervangen door de Lees-API. De Lees-API heeft vergelijk bare mogelijkheden en is bijgewerkt voor het verwerken van PDF-, TIFF-en bestanden met meerdere pagina's.

De Tekst herkennen-API is vergelijkbaar met OCR, maar wordt asynchroon uitgevoerd en maakt gebruik van bijgewerkte herkennings modellen. Raadpleeg de [documentatie](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) van de tekst herkennen-API voor meer informatie.

### <a name="image-requirements"></a>Vereisten voor installatiekopieën

De Tekst herkennen-API werkt met installatie kopieën die voldoen aan de volgende vereisten:

- De afbeelding moet worden weer gegeven in de indeling JPEG, PNG of BMP.
- De afmetingen van de afbeelding moeten tussen 50 x 50 en 4200 x 4200 pixels liggen.
- De bestands grootte van de afbeelding moet kleiner zijn dan 4 MB.

## <a name="limitations"></a>Beperkingen

De nauw keurigheid van tekst herkennings bewerkingen is afhankelijk van de kwaliteit van de afbeeldingen. De volgende factoren kunnen een onnauwkeurige Lees bewerking veroorzaken:

* Wazige afbeeldingen.
* Handgeschreven of cursieve tekst.
* Artistieke lettertypestijlen.
* Kleine lettergrootte.
* Complexe achtergronden, schaduwen of schittering op de tekst of vervorming van het perspectief.
* Grote of ontbrekende hoofd letters aan het begin van woorden.
* Subscript, superscript of doorgehaalde tekst.

## <a name="next-steps"></a>Volgende stappen

Volg de Snelstartgids voor het [uitpakken van tekst (lezen)](./QuickStarts/CSharp-hand-text.md) om tekst herkenning in een eenvoudige C#-app te implementeren.
