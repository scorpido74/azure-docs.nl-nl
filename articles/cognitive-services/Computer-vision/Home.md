---
title: Wat is Computer Vision? - Computer Vision
titleSuffix: Azure Cognitive Services
description: De Computer Vision-service geeft ontwikkelaars toegang tot geavanceerde algoritmen voor het verwerken van afbeeldingen en het retourneren van informatie.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 05/27/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 90c2068021f1e9a173f37bdf1098edda10ab3d8d
ms.sourcegitcommit: fdaad48994bdb9e35cdd445c31b4bac0dd006294
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85413464"
---
# <a name="what-is-computer-vision"></a>Wat is Computer Vision?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

De Computer Vision service van Azure biedt ontwikkel aars toegang tot geavanceerde algoritmen waarmee afbeeldingen worden verwerkt en gegevens worden geretourneerd op basis van de visuele functies die u wilt gebruiken. Computer Vision kunt bijvoorbeeld bepalen of een afbeelding inhoud voor volwassenen bevat, specifieke merken of objecten zoeken, of mensen zoeken.

U kunt Computer Vision in uw toepassing gebruiken via een SDK van de client bibliotheek of door de REST API rechtstreeks aan te roepen. Deze pagina laat grosso modo zien wat u kunt doen met Computer Vision.

## <a name="computer-vision-for-digital-asset-management"></a>Computer Vision voor beheer van digitale activa

Computer Vision kunt veel software voor het beheer van digitale activa (moeder) ondersteunen. DAM is het bedrijfs proces van het organiseren, opslaan en ophalen van uitgebreide media-assets en het beheren van digitale rechten en machtigingen. Het is bijvoorbeeld mogelijk dat een bedrijf afbeeldingen wil groeperen en identificeren op basis van zicht bare logo's, gezichten, objecten, kleuren, enzovoort. Het is ook mogelijk dat u automatisch [bijschriften voor afbeeldingen wilt genereren](./Tutorials/storage-lab-tutorial.md) en tref woorden wilt toevoegen zodat ze kunnen worden doorzocht. Zie voor een alles-in-één moeder oplossing die gebruikmaakt van Cognitive Services, Azure Cognitive Search en intelligent Reporting de [hand leiding voor de oplossing voor kennis analyse](https://github.com/Azure-Samples/azure-search-knowledge-mining) op github. Raadpleeg de opslag plaats van de [Computer Vision-oplossing](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates) voor andere voor beelden van moeders.

## <a name="analyze-images-for-insight"></a>Analyseren van afbeeldingen voor inzicht

