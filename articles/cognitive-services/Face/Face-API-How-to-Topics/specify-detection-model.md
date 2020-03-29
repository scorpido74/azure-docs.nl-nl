---
title: Een detectiemodel opgeven - Gezicht
titleSuffix: Azure Cognitive Services
description: In dit artikel ziet u hoe u kiezen welk gezichtsherkenningsmodel u wilt gebruiken met uw Azure Face-toepassing.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: 40ca1dbf981c5a9025cf5a0bac6b007709d69a77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76934577"
---
# <a name="specify-a-face-detection-model"></a>Een model voor gezichtsdetectie opgeven

In deze handleiding ziet u hoe u een gezichtsherkenningsmodel opgeeft voor de Azure Face-service.

De Face-service maakt gebruik van machine learning-modellen om bewerkingen uit te voeren op menselijke gezichten in afbeeldingen. We blijven de nauwkeurigheid van onze modellen verbeteren op basis van feedback van klanten en vooruitgang in het onderzoek, en we leveren deze verbeteringen als modelupdates. Ontwikkelaars hebben de mogelijkheid om aan te geven welke versie van het gezichtsherkenningsmodel ze willen gebruiken; ze kunnen het model kiezen dat het beste bij hun use case past.

Lees verder om te leren hoe u het gezichtsherkenningsmodel in bepaalde gezichtsbewerkingen opgeven. De Face-service maakt gebruik van gezichtsherkenning wanneer een afbeelding van een gezicht wordt omgezet in een andere vorm van gegevens.

Als u niet zeker weet of u het nieuwste model moet gebruiken, gaat u naar de sectie [Verschillende modellen evalueren](#evaluate-different-models) om het nieuwe model te evalueren en resultaten te vergelijken met uw huidige gegevensset.

## <a name="prerequisites"></a>Vereisten

U moet bekend zijn met het concept van AI gezichtsherkenning. Als u dat niet bent, raadpleegt u de conceptuele handleiding voor gezichtsherkenning of handleiding:

* [Concepten voor gezichtsherkenning](../concepts/face-detection.md)
* [Gezichten in een afbeelding detecteren](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Gezichten detecteren met opgegeven model

Gezichtsherkenning vindt de grenslocaties van menselijke gezichten en identificeert hun visuele oriëntatiepunten. Het haalt de functies van het gezicht en [recognition](../concepts/face-recognition.md) slaat ze op voor later gebruik in herkenningsbewerkingen.

Wanneer u de Face - Detect API [gebruikt,] `detectionModel` kunt u de modelversie toewijzen aan de parameter. De beschikbare waarden zijn:

* `detection_01`
* `detection_02`

Een aanvraag-URL voor de [Face - Detect] REST API ziet er als volgt uit:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

Als u de clientbibliotheek gebruikt, `detectionModel` u de waarde toewijzen door een geschikte tekenreeks door te geven. Als u de toegewezen waarde niet hebt toegewezen,`detection_01`gebruikt de API de standaardmodelversie ( ). Zie het volgende codevoorbeeld voor de .NET-clientbibliotheek.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Gezicht toevoegen aan Persoon met opgegeven model

De Face-service kan gezichtsgegevens uit een afbeelding extraheren en koppelen aan een **object Persoon** via de PersonGroup Person - Face API [toevoegen.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) In deze API-aanroep u het detectiemodel op dezelfde manier opgeven als in [Face - Detect].

Zie het volgende codevoorbeeld voor de .NET-clientbibliotheek.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Met deze code wordt `mypersongroupid` een **persoonsgroep** met id's aanmaken en wordt er een **persoon** aan toegevoegd. Vervolgens voegt het een **Person** gezicht `detection_02` toe aan deze persoon met behulp van het model. Als u de parameter *detectionModel* niet opgeeft, gebruikt `detection_01`de API het standaardmodel.

> [!NOTE]
> U hoeft niet hetzelfde detectiemodel te gebruiken voor alle gezichten in een **persoonsobject** en u hoeft niet hetzelfde detectiemodel te gebruiken bij het detecteren van nieuwe gezichten om te vergelijken met een **object Persoon** (bijvoorbeeld in de API Face [- Identify] API).

## <a name="add-face-to-facelist-with-specified-model"></a>Gezicht toevoegen aan FaceList met opgegeven model

U ook een detectiemodel opgeven wanneer u een gezicht toevoegt aan een bestaand **FaceList-object.** Zie het volgende codevoorbeeld voor de .NET-clientbibliotheek.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Deze code maakt een `My face collection` **FaceList** aangeroepen en `detection_02` voegt er een Face aan toe met het model. Als u de parameter *detectionModel* niet opgeeft, gebruikt `detection_01`de API het standaardmodel.

> [!NOTE]
> U hoeft niet hetzelfde detectiemodel te gebruiken voor alle gezichten in een **FaceList-object** en u hoeft niet hetzelfde detectiemodel te gebruiken bij het detecteren van nieuwe gezichten om te vergelijken met een **FaceList-object.**

## <a name="evaluate-different-models"></a>Verschillende modellen evalueren

De verschillende gezichtsherkenningsmodellen zijn geoptimaliseerd voor verschillende taken. Zie de volgende tabel voor een overzicht van de verschillen.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Standaardkeuze voor alle gezichtsherkenningsbewerkingen. | Uitgebracht in mei 2019 en optioneel verkrijgbaar in alle gezichtsherkenningsbewerkingen.
|Niet geoptimaliseerd voor kleine, zijwaartse of wazige gezichten.  | Verbeterde nauwkeurigheid op kleine, zijaanzicht en wazige gezichten. |
|Retourneert gezichtskenmerken (hoofdpose, leeftijd, emotie, enzovoort) als ze zijn opgegeven in de detectie-oproep. |  Geeft geen gezichtskenmerken terug.     |
|Retourneert als ze zijn opgegeven in de detectieoproep.   | Keert niet terug gezicht oriëntatiepunten.  |

De beste manier om de `detection_01` `detection_02` prestaties van de en modellen te vergelijken is om ze te gebruiken op een voorbeeld dataset. We raden u aan de [Face - Detect] API te bellen op een verscheidenheid aan afbeeldingen, met name afbeeldingen van vele gezichten of gezichten die moeilijk te zien zijn, met behulp van elk detectiemodel. Besteed aandacht aan het aantal gezichten dat elk model retourneert.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u het detectiemodel opgeven dat moet worden gebruikt met verschillende Face API's. Volg vervolgens een snelle start om aan de slag te gaan met gezichtsherkenning.

* [Face .NET SDK](../Quickstarts/csharp-sdk.md)
* [Face Python SDK](../Quickstarts/python-sdk.md)
* [Face Go SDK](../Quickstarts/go-sdk.md)

[Face - Detecteren]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face - Identificeren]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[FaceList - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
