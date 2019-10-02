---
title: Wat is de Computer Vision-API? - Computer Vision
titleSuffix: Azure Cognitive Services
description: De Computer Vision-service geeft ontwikkelaars toegang tot geavanceerde algoritmen voor het verwerken van afbeeldingen en het retourneren van informatie.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: db62f003f7eb8dd272305b86712cf1d721797114
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719545"
---
# <a name="what-is-computer-vision"></a>Wat is Computer Vision?

De Computer Vision-service van Azure geeft ontwikkelaars toegang tot geavanceerde algoritmen voor het verwerken van afbeeldingen en het retourneren van informatie. Voor het analyseren van een afbeelding kunt u een afbeeldingsindeling uploaden of een afbeeldings-URL opgeven. Met de algoritmen voor het verwerken van afbeeldingen kunt u de inhoud van een afbeelding op verschillende manieren analyseren, afhankelijk van de visuele kenmerken waarin u bent geïnteresseerd. Computer Vision kan bijvoorbeeld bepalen of een afbeelding inhoud voor volwassenen bevat of alle menselijke gezichten in een afbeelding vinden.

U kunt Computer Vision in uw toepassing gebruiken met behulp van een systeemeigen SDK of door de REST-API rechtstreeks aan te roepen. Deze pagina laat grosso modo zien wat u kunt doen met Computer Vision.

## <a name="analyze-images-for-insight"></a>Analyseren van afbeeldingen voor inzicht

