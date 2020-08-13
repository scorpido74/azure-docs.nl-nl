---
title: Optische teken herkenning (OCR)-Computer Vision
titleSuffix: Azure Cognitive Services
description: Concepten met betrekking tot optische teken herkenning (OCR) van afbeeldingen en documenten met gedrukte en handgeschreven tekst met behulp van de Computer Vision-API.
services: cognitive-services
author: PatrickFarley
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e2226f70ed3318bb370f0afee003fd9f91153a45
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167862"
---
# <a name="optical-character-recognition-ocr"></a>Optische tekenherkenning (OCR)

De Computer Vision-API van Azure bevat functies voor optische teken herkenning (OCR) waarmee gedrukte of handgeschreven tekst uit afbeeldingen kan worden opgehaald. U kunt tekst extra heren uit afbeeldingen, zoals foto's van een licentie plaat of containers met serie nummers, en van documenten: facturen, facturen, financiële rapporten, artikelen en meer. 

## <a name="read-api"></a>API lezen 

De Computer Vision [Lees-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) is de nieuwste OCR-technologie van Azure waarmee gedrukte tekst (in verschillende talen), handgeschreven tekst (alleen Engels), cijfers en valuta symbolen van afbeeldingen en PDF-documenten met meerdere pagina's worden geëxtraheerd. Het is geoptimaliseerd voor het extra heren van tekst uit tekst-zware afbeeldingen en PDF-documenten met meerdere pagina's met gemengde talen. Het biedt ondersteuning voor het detecteren van gedrukte en handgeschreven tekst in dezelfde afbeelding of hetzelfde document.

![Hoe OCR afbeeldingen en documenten converteert naar gestructureerde uitvoer met geëxtraheerde tekst](./Images/how-ocr-works.svg)

De Lees-API biedt **OCR-mogelijkheden** via twee bewerkingen-Lees-en **Lees resultaten ophalen**.

## <a name="the-read-operation"></a>De Lees bewerking

De [Lees bewerking](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) neemt een afbeeldings-of PDF-document als invoer en extraheert tekst asynchroon. De aanroep retourneert een veld met een antwoord header met de naam `Operation-Location` . De `Operation-Location` waarde is een URL die de bewerkings-id bevat die in de volgende stap moet worden gebruikt.

|Reactie header| Resultaten-URL |
|:-----|:----|
|Bewerkings locatie | https://cognitiveservice/vision/v3.0-preview/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f |

## <a name="the-get-read-results-operation"></a>De bewerking Lees resultaten ophalen

De tweede stap is het aanroepen van de bewerking [Lees resultaten ophalen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) . Met deze bewerking wordt de bewerkings-ID die is gemaakt door de Lees bewerking, als invoer gebruikt. Er wordt een JSON-antwoord geretourneerd dat een **status** veld met de volgende mogelijke waarden bevat. U roept deze bewerking iteratief aan tot deze met de **geslaagde** waarde wordt geretourneerd. Gebruik een interval van 1 tot 2 seconden om te voor komen dat het aantal aanvragen per seconde (RPS) wordt overschreden.

|Veld| Type | Mogelijke waarden |
|:-----|:----:|:----|
|status | tekenreeks | notStarted: de bewerking is niet gestart. |
| |  | uitgevoerd: de bewerking wordt verwerkt. |
| |  | mislukt: de bewerking is mislukt. |
| |  | geslaagd: de bewerking is voltooid. |

> [!NOTE]
> De laag gratis beperkt het aanvraag aantal tot 20 aanroepen per minuut. De laag betaald staat 10 aanvragen per seconde (RPS) toe die op aanvraag kunnen worden verhoogd. Gebruik het ondersteunings kanaal van Azure of uw account team om een RPS-rate (hogere aanvraag per seconde) aan te vragen.

Wanneer het veld **status** de waarde **geslaagd** heeft, bevat het JSON-antwoord de geëxtraheerde tekst inhoud van uw afbeelding of document. De JSON-respons houdt de oorspronkelijke regel groeperingen van herkende woorden bij. Het bevat de geëxtraheerde tekst regels en de coördinaten van het begrenzingsvak. Elke tekst regel bevat alle geëxtraheerde woorden met hun coördinaten en betrouwbaarheids scores.

### <a name="sample-json-output"></a>Voor beeld van JSON-uitvoer

Zie het volgende voor beeld van een geslaagde JSON-reactie:

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

## <a name="input-requirements"></a>Vereisten voor invoer

De invoer afbeeldingen en documenten hebben de volgende vereisten:
* Ondersteunde bestands indelingen: JPEG, PNG, BMP, PDF en TIFF
* Voor PDF en TIFF worden Maxi maal 2000 pagina's verwerkt. Voor abonnees met een gratis laag worden alleen de eerste twee pagina's verwerkt.
* De bestands grootte moet kleiner zijn dan 50 MB en de afmetingen zijn ten minste 50 x 50 pixels en Maxi maal 10000 x 10000 pixels.
* De PDF-dimensies mogen Maxi maal 17 x 17 inch zijn, overeenkomen met de papier formaten Legal of a3 en kleiner.