U kunt afbeeldingen analyseren om inzicht te krijgen in de visuele functies en kenmerken. Alle functies in de onderstaande tabel worden geleverd door de [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API.

| Bewerking | Beschrijving |
| ------ | ----------- |
|**[Visuele kenmerken taggen](concept-tagging-images.md)**|Identificeer en tag visuele kenmerken in een afbeelding op basis van een set met duizenden herkenbare objecten, levende wezens, landschappen en acties. Wanneer de Tags ambigu of niet algemeen bekend zijn, biedt de API-reactie hints om de context van de tag te verduidelijken. U kunt tagging niet alleen gebruiken voor het hoofdonderwerp, zoals een persoon op de voorgrond, maar ook voor de omgeving (binnen of buiten), meubels, gereedschap, planten, dieren, accessoires, gadgets en enzovoort.|
|**[Objecten detecteren](concept-object-detection.md)**| Objectdetectie is vergelijkbaar met het gebruik van tags, maar de API retourneert de coördinaten van de omsluitende box voor elke tag die wordt toegepast. Als een afbeelding bijvoorbeeld een hond, kat en persoon bevat, zal de detectiebewerking deze objecten vermelden, samen met hun coördinaten in de afbeelding. U kunt deze functie gebruiken om verdere relaties tussen de objecten in een afbeelding te verwerken. Ook weet u daardoor wanneer er meerdere exemplaren van dezelfde tag in een afbeelding voorkomen.|
|**[Merken detecteren](concept-brand-detection.md)**|Identificeer commerciële merken in afbeeldingen of video's met behulp van een database met duizenden logo's. U kunt deze functie bijvoorbeeld gebruiken om te ontdekken welke merken het populairst zijn op sociale media of het meest voorkomen in productplaatsing in de media.|
|**[Een afbeelding categoriseren](concept-categorizing-images.md)**|Identificeer en categoriseer een volledige afbeelding met behulp van een [categorietaxonomie](Category-Taxonomy.md) met bovenliggende/onderliggende erfelijke hiërarchieën. Categorieën kunnen zelfstandig worden gebruikt of met onze nieuwe tagmodellen.<br/>Engels is momenteel de enige ondersteunde taal voor het taggen en categoriseren van afbeeldingen.|
|**[Een afbeelding beschrijven](concept-describing-images.md)**|Genereer een beschrijving van een volledige afbeelding in leesbare taal met behulp van volledige zinnen. Met de algoritmen van Computer Vision worden verschillende beschrijvingen gegenereerd op basis van de objecten die zijn geïdentificeerd in de afbeelding. De beschrijvingen worden geëvalueerd en hiervoor wordt een betrouwbaarheidsscore gegenereerd. Vervolgens wordt er een lijst geretourneerd die is geordend van de hoogste naar de laagste betrouwbaarheidsscore.|
|**[Gezichten detecteren](concept-detecting-faces.md)** |Detecteer gezichten in een afbeelding en geef informatie op over elk gedetecteerd gezicht. Met Computer Vision worden de coördinaten, de rechthoek, het geslacht en de leeftijd van elk gedetecteerd gezicht geretourneerd.<br/>Computer Vision biedt een subset van de functionaliteit van het [gezichts](/azure/cognitive-services/face/) service. U kunt de face-service gebruiken voor een gedetailleerdere analyse, zoals gezichts identificatie en detectie.|
|**[Afbeeldingstypen detecteren](concept-detecting-image-types.md)**|Detecteer kenmerken van een afbeelding, bijvoorbeeld of een afbeelding een lijntekening of een illustratie is.|
|**[Domeinspecifieke inhoud detecteren](concept-detecting-domain-content.md)**|Gebruik domeinmodellen om domeinspecifieke inhoud in een afbeelding te detecteren en te identificeren, zoals beroemdheden en oriëntatiepunten. Als een afbeelding bijvoorbeeld personen bevat, kan Computer Vision een domein model voor beroemdheden gebruiken om te bepalen of de personen die in de installatie kopie worden gedetecteerd, bekend zijn met beroemdheden.|
|**[Het kleurenschema detecteren](concept-detecting-color-schemes.md)**|Analyseer het kleurgebruik in een afbeelding. Met Computer Vision kan worden bepaald of een afbeelding zwart-wit of kleur is en voor kleurenafbeeldingen kunnen de dominante kleuren en accentkleuren worden geïdentificeerd.|
|**[Een miniatuur genereren](concept-generating-thumbnails.md)**|Analyseer de inhoud van een afbeelding om een geschikte miniatuur voor deze afbeelding te genereren. Computer Vision genereert eerst een miniatuur van hoge kwaliteit, waarna de objecten in de afbeelding worden geanalyseerd om het *interessegebied* te bepalen. Computer Vision past de afbeelding vervolgens aan de vereisten van het interessegebied aan. De gegenereerde miniatuur kan worden weergegeven met een andere hoogte-breedteverhouding dan wordt gebruikt in de oorspronkelijke afbeelding, afhankelijk van uw behoeften.|
|**[Interessegebied ophalen](concept-generating-thumbnails.md#area-of-interest)**|Analyseer de inhoud van een afbeelding om de coördinaten van het *interessegebied* te retourneren. In plaats van de afbeelding te bijsnijden en een miniatuur te genereren, Computer Vision de coördinaten van het selectie kader van de regio als resultaat gegeven, zodat de aanroepende toepassing de oorspronkelijke afbeelding kan wijzigen naar wens.|

## <a name="extract-text-from-images"></a>Extraheren van tekst uit afbeeldingen

U kunt de Computer Vision [Lees](concept-recognizing-text.md#read-api) -API gebruiken om gedrukte en handgeschreven tekst uit afbeeldingen te extra heren in een door een machine Lees bare teken stroom. De Lees-API maakt gebruik van de nieuwste modellen en werkt met tekst op verschillende Opper vlakken en achtergronden, zoals bevestigingen, posters, visite kaartjes, brieven en White boards. Het werkt op dit moment voor zeven verschillende talen (Zie [taal ondersteuning](./language-support.md)).

U kunt ook de [OCR-API (Optical Character Recognition)](concept-recognizing-text.md) gebruiken om gedrukte tekst in verschillende talen te extra heren. Via OCR wordt zo nodig de draaiing van de herkende tekst gecorrigeerd en worden de framecoördinaten van elk woord weergegeven. OCR ondersteunt 25 talen en detecteert automatisch de taal van de herkende tekst.

## <a name="moderate-content-in-images"></a>Beheren van inhoud in afbeeldingen

U kunt Computer Vision gebruiken om [inhoud voor volwassenen te detecteren](concept-detecting-adult-content.md) in een afbeelding en betrouwbaarheids scores voor verschillende classificaties te retour neren. De drempel waarde voor het markeren van inhoud kan worden ingesteld op een schuif schaal om uw voor keuren aan te passen.

## <a name="use-containers"></a>Containers gebruiken

[Gebruik computer vision containers](computer-vision-how-to-install-containers.md) om gedrukte en handgeschreven tekst lokaal te herkennen door een gestandaardiseerde docker-container dichter bij uw gegevens te installeren.

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

- [Snelstartgids: Computer Vision .NET-client bibliotheek](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
- [Snelstartgids: Computer Vision python-client bibliotheek](./quickstarts-sdk/client-library.md?pivots=programming-language-python)
- [Snelstartgids: Computer Vision Java-client bibliotheek](./quickstarts-sdk/client-library.md?pivots=programming-language-java)
