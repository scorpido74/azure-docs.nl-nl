---
title: Optische teken herkenning (OCR)-Computer Vision
titleSuffix: Azure Cognitive Services
description: Concepten met betrekking tot optische teken herkenning (OCR) van afbeeldingen en documenten met gedrukte en handgeschreven tekst met behulp van de Computer Vision-API.
services: cognitive-services
author: msbbonsu
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: t-bebon
ms.custom: seodec18
ms.openlocfilehash: 6bc118145bec30085c2d9fbf726c40a20b312430
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207049"
---
# <a name="optical-character-recognition-ocr"></a>Optische tekenherkenning (OCR)

De Computer Vision-API van micro soft bevat OCR-mogelijkheden (Optical Character Recognition) waarmee u gedrukte of handgeschreven tekst uit afbeeldingen en PDF-documenten ophaalt. De OCR-Api's halen tekst uit zowel analoge documenten (afbeeldingen, gescande documenten) als gedigitaliseerde documenten. U kunt tekst uit afbeeldingen in het wild extra heren, zoals foto's van een licentie plaat of containers met serie nummers, en van documenten, facturen, facturen, financiële rapporten, artikelen en meer. De nieuwe OCR-API voor lezen is beschikbaar als onderdeel van de beheerde service in de Cloud of on-premises (containers). Het ondersteunt ook virtuele netwerken en persoonlijke eind punten om te voldoen aan de vereisten voor naleving en privacy van uw onderneming.

## <a name="read-api"></a>API lezen 

De [Lees-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) van computer vision is de nieuwste OCR-technologie van micro soft waarmee gedrukte tekst (zeven talen), handgeschreven tekst (alleen Engels), cijfers en valuta symbolen van afbeeldingen en PDF-documenten met meerdere pagina's worden geëxtraheerd. Het is geoptimaliseerd voor het extra heren van tekst van in-the-Wild-tekst zware afbeeldingen en PDF-documenten met meerdere pagina's met gemengde talen. Het biedt ondersteuning voor het detecteren van gedrukte en handgeschreven tekst (alleen Engels) in dezelfde afbeelding of hetzelfde document. Raadpleeg de volledige lijst met [ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) voor de OCR-pagina.

### <a name="how-ocr-works"></a>Hoe OCR werkt

De [Lees-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) ondersteunt tekst-zware documenten van maxi maal 2000 pagina's en wordt daarom asynchroon uitgevoerd. De eerste stap is het aanroepen van de Lees bewerking. De Lees bewerking neemt een afbeeldings-of PDF-document als invoer en retourneert een bewerkings-ID. 

De tweede stap is het aanroepen van de bewerking [resultaten ophalen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) . Deze bewerking neemt de bewerkings-ID die is gemaakt door de Lees bewerking. Vervolgens wordt de uitgepakte tekst inhoud uit de afbeelding of het document geretourneerd in de vorm van JSON. De JSON-respons houdt de oorspronkelijke regel groeperingen van herkende woorden bij. Het bevat de geëxtraheerde tekst regels en de coördinaten van het begrenzingsvak. Elke tekst regel bevat alle geëxtraheerde woorden met hun coördinaten en betrouwbaarheids scores.

Als dat nodig is, wordt de rotatie van de herkende pagina gecorrigeerd door de rotatie verschuiving in graden over de horizontale afbeeldings te retour neren, zoals in de volgende afbeelding wordt weer gegeven.

![Hoe OCR afbeeldingen en documenten converteert naar gestructureerde uitvoer met geëxtraheerde tekst](./Images/how-ocr-works.svg)

### <a name="sample-ocr-output"></a>Voor beeld van OCR-uitvoer