> [!NOTE]
> **Taal invoer** 
>
> De [Lees bewerking](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) heeft een optionele aanvraag parameter voor de taal. Dit is de taal code BCP-47 van de tekst in het document. Lees ondersteuning voor automatische taal identificatie en meertalige documenten, zodat u alleen een taal code kunt opgeven als u wilt afdwingen dat het document wordt verwerkt als die specifieke taal.

## <a name="language-support"></a>Taalondersteuning

### <a name="printed-text"></a>Afgedrukte tekst
De [lees 3,0-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) biedt ondersteuning voor het extra heren van gedrukte tekst in het Engels, Spaans, Duits, Frans, Italiaans, Portugees en Nederlandstalige talen. 

De [open bare preview van de lees 3,1 API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005) voegt ondersteuning toe voor vereenvoudigd Chinees. Als uw scenario ondersteuning vereist voor meer talen, raadpleegt u de sectie [OCR API](#ocr-api) . 

Zie de [ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) voor de volledige lijst met talen die door OCR worden ondersteund.

### <a name="handwritten-text"></a>Handgeschreven tekst
De Lees bewerking biedt momenteel alleen ondersteuning voor het extra heren van handgeschreven tekst in het Engels.

## <a name="integration-options"></a>Integratie opties

### <a name="use-the-rest-api-or-client-sdk"></a>De REST API-of client-SDK gebruiken
De [rest API 3. x](./QuickStarts/CSharp-hand-text.md) is de voorkeurs optie voor de meeste klanten vanwege het gemak van integratie en snelle productiviteit. Azure en de Computer Vision service-afhandelings schaal, prestaties, gegevens beveiliging en nalevings behoeften terwijl u zich richt op het voldoen aan de behoeften van uw klanten.

### <a name="use-containers-for-on-premise-deployment"></a>Containers gebruiken voor on-premises implementaties
Met de [lees 2,0 docker-container (preview)](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers) kunt u de nieuwe OCR-mogelijkheden in uw eigen lokale omgeving implementeren. Containers zijn geweldig voor specifieke vereisten voor beveiliging en gegevens beheer.

## <a name="read-ocr-examples"></a>OCR-voor beelden lezen

### <a name="text-from-images"></a>Tekst van afbeeldingen

De volgende Lees-API-uitvoer toont de geëxtraheerde tekst van een afbeelding met verschillende tekst hoeken, kleuren en letter typen.

![Een afbeelding van verschillende woorden bij verschillende kleuren en hoeken, met geëxtraheerde tekst in de lijst](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>Tekst uit documenten

Lees-API kan ook PDF-documenten als invoer hebben.

![Een factuur document, met de lijst geëxtraheerde tekst](./Images/text-from-pdf-example.png)

### <a name="handwritten-text"></a>Handgeschreven tekst

Met de Lees bewerking wordt handgeschreven tekst uit afbeeldingen geëxtraheerd (momenteel alleen in het Engels).

![Een afbeelding van een handgeschreven notitie met geëxtraheerde tekst in de lijst](./Images/handwritten-example.png)

### <a name="printed-text"></a>Afgedrukte tekst

Met de Lees bewerking kan gedrukte tekst in verschillende talen worden uitgepakt.

![Een afbeelding van een Spaans Textbook, waarbij geëxtraheerde tekst wordt weer gegeven](./Images/supported-languages-example.png)

### <a name="mixed-language-documents"></a>Gemengde taal documenten

De Lees-API ondersteunt afbeeldingen en documenten die meerdere talen bevatten, ook wel bekend als gemengde taal documenten. De functie werkt door elke tekst regel in het document in de gedetecteerde taal te classificeren voordat de tekst inhoud wordt geëxtraheerd.

![Een afbeelding van zinsdelen in verschillende talen, met geëxtraheerde tekst in de lijst](./Images/mixed-language-example.png)

## <a name="ocr-api"></a>OCR-API

De [OCR-API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) maakt gebruik van een ouder herkennings model, ondersteunt alleen installatie kopieën en voert synchroon uit, wat direct met de gedetecteerde tekst wordt geretourneerd. Zie de [OCR-ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) en lees vervolgens de API.

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Net als bij alle cognitieve services moeten ontwikkel aars die de Lees/OCR-Services gebruiken, op de hoogte zijn van het micro soft-beleid voor klant gegevens. Zie de pagina Cognitive Services in het [micro soft vertrouwens centrum](https://www.microsoft.com/trust-center/product-overview) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [lees 3,0-rest API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005).
- Meer informatie over de [open bare preview-versie van 3,1 lees rest API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005) met toegevoegde ondersteuning voor vereenvoudigd Chinees.
- Volg de Snelstartgids voor het [uitpakken van tekst](./QuickStarts/CSharp-hand-text.md) voor het implementeren van OCR met C#, Java, java script of python samen met rest API.
