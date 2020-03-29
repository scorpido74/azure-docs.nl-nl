---
title: Inktgegevens naar de Ink Recognizer-API verzenden
titleSuffix: Azure Cognitive Services
description: Meer informatie over het aanroepen van de Ink Analyzer API voor verschillende toepassingen
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 0ad961495d44f13522a3c02224a5612aaedaf076
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221104"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>Inktgegevens naar de Ink Recognizer-API verzenden 

Digital Inking verwijst naar technologieën die digitale representaties mogelijk maken van input zoals handgeschreven tekst en tekeningen. Dit wordt meestal bereikt met behulp van een digitizer die de bewegingen van invoerapparaten vastlegt, zoals een stylus. Omdat apparaten rijke digitale inktwaarneming mogelijk maken, kunnen kunstmatige intelligentie en machine learning geschreven vormen en tekst in vrijwel elke context herkennen. Met de Ink Recognizer API u pennenstreken verzenden en er gedetailleerde informatie over krijgen. 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>De Ink recognizer API vs. OCR-services

De Ink Recognizer API maakt geen gebruik van Optical Character Recognition (OCR). OCR-services verwerken de pixelgegevens van afbeeldingen om handschrift- en tekstherkenning te bieden. Dit wordt ook wel offline herkenning genoemd. In plaats daarvan vereist de Ink Recognizer API digitale inklijngegevens die worden vastgelegd wanneer het invoerapparaat wordt gebruikt. Het op deze manier verwerken van digitale inktgegevens kan nauwkeurigere herkenningsresultaten opleveren in vergelijking met OCR-services. 

## <a name="sending-ink-data"></a>Inktgegevens verzenden

De Ink Recognizer API vereist de X- en Y-coördinaten die de inktlijnen vertegenwoordigen die door een invoerapparaat zijn gemaakt, vanaf het moment dat het detectieoppervlak wordt geraakt tot wanneer het wordt opgeheven. De punten van elke lijn moeten een tekenreeks van door komma's gescheiden waarden zijn en worden opgemaakt in JSON, zoals het onderstaande voorbeeld. Bovendien moet elke pennenstreek een unieke id in elk verzoek hebben. Als de ID binnen dezelfde aanvraag wordt herhaald, retourneert de API een fout. Voor de meest nauwkeurige herkenningsresultaten hebt u ten minste acht cijfers achter het decimaalpunt. De oorsprong (0,0) van het canvas wordt verondersteld de linkerbovenhoek van het inktdoek te zijn.

> [!NOTE]
> Het volgende voorbeeld is geen geldige JSON. U een volledige Ink Recognizer JSON-aanvraag vinden op [GitHub.](https://go.microsoft.com/fwlink/?linkid=2089909)
 
```json
{
  "language": "en-US",
  "strokes": [
   {
    "id": 43,
    "points": 
        "5.1365, 12.3845,
        4.9534, 12.1301,
        4.8618, 12.1199,
        4.7906, 12.2217,
        4.7906, 12.5372,
        4.8211, 12.9849,
        4.9534, 13.6667,
        5.0958, 14.4503,
        5.3299, 15.2441,
        5.6555, 16.0480,
        ..."
   },
    ...
  ]
}
```

## <a name="ink-recognizer-response"></a>Reactie van Ink Recognizer

De Ink Recognizer API retourneert een analysereactie over de objecten die deze herkende uit de inktinhoud. Het antwoord bevat herkenningseenheden die de relaties tussen verschillende pennenstreken beschrijven. Lijnen die bijvoorbeeld afzonderlijke, afzonderlijke vormen maken, worden in verschillende eenheden weergegeven. Elke eenheid bevat gedetailleerde informatie over de inktlijnen, waaronder het herkende object, de coördinaten en andere tekenkenmerken.

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>Shapes die worden herkend door de INK Recognizer API

De Ink Recognizer API kan de meest gebruikte vormen identificeren bij het maken van notities. De onderstaande afbeelding toont enkele basisvoorbeelden. Zie het [API-verwijzingsartikel](https://go.microsoft.com/fwlink/?linkid=2089907)voor een volledige lijst met vormen en andere inktinhoud die door de API wordt herkend. 

![De lijst met vormen die worden herkend door de INK Recognizer API](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>Aanbevolen belpatronen

U de Ink Recognizer REST API in verschillende patronen aanroepen volgens uw toepassing. 

### <a name="user-initiated-api-calls"></a>Door de gebruiker geïnitieerde API-aanroepen

Als u een app bouwt die gebruikersinvoer nodig heeft (bijvoorbeeld een notitie- of annotatie-app), u hen de controle geven over wanneer en welke inkt naar de Ink Recognizer API wordt verzonden. Deze functionaliteit is vooral handig wanneer tekst en vormen zowel op het canvas aanwezig zijn als gebruikers voor elk van deze functies verschillende acties willen uitvoeren. Overweeg selectiefuncties toe te voegen (zoals een lasso of ander geometrisch selectiegereedschap) waarmee gebruikers kunnen kiezen wat er naar de API wordt verzonden.  

### <a name="app-initiated-api-calls"></a>Door app geïnitieerde API-aanroepen

U uw app ook de Ink Recognizer API laten aanroepen na een time-out. Door de huidige pennenstreken routinematig naar de API te verzenden, u herkenningsresultaten opslaan terwijl ze worden gemaakt terwijl de responstijd van de API wordt verbeterd. U bijvoorbeeld een regel handgeschreven tekst naar de API sturen nadat u hebt gedetecteerd dat uw gebruiker deze heeft voltooid. 

Het hebben van de erkenning resultaten op voorhand geeft u informatie over de kenmerken van pennenstreken als ze betrekking hebben op elkaar. Welke lijnen worden bijvoorbeeld gegroepeerd om hetzelfde woord, lijn, lijst, alinea of vorm te vormen. Deze informatie kan de inktselectiefuncties van uw app verbeteren door bijvoorbeeld groepen lijnen tegelijk te kunnen selecteren.

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>De Ink Recognizer API integreren met Windows Ink

[Windows Ink](https://docs.microsoft.com/windows/uwp/design/input/pen-and-stylus-interactions) biedt tools en technologieën om digitale inktervaringen op een breed scala aan apparaten mogelijk te maken. U het Windows Ink-platform combineren met de Ink Recognizer API om toepassingen te maken die digitale pennenstreken weergeven en interpreteren.

## <a name="next-steps"></a>Volgende stappen

* [Wat is de Ink Recognizer-API?](../overview.md)
* [Verwijzing naar de REST-API voor inktherkenning](https://go.microsoft.com/fwlink/?linkid=2089907)

* Begin met het verzenden van digitale pennenstreekgegevens met:
    * [C #](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [Javascript](../quickstarts/javascript.md)
