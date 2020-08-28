---
title: Inktgegevens naar de Ink Recognizer-API verzenden
titleSuffix: Azure Cognitive Services
description: Meer informatie over het aanroepen van de Ink Analyzer-API voor verschillende toepassingen
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: aahi
ms.openlocfilehash: 8665e27b6b7c43bce7d749d9bfe0a2f815a0f46d
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051097"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>Inktgegevens naar de Ink Recognizer-API verzenden 

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

Digital Inking verwijst naar technologieën die digitale representaties mogelijk maken van input zoals handgeschreven tekst en tekeningen. Dit wordt doorgaans gerealiseerd met een digitizer waarmee de bewegingen van invoer apparaten, zoals een pen, worden vastgelegd. Omdat apparaten rijke digitale inktwaarneming mogelijk maken, kunnen kunstmatige intelligentie en machine learning geschreven vormen en tekst in vrijwel elke context herkennen. Met de API voor inkt herkenning kunt u inkt streken verzenden en gedetailleerde informatie hierover ophalen. 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>De API voor inkt herkenning versus OCR-Services

De inkt Recognizer-API maakt geen gebruik van optische teken herkenning (OCR). OCR-Services verwerken de pixel gegevens van afbeeldingen om te voorzien in hand schrift-en tekst herkenning. Dit wordt ook wel offline herkenning genoemd. In plaats daarvan vereist de inkt Recognizer-API digitale inkt lijn gegevens die zijn vastgelegd als het invoer apparaat wordt gebruikt. Door digitale inkt gegevens op deze manier te verwerken, kunnen nauw keurige herkennings resultaten worden geproduceerd vergeleken met de OCR-Services. 

## <a name="sending-ink-data"></a>Inkt gegevens verzenden

De inkt Recognizer-API vereist de X-en Y-coördinaten die de door een invoer apparaat gemaakte inkt streken vertegenwoordigen, vanaf het moment dat deze het detectie-Opper vlak aanraakt wanneer het is opgeheven. De punten van elke lijn moeten een teken reeks met door komma's gescheiden waarden zijn en moeten worden ingedeeld in JSON, zoals in het onderstaande voor beeld. Daarnaast moet elke inkt streek een unieke ID in elke aanvraag hebben. Als de ID wordt herhaald binnen dezelfde aanvraag, wordt een fout geretourneerd door de API. Voor de meest nauw keurige herkennings resultaten moet u ten minste acht cijfers achter het decimaal teken hebben. De oorsprong (0,0) van het canvas wordt verondersteld de linkerbovenhoek van het canvas voor inkt te zijn.

> [!NOTE]
> Het volgende voor beeld is geen geldige JSON. U kunt een volledige inkt Recognizer JSON-aanvraag vinden op [github](https://go.microsoft.com/fwlink/?linkid=2089909).
 
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

## <a name="ink-recognizer-response"></a>Reactie van inkt herkenning

De inkt Recognizer-API retourneert een analyse reactie op de objecten die worden herkend vanuit de inhoud van de inkt. Het antwoord bevat herkennings eenheden die de relaties tussen verschillende inkt streken beschrijven. Bijvoorbeeld, streken die afzonderlijke vormen maken, worden in verschillende eenheden opgenomen. Elke eenheid bevat gedetailleerde informatie over de inkt streken met inbegrip van het herkende object, de bijbehorende coördinaten en andere teken kenmerken.

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>Vormen die worden herkend door de API voor inkt herkenning

Met de API voor inkt herkenning kunt u de meest gebruikte vormen identificeren bij het maken van notities. In de onderstaande afbeelding ziet u enkele eenvoudige voor beelden. Zie het [naslag artikel over API](https://go.microsoft.com/fwlink/?linkid=2089907)voor een volledige lijst met shapes en andere inkt inhoud die wordt herkend door de API. 

![De lijst met vormen die worden herkend door de API voor inkt herkenning](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>Aanbevolen aanroep patronen

U kunt de inkt Recognizer REST API in verschillende patronen aanroepen volgens uw toepassing. 

### <a name="user-initiated-api-calls"></a>Door de gebruiker geïnitieerde API-aanroepen

Als u een app bouwt die gebruikers invoer uitvoert (bijvoorbeeld een notitie-of annotatie-app), wilt u deze mogelijk laten bepalen wanneer en welke inkt wordt verzonden naar de inkt Recognizer-API. Deze functionaliteit is vooral nuttig wanneer tekst en vormen aanwezig zijn op het canvas en gebruikers verschillende acties voor elke actie willen uitvoeren. U kunt selectie functies toevoegen (zoals een lasso of andere geometrische selectie hulpmiddel) waarmee gebruikers kunnen kiezen wat er naar de API wordt verzonden.  

### <a name="app-initiated-api-calls"></a>Door app geïnitieerde API-aanroepen

U kunt uw app ook de API voor inkt herkenning aanroepen na een time-out. Door de huidige inkt streken naar de API-routine te verzenden, kunt u herkennings resultaten opslaan terwijl ze zijn gemaakt tijdens het verbeteren van de reactie tijd van de API. U kunt bijvoorbeeld een regel handgeschreven tekst verzenden naar de API nadat u hebt gedetecteerd dat de gebruiker deze heeft voltooid. 

Met de herkennings resultaten vooraf vindt u informatie over de kenmerken van inkt streken die met elkaar in verband staan. Bijvoorbeeld, welke streken zijn gegroepeerd om hetzelfde woord, lijn, lijst, alinea of vorm te vormen. Met deze informatie kunt u de functies van de inkt selectie van uw app verbeteren door bijvoorbeeld groepen streken tegelijk te selecteren.

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>De inkt Recognizer-API integreren met Windows Ink

[Windows Ink](https://docs.microsoft.com/windows/uwp/design/input/pen-and-stylus-interactions) bevat hulpprogram ma's en technologieën om digitale inkt ervaringen op diverse apparaten mogelijk te maken. U kunt het Windows-inkt platform combi neren met de API voor inkt herkenning om toepassingen te maken die digitale inkt streken weer geven en interpreteren.

## <a name="next-steps"></a>Volgende stappen

* [Wat is de Ink Recognizer-API?](../overview.md)
* [Naslag informatie over REST API van inkt herkenning](https://go.microsoft.com/fwlink/?linkid=2089907)

* Begin met het verzenden van digitale inkt gegevens met behulp van:
    * [C#](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [JavaScript](../quickstarts/javascript.md)