U kunt afbeeldingen analyseren om inzicht te krijgen in de visuele kenmerken en eigenschappen van die afbeeldingen. Alle functies in de onderstaande tabel worden geleverd door de [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API.

| Action | Description |
| ------ | ----------- |
|**[Visuele kenmerken taggen](concept-tagging-images.md)**|Identificeer en tag visuele kenmerken in een afbeelding op basis van een set met duizenden herkenbare objecten, levende wezens, landschappen en acties. Wanneer de Tags ambigu of niet algemeen bekend zijn, biedt de API-reactie hints om de context van de tag te verduidelijken. U kunt tagging niet alleen gebruiken voor het hoofdonderwerp, zoals een persoon op de voorgrond, maar ook voor de omgeving (binnen of buiten), meubels, gereedschap, planten, dieren, accessoires, gadgets en enzovoort.|
|**[Objecten detecteren](concept-object-detection.md)**| Objectdetectie is vergelijkbaar met het gebruik van tags, maar de API retourneert de coördinaten van de omsluitende box voor elke tag die wordt toegepast. Als een afbeelding bijvoorbeeld een hond, kat en persoon bevat, zal de detectiebewerking deze objecten vermelden, samen met hun coördinaten in de afbeelding. U kunt deze functie gebruiken om verdere relaties tussen de objecten in een afbeelding te verwerken. Ook weet u daardoor wanneer er meerdere exemplaren van dezelfde tag in een afbeelding voorkomen.|
|**[Merken detecteren](concept-brand-detection.md)**|Identificeer commerciële merken in afbeeldingen of video's met behulp van een database met duizenden logo's. U kunt deze functie bijvoorbeeld gebruiken om te ontdekken welke merken het populairst zijn op sociale media of het meest voorkomen in productplaatsing in de media.|
|**[Een afbeelding categoriseren](concept-categorizing-images.md)**|Identificeer en categoriseer een volledige afbeelding met behulp van een [categorietaxonomie](Category-Taxonomy.md) met bovenliggende/onderliggende erfelijke hiërarchieën. Categorieën kunnen zelfstandig worden gebruikt of met onze nieuwe tagmodellen.<br/>Engels is momenteel de enige ondersteunde taal voor het taggen en categoriseren van afbeeldingen.|
|**[Een afbeelding beschrijven](concept-describing-images.md)**|Genereer een beschrijving van een volledige afbeelding in leesbare taal met behulp van volledige zinnen. Met de algoritmen van Computer Vision worden verschillende beschrijvingen gegenereerd op basis van de objecten die zijn geïdentificeerd in de afbeelding. De beschrijvingen worden geëvalueerd en hiervoor wordt een betrouwbaarheidsscore gegenereerd. Vervolgens wordt er een lijst geretourneerd die is geordend van de hoogste naar de laagste betrouwbaarheidsscore.|
|**[Gezichten detecteren](concept-detecting-faces.md)** |Detecteer gezichten in een afbeelding en geef informatie op over elk gedetecteerd gezicht. Met Computer Vision worden de coördinaten, de rechthoek, het geslacht en de leeftijd van elk gedetecteerd gezicht geretourneerd.<br/>Computer Vision biedt een subset van de [](/azure/cognitive-services/face/) functionaliteit van het gezichts service. U kunt de face-service gebruiken voor een gedetailleerdere analyse, zoals gezichts identificatie en detectie.|
|**[Afbeeldingstypen detecteren](concept-detecting-image-types.md)**|Detecteer kenmerken van een afbeelding, bijvoorbeeld of een afbeelding een lijntekening of een illustratie is.|
|**[Domeinspecifieke inhoud detecteren](concept-detecting-domain-content.md)**|Gebruik domeinmodellen om domeinspecifieke inhoud in een afbeelding te detecteren en te identificeren, zoals beroemdheden en oriëntatiepunten. Als een afbeelding bijvoorbeeld personen bevat, kan Computer Vision een domein model voor beroemdheden gebruiken om te bepalen of de personen die in de installatie kopie worden gedetecteerd, bekend zijn met beroemdheden.|
|**[Het kleurenschema detecteren](concept-detecting-color-schemes.md)**|Analyseer het kleurgebruik in een afbeelding. Met Computer Vision kan worden bepaald of een afbeelding zwart-wit of kleur is en voor kleurenafbeeldingen kunnen de dominante kleuren en accentkleuren worden geïdentificeerd.|
|**[Een miniatuur genereren](concept-generating-thumbnails.md)**|Analyseer de inhoud van een afbeelding om een geschikte miniatuur voor deze afbeelding te genereren. Computer Vision genereert eerst een miniatuur van hoge kwaliteit, waarna de objecten in de afbeelding worden geanalyseerd om het *interessegebied* te bepalen. Computer Vision past de afbeelding vervolgens aan de vereisten van het interessegebied aan. De gegenereerde miniatuur kan worden weergegeven met een andere hoogte-breedteverhouding dan wordt gebruikt in de oorspronkelijke afbeelding, afhankelijk van uw behoeften.|
|**[Interessegebied ophalen](concept-generating-thumbnails.md#area-of-interest)**|Analyseer de inhoud van een afbeelding om de coördinaten van het *interessegebied* te retourneren. In plaats van de afbeelding te bijsnijden en een miniatuur te genereren, Computer Vision de coördinaten van het selectie kader van de regio als resultaat gegeven, zodat de aanroepende toepassing de oorspronkelijke afbeelding kan wijzigen naar wens.|

## <a name="extract-text-from-images"></a>Extraheren van tekst uit afbeeldingen

U kunt Computer Vision [Lees-API](concept-recognizing-text.md#read-api) gebruiken om gedrukte en handgeschreven tekst uit afbeeldingen te extra heren in een door een machine Lees bare teken stroom. De Lees-API maakt gebruik van onze nieuwste modellen en werkt met tekst op verschillende Opper vlakken en achtergronden, zoals bevestigingen, posters, visite kaartjes, brieven en White boards. Engels is momenteel de enige ondersteunde taal.

U kunt ook de [OCR-API (Optical Character Recognition)](concept-recognizing-text.md#ocr-optical-character-recognition-api) gebruiken om gedrukte tekst in verschillende talen te extra heren. Via OCR wordt zo nodig de draaiing van de herkende tekst gecorrigeerd en worden de framecoördinaten van elk woord weergegeven. OCR ondersteunt 25 talen en detecteert automatisch de taal van de herkende tekst.



## <a name="moderate-content-in-images"></a>Beheren van inhoud in afbeeldingen

U kunt Computer Vision gebruiken om [inhoud voor volwassenen te detecteren](concept-detecting-adult-content.md) in een afbeelding en betrouwbaarheids scores voor verschillende classificaties te retour neren. De drempel waarde voor het markeren van inhoud kan worden ingesteld op een schuif schaal om uw voor keuren aan te passen.

## <a name="use-containers"></a>Containers gebruiken

[Gebruik Computer Vision-containers](computer-vision-how-to-install-containers.md) voor het lokaal herkennen van gedrukte en handgeschreven tekst door het installeren van een gestandaardiseerde Docker-container die zich dichter bij uw gegevens bevindt.

## <a name="image-requirements"></a>Vereisten voor installatiekopieën

Met Computer Vision kunt u afbeeldingen analyseren die voldoen aan de volgende vereisten:

- De afbeelding moet worden weergegeven in de JPEG-, PNG-, GIF- of BMP-indeling
- De afbeelding moet kleiner zijn dan 4 megabyte (MB)
- De afmetingen van de afbeelding moet groter zijn dan 50 x 50 pixels
  - Voor de Lees-API moet de afmetingen van de afbeelding tussen 50 x 50 en 10000 x 10000 pixels liggen.

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Zoals geldt voor alle Cognitive Services, dienen ontwikkelaars die de Computer Vision-service gebruiken op de hoogte te zijn van het beleid van Microsoft inzake klantgegevens. Zie de [pagina Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) (Engelstalig) in het Microsoft Trust Center voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Aan de slag met Computer Vision door een quickstart te volgen:

- [Snelstart: Computer Vision .NET SDK](quickstarts-sdk/csharp-sdk.md)
- [Snelstart: Computer Vision python-SDK](quickstarts-sdk/python-sdk.md)
- [Snelstart: Computer Vision Java-SDK](quickstarts-sdk/java-sdk.md)
