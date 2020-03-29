---
title: Een herkenningsmodel opgeven - Gezicht
titleSuffix: Azure Cognitive Services
description: In dit artikel ziet u hoe u kiezen welk herkenningsmodel u wilt gebruiken met uw Azure Face-toepassing.
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: longl
ms.openlocfilehash: da9ad5576d146c007e45124668875e9681860ce6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76938827"
---
# <a name="specify-a-face-recognition-model"></a>Een model voor gezichtsherkenning opgeven

In deze handleiding ziet u hoe u een gezichtsherkenningsmodel opgeeft voor gezichtsherkenning, identificatie en zoeken naar overeenkomsten met behulp van de Azure Face-service.

De Face-service maakt gebruik van machine learning-modellen om bewerkingen uit te voeren op menselijke gezichten in afbeeldingen. We blijven de nauwkeurigheid van onze modellen verbeteren op basis van feedback van klanten en vooruitgang in het onderzoek, en we leveren deze verbeteringen als modelupdates. Ontwikkelaars hebben de mogelijkheid om aan te geven welke versie van het gezichtsherkenningsmodel ze willen gebruiken; ze kunnen het model kiezen dat het beste bij hun use case past.

Als u een nieuwe gebruiker bent, raden we u aan het nieuwste model te gebruiken. Lees verder om te leren hoe u dit opgeven in verschillende Face-bewerkingen terwijl modelconflicten worden vermeden. Als u een gevorderde gebruiker bent en niet zeker weet of u moet overschakelen naar het nieuwste model, gaat u naar de sectie [Verschillende modellen evalueren](#evaluate-different-models) om het nieuwe model te evalueren en resultaten te vergelijken met uw huidige gegevensset.

## <a name="prerequisites"></a>Vereisten

U moet bekend zijn met de concepten van AI gezichtsherkenning en identificatie. Als u dat niet bent, raadpleegt u deze handleidingen eerst:

* [Gezichten in een afbeelding detecteren](HowtoDetectFacesinImage.md)
* [Gezichten in een afbeelding identificeren](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Gezichten detecteren met opgegeven model

Gezichtsherkenning identificeert de visuele oriëntatiepunten van menselijke gezichten en vindt hun grenslocatie. Het haalt ook de functies van het gezicht en slaat ze op voor gebruik in identificatie. Al deze informatie vormt de weergave van één gezicht.

Het herkenningsmodel wordt gebruikt wanneer de gezichtsfuncties worden geëxtraheerd, zodat u een modelversie opgeven bij het uitvoeren van de bewerking Detecteren.

Wanneer u de Face - Detect API `recognitionModel` [gebruikt,] wijst u de modelversie toe met de parameter. De beschikbare waarden zijn:

* `recognition_01`
* `recognition_02`

Optioneel u de parameter _returnRecognitionModel_ (standaard **false)** opgeven om aan te geven of _recognitionModel_ als antwoord moet worden geretourneerd. Dus, een aanvraag URL voor de [Face - Detect] REST API ziet er als volgt uit:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

Als u de clientbibliotheek gebruikt, `recognitionModel` u de waarde toewijzen door een tekenreeks door te geven die de versie vertegenwoordigt.
Als u de toegewezen versie niet hebt toegewezen, wordt de standaardmodelversie _(recognition_01)_ gebruikt. Zie het volgende codevoorbeeld voor de .NET-clientbibliotheek.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Gezichten identificeren met opgegeven model

De Face-service kan gezichtsgegevens uit een afbeelding extraheren en koppelen aan een **object Persoon** (bijvoorbeeld via de API-aanroep Voor [gezicht toevoegen)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) en meerdere **persoonsobjecten** kunnen samen worden opgeslagen in een **persoonsgroep**. Vervolgens kan een nieuw gezicht worden vergeleken met een **persoonsgroep** (met het [gesprek Face - Identify] call) en kan de overeenkomende persoon binnen die groep worden geïdentificeerd.

Een **persoonsgroep** moet één uniek herkenningsmodel hebben voor alle **personen**en u dit opgeven met behulp van de `recognitionModel` parameter wanneer u de groep maakt ([Persoonsgroep - Maken] of [LargePersonGroup - Maken]). Als u deze parameter niet `recognition_01` opgeeft, wordt het oorspronkelijke model gebruikt. Een groep zal altijd het herkenningsmodel gebruiken waarmee het is gemaakt en nieuwe gezichten worden gekoppeld aan dit model wanneer ze eraan worden toegevoegd; dit kan niet worden gewijzigd na de creatie van een groep. Als u wilt zien met welk model een **persoonsgroep** is geconfigureerd, gebruikt u de [PersonGroup - Get] API met de parameter _returnRecognitionModel_ als **true**.

Zie het volgende codevoorbeeld voor de .NET-clientbibliotheek.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

In deze code wordt een `mypersongroupid` **persoonsgroep** met id gemaakt en deze is ingesteld om het _recognition_02-model_ te gebruiken om gezichtsfuncties te extraheren.

Vervolgens moet u opgeven welk model u moet gebruiken bij het detecteren van gezichten om te vergelijken met deze **persoonsgroep** (via de [Api Face - Detect).] Het model dat u gebruikt, moet altijd consistent zijn met de configuratie van de **PersonGroup;** anders zal de bewerking mislukken als gevolg van onverenigbare modellen.

Er is geen verandering in de [Face - Identify] API; u hoeft alleen de modelversie in detectie op te geven.

## <a name="find-similar-faces-with-specified-model"></a>Vergelijkbare gezichten zoeken met opgegeven model

U ook een herkenningsmodel opgeven voor zoeken naar overeenkomsten. U de modelversie toewijzen bij `recognitionModel` het maken van de gezichtslijst met [FaceList - API maken] of [LargeFaceList - Maken]. Als u deze parameter niet `recognition_01` opgeeft, wordt het oorspronkelijke model gebruikt. Een gezichtsherkenningsmodel gebruikt altijd het herkenningsmodel waarmee het is gemaakt en nieuwe gezichten worden gekoppeld aan dit model wanneer ze eraan worden toegevoegd; dit kan niet worden gewijzigd na de schepping. Als u wilt zien met welk model een gezichtslijst is geconfigureerd, gebruikt u de [FaceList - Download] API met de parameter _returnRecognitionModel_ als **true.**

Zie het volgende codevoorbeeld voor de .NET-clientbibliotheek.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

Met deze code wordt `My face collection`een gezichtslijst gemaakt met de naam _recognition_02-model_ voor functieextractie. Wanneer u in deze gezichtslijst zoekt naar vergelijkbare gezichten als een nieuw gedetecteerd gezicht, moet dat gezicht zijn gedetecteerd[(Gezicht - Detecteren)]met behulp van het _recognition_02-model._ Net als in de vorige sectie moet het model consistent zijn.

Er is geen verandering in de [Face - Zoek vergelijkbare] API; u geeft alleen de modelversie op in detectie.

## <a name="verify-faces-with-specified-model"></a>Gezichten verifiëren met opgegeven model

De [Face - Verify] API controleert of twee gezichten van dezelfde persoon zijn. Er is geen wijziging in de API verifiëren met betrekking tot herkenningsmodellen, maar u alleen gezichten vergelijken die met hetzelfde model zijn gedetecteerd. Dus, de twee gezichten zullen beide moeten `recognition_01` `recognition_02`zijn gedetecteerd met behulp van of .

## <a name="evaluate-different-models"></a>Verschillende modellen evalueren

Als u de prestaties van de _recognition_01_ en _recognition_02_ modellen op uw gegevens wilt vergelijken, moet u:

1. Maak twee **PersonGroup**s met respectievelijk _recognition_01_ en _recognition_02._
1. Gebruik uw afbeeldingsgegevens om gezichten te detecteren en deze te registreren **bij Personen**voor deze twee **persongroups**en activeer het trainingsproces met [PersonGroup - Train] API.
1. Test met [Face - Identificeer] op beide **PersonGroup**s en vergelijk de resultaten.

Als u normaal gesproken een betrouwbaarheidsdrempel opgeeft (een waarde tussen nul en een waarde die bepaalt hoe zeker het model moet zijn om een gezicht te identificeren), moet u mogelijk verschillende drempelwaarden voor verschillende modellen gebruiken. Een drempel voor het ene model is niet bedoeld om met een ander model te worden gedeeld en zal niet noodzakelijkerwijs dezelfde resultaten opleveren.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u het herkenningsmodel opgeeft dat moet worden gebruikt met verschillende FACE-service-API's. Volg vervolgens een snelle start om aan de slag te gaan met gezichtsherkenning.

* [Face .NET SDK](../Quickstarts/csharp-sdk.md)
* [Face Python SDK](../Quickstarts/python-sdk.md)
* [Face Go SDK](../Quickstarts/go-sdk.md)

[Face - Detecteren]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Gezicht - Vergelijkbare vinden]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face - Identificeren]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Gezicht - Verifiëren]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Maken]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList - Maken]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
