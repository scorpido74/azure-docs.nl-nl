---
title: Tekst lezen uit afbeeldingen en documenten-Computer Vision
titleSuffix: Azure Cognitive Services
description: Concepten met betrekking tot optische teken herkenning (OCR) en tekst van afbeeldingen en documenten voor gedrukte en handgeschreven tekst met behulp van de Computer Vision-API.
services: cognitive-services
author: msbbonsu
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: t-bebon
ms.custom: seodec18
ms.openlocfilehash: 416a7edfdcd7e7915aa7886a8f53cf822b43fe93
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/30/2020
ms.locfileid: "85560810"
---
# <a name="read-text-from-images-and-documents"></a>Tekst van afbeeldingen en documenten lezen

Computer Vision bevat nieuwe, op diep Learning gebaseerde mogelijkheden voor optische teken herkenning (OCR) waarmee gedrukte en handgeschreven tekst uit afbeeldingen en PDF-documenten worden geëxtraheerd. Computer Vision extraheert tekst uit zowel analoge documenten (afbeeldingen, gescande documenten) als gedigitaliseerde documenten. U kunt tekst uit afbeeldingen in het wild extra heren, zoals foto's van een licentie plaat of containers met serie nummers, en van documenten, facturen, facturen, financiële rapporten, artikelen en meer. Deze OCR-functionaliteit is beschikbaar als onderdeel van de beheerde service in de Cloud of on-premises (containers). Het ondersteunt ook virtuele netwerken en persoonlijke eind punten om te voldoen aan de vereisten voor naleving en privacy van uw onderneming.

## <a name="read-api"></a>API lezen 

De [Lees-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) van computer vision is de nieuwste OCR-technologie van micro soft waarmee gedrukte tekst (zeven talen), handgeschreven tekst (alleen Engels), cijfers en valuta symbolen van afbeeldingen en PDF-documenten met meerdere pagina's worden geëxtraheerd. Het is geoptimaliseerd voor het extra heren van tekst van in-the-Wild-tekst zware afbeeldingen en PDF-documenten met meerdere pagina's met gemengde talen. Het biedt ondersteuning voor het detecteren van gedrukte en handgeschreven tekst (alleen Engels) in dezelfde afbeelding of hetzelfde document. U kunt de volledige lijst met ondersteunde talen vinden op de pagina [taal ondersteuning voor computer vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition) .

### <a name="how-it-works"></a>Hoe werkt het?

De [Lees-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) ondersteunt tekst-zware documenten van maxi maal 2000 pagina's en wordt daarom asynchroon uitgevoerd. De eerste stap is het aanroepen van de Lees bewerking. De Lees bewerking neemt een afbeeldings-of PDF-document als invoer en retourneert een bewerkings-ID. 

De tweede stap is het aanroepen van de bewerking [resultaten ophalen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) . Deze bewerking neemt de bewerkings-ID die is gemaakt door de Lees bewerking. Vervolgens wordt de uitgepakte tekst inhoud uit de afbeelding of het document geretourneerd in de vorm van JSON. De JSON-respons houdt de oorspronkelijke regel groeperingen van herkende woorden bij. Het bevat de geëxtraheerde tekst regels en de coördinaten van het begrenzingsvak. Elke tekst regel bevat alle geëxtraheerde woorden met hun coördinaten en een betrouwbaarheids Score.

Als dat nodig is, wordt de rotatie van de herkende pagina gecorrigeerd door de rotatie verschuiving in graden over de horizontale afbeeldings te retour neren, zoals in de volgende afbeelding wordt weer gegeven.

![Een afbeelding die wordt gedraaid en de bijbehorende tekst wordt gelezen en afgebakend](./Images/vision-overview-ocr-read.png)

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

De Lees-API biedt ondersteuning voor het extra heren van gedrukte tekst in het Engels, Spaans, Duits, Frans, Italiaans, Portugees en Nederlandstalige talen. Als uw scenario ondersteuning vereist voor meer talen, raadpleegt u het overzicht van de OCR API in dit document. Raadpleeg de lijst met alle [ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition)

![Een afbeelding die wordt gedraaid en de bijbehorende tekst wordt gelezen en afgebakend](./Images/supported-languages-example.png)

### <a name="mixed-languages-support"></a>Ondersteuning voor gemengde talen

De Lees-API ondersteunt afbeeldingen en documenten met meerdere talen, die vaak worden aangeduid als gemengde taal documenten. Dit doet u door elke tekst regel in het document in de gedetecteerde taal te classificeren voordat u de tekst inhoud uitpakt.

![Een afbeelding die wordt gedraaid en de bijbehorende tekst wordt gelezen en afgebakend](./Images/mixed-language-example.png)

### <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Net als bij alle cognitieve services moeten ontwikkel aars die de Lees service gebruiken op de hoogte zijn van het micro soft-beleid voor klant gegevens. Zie de pagina Cognitive Services in het [micro soft vertrouwens centrum](https://www.microsoft.com/en-us/trust-center/product-overview) voor meer informatie.

### <a name="deploy-on-premises"></a>On-premises implementeren

Lezen is ook beschikbaar als docker-container (preview), zodat u de nieuwe OCR-mogelijkheden kunt implementeren in uw eigen omgeving. Containers zijn geweldig voor specifieke vereisten voor beveiliging en gegevens beheer. Zie [Lees containers installeren en uitvoeren.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)


## <a name="ocr-api"></a>OCR-API

De [OCR-API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) maakt gebruik van een ouder herkennings model, ondersteunt alleen installatie kopieën en voert synchroon uit, wat direct met de gedetecteerde tekst wordt geretourneerd. Het ondersteunt [meer talen](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition) dan een lees-API.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [lees 3,0-rest API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005).
- Volg de Snelstartgids voor het [uitpakken van tekst](./QuickStarts/CSharp-hand-text.md) voor het implementeren van OCR met C#, Java, java script of python samen met rest API.
