---
title: Gedrukte, handgeschreven tekstherkenning - Computer Vision
titleSuffix: Azure Cognitive Services
description: Concepten met betrekking tot het herkennen van gedrukte en handgeschreven tekst in afbeeldingen met behulp van de Computer Vision API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a4c90ed12c8023e0b9ebc509b20d8d9224b49f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220158"
---
# <a name="recognize-printed-and-handwritten-text"></a>Afgedrukte en handgeschreven tekst herkennen

Computer Vision biedt een aantal services die afgedrukte of handgeschreven tekst detecteren en extraheren die in afbeeldingen wordt weergegeven. Dit is handig in verschillende scenario's, zoals notities maken, medische dossiers, beveiliging en bankieren. In de volgende drie secties worden drie verschillende API's voor tekstherkenning beschreven, elk geoptimaliseerd voor verschillende use cases.

## <a name="read-api"></a>API lezen

De Read API detecteert tekstinhoud in een afbeelding met behulp van onze nieuwste herkenningsmodellen en zet de geïdentificeerde tekst om in een machineleesbare tekenstroom. Het is geoptimaliseerd voor tekstzware afbeeldingen (zoals documenten die digitaal zijn gescand) en voor afbeeldingen met veel visuele ruis. Het zal bepalen welk herkenningsmodel voor elke regel tekst moet worden gebruikt en ondersteunt afbeeldingen met zowel gedrukte als handgeschreven tekst. De Api lezen wordt asynchroon uitgevoerd omdat het enkele minuten kan duren voordat grotere documenten een resultaat kunnen retourneren.

Met de bewerking Lezen worden de oorspronkelijke regelgroepen van herkende woorden in de uitvoer behouden. Elke regel wordt geleverd met grenzende vak coördinaten, en elk woord binnen de lijn heeft ook zijn eigen coördinaten. Als een woord met weinig vertrouwen werd herkend, wordt die informatie ook overgebracht. Zie de [referentiedocumenten voor API-verwijzingen lezen](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) en [Referentiedocumenten voor API-voorbeeld lezen](https://go.microsoft.com/fwlink/?linkid=2118322) voor meer informatie.

> [!NOTE]
> Deze functie is alleen beschikbaar voor Engelse en Spaanse (voorbeeld) tekst.

### <a name="image-requirements"></a>Vereisten voor installatiekopieën

De API Lezen werkt met afbeeldingen die voldoen aan de volgende vereisten:

- De afbeelding moet worden weergegeven in de JPEG-, PNG-, BMP-, PDF- of TIFF-indeling.
- De afmetingen van de afbeelding moeten tussen de 50 x 50 en 10000 x 10000 pixels liggen. PDF-pagina's moeten 17 x 17 inch of kleiner zijn.
- De bestandsgrootte van de afbeelding moet minder dan 20 megabyte (MB) bedragen.

### <a name="limitations"></a>Beperkingen

Als u een gratis abonnement gebruikt, verwerkt de Api Lezen alleen de eerste twee pagina's van een PDF- of TIFF-document. Met een betaald abonnement verwerkt het tot 200 pagina's. Houd er ook rekening mee dat de API maximaal 300 regels per pagina detecteert.

## <a name="ocr-optical-character-recognition-api"></a>OCR (optische tekenherkenning) API

De OCR-API (Optical Character Recognition) van Computer Vision is vergelijkbaar met de Read API, maar wordt synchroon uitgevoerd en is niet geoptimaliseerd voor grote documenten. Het maakt gebruik van een eerder herkenningsmodel, maar werkt met meer talen; [zie Taalondersteuning](language-support.md#text-recognition) voor een volledige lijst met ondersteunde talen.

Indien nodig corrigeert OCR de rotatie van de herkende tekst door de rotatieverschuiving in graden over de horizontale afbeeldingsas terug te sturen. OCR biedt ook de framecoördinaten van elk woord, zoals te zien in de volgende afbeelding.

![Een afbeelding wordt gedraaid en de tekst wordt gelezen en afgebakend](./Images/vision-overview-ocr.png)

Zie de [OCR-referentiedocumenten](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) voor meer informatie.

### <a name="image-requirements"></a>Vereisten voor installatiekopieën

De OCR API werkt op afbeeldingen die aan de volgende vereisten voldoen:

* De afbeelding moet worden weergegeven in de JPEG-, PNG-, GIF- of BMP-indeling.
* De grootte van de invoerafbeelding moet tussen de 50 x 50 en 4200 x 4200 pixels liggen.
* De tekst in de afbeelding kan worden gedraaid door een veelvoud van 90 graden plus een kleine hoek van maximaal 40 graden.

### <a name="limitations"></a>Beperkingen

Op foto's waar tekst dominant is, kunnen valse positieven afkomstig zijn van gedeeltelijk erkende woorden. Op sommige foto's, vooral foto's zonder tekst, kan de precisie variëren afhankelijk van het type afbeelding.

## <a name="recognize-text-api"></a>Tekst-API herkennen

> [!NOTE]
> De API Voor tekst herkennen wordt afgeschaft ten gunste van de Read API. De Read API heeft vergelijkbare mogelijkheden en is bijgewerkt voor het verwerken van PDF-, TIFF- en meerpaginabestanden.

De API voor tekst herkennen is vergelijkbaar met OCR, maar wordt asynchroon uitgevoerd en maakt gebruik van bijgewerkte herkenningsmodellen. Zie de [referentiedocumenten voor tekst-API herkennen](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) voor meer informatie.

### <a name="image-requirements"></a>Vereisten voor installatiekopieën

De API Tekst herkennen werkt met afbeeldingen die aan de volgende vereisten voldoen:

- De afbeelding moet worden weergegeven in jpeg-, PNG- of BMP-indeling.
- De afmetingen van de afbeelding moeten tussen de 50 x 50 en 4200 x 4200 pixels liggen.
- De bestandsgrootte van de afbeelding moet minder dan 4 megabyte (MB) bedragen.

## <a name="limitations"></a>Beperkingen

De nauwkeurigheid van tekstherkenningsbewerkingen is afhankelijk van de kwaliteit van de afbeeldingen. De volgende factoren kunnen een onjuiste meting veroorzaken:

* Wazige afbeeldingen.
* Handgeschreven of cursieve tekst.
* Artistieke lettertypestijlen.
* Kleine lettergrootte.
* Complexe achtergronden, schaduwen of schittering op de tekst of vervorming van het perspectief.
* Oversized of ontbrekende hoofdletters aan het begin van woorden.
* Subscript, superscript of doorgehaalde tekst.

## <a name="next-steps"></a>Volgende stappen

Volg de [snelstart van de tekst extraheren (Lezen)](./QuickStarts/CSharp-hand-text.md) om tekstherkenning te implementeren in een eenvoudige C#-app.