Een geslaagde reactie wordt geretourneerd in de JSON-indeling, zoals wordt weer gegeven in het volgende voor beeld:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              }
            ]
          }
        ]
      }
    ]
  }
}
```

Volg de Snelstartgids [gedrukte en handgeschreven tekst uitpakken](./QuickStarts/CSharp-hand-text.md) om OCR te implementeren met C# en de rest API.

### <a name="input-requirements"></a>Invoer vereisten

De Lees-API heeft de volgende invoer:
* Ondersteunde bestands indelingen: JPEG, PNG, BMP, PDF en TIFF
* Voor PDF en TIFF worden Maxi maal 2000 pagina's verwerkt. Voor abonnees met een gratis laag worden alleen de eerste twee pagina's verwerkt.
* De bestands grootte moet kleiner zijn dan 50 MB en de afmetingen zijn ten minste 50 x 50 pixels en Maxi maal 10000 x 10000 pixels.
* De PDF-dimensies mogen Maxi maal 17 x 17 inch zijn, overeenkomen met de papier formaten Legal of a3 en kleiner.

### <a name="text-from-images"></a>Tekst van afbeeldingen

De volgende Lees-API-uitvoer toont de geëxtraheerde tekst regels en woorden uit een afbeelding met tekst op verschillende hoeken, kleuren en letter typen

![Een afbeelding die wordt gedraaid en de bijbehorende tekst wordt gelezen en afgebakend](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>Tekst uit documenten

Naast installatie kopieën gebruikt de Lees-API een PDF-document als invoer.

![Een afbeelding die wordt gedraaid en de bijbehorende tekst wordt gelezen en afgebakend](./Images/text-from-documents-example.png)


### <a name="handwritten-text-in-english"></a>Handgeschreven tekst in het Engels

De Lees bewerking biedt nu alleen ondersteuning voor het extra heren van handgeschreven tekst in het Engels.

![Een afbeelding die wordt gedraaid en de bijbehorende tekst wordt gelezen en afgebakend](./Images/handwritten-example.png)

### <a name="printed-text-in-supported-languages"></a>Gedrukte tekst in ondersteunde talen

De Lees-API biedt ondersteuning voor het extra heren van gedrukte tekst in het Engels, Spaans, Duits, Frans, Italiaans, Portugees en Nederlandstalige talen. Als uw scenario ondersteuning vereist voor meer talen, raadpleegt u het overzicht van de OCR API in dit document. Raadpleeg de lijst met alle talen die door [OCR worden ondersteund](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr)

![Een afbeelding die wordt gedraaid en de bijbehorende tekst wordt gelezen en afgebakend](./Images/supported-languages-example.png)

### <a name="mixed-languages-support"></a>Ondersteuning voor gemengde talen

De Lees-API ondersteunt afbeeldingen en documenten met meerdere talen, die vaak worden aangeduid als gemengde taal documenten. Dit doet u door elke tekst regel in het document in de gedetecteerde taal te classificeren voordat u de tekst inhoud uitpakt.

![Een afbeelding die wordt gedraaid en de bijbehorende tekst wordt gelezen en afgebakend](./Images/mixed-language-example.png)

### <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Net als bij alle cognitieve services moeten ontwikkel aars die de Lees service gebruiken op de hoogte zijn van het micro soft-beleid voor klant gegevens. Zie de pagina Cognitive Services in het [micro soft vertrouwens centrum](https://www.microsoft.com/en-us/trust-center/product-overview) voor meer informatie.

### <a name="deploy-on-premises"></a>On-premises implementeren

Lezen is ook beschikbaar als docker-container (preview), zodat u de nieuwe OCR-mogelijkheden kunt implementeren in uw eigen omgeving. Containers zijn geweldig voor specifieke vereisten voor beveiliging en gegevens beheer. Zie [Lees containers installeren en uitvoeren.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)


## <a name="ocr-api"></a>OCR-API

De [OCR-API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) maakt gebruik van een ouder herkennings model, ondersteunt alleen installatie kopieën en voert synchroon uit, wat direct met de gedetecteerde tekst wordt geretourneerd. Zie de [OCR-ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) dan de Lees-API.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [lees 3,0-rest API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005).
- Volg de Snelstartgids voor het [uitpakken van tekst](./QuickStarts/CSharp-hand-text.md) voor het implementeren van OCR met C#, Java, java script of python samen met rest API.
