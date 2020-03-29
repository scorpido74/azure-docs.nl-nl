---
title: Beeldcategorisering - Computer Vision
titleSuffix: Azure Cognitive Services
description: Leer concepten met betrekking tot de functie voor het categoriseren van afbeeldingen van de Computer Vision API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 477349f1addf71a30e8ecb179266d8eac5510887
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244747"
---
# <a name="categorize-images-by-subject-matter"></a>Afbeeldingen categoriseren op onderwerp

Naast tags en een beschrijving geeft Computer Vision de op taxonomie gebaseerde categorieën als resultaat die in een afbeelding zijn gedetecteerd. In tegenstelling tot tags worden categorieën georganiseerd in een bovenliggende/onderliggende erfelijke hiërarchie, en er zijn er minder (86, in tegenstelling tot duizenden tags). Alle categorienamen zijn in het Engels. Categoriseren kan op zichzelf of naast het nieuwere tagsmodel.

## <a name="the-86-category-concept"></a>Het concept van de 86 categorieën

Computer vision kan een afbeelding breed of specifiek categoriseren, met behulp van de lijst van 86 categorieën in het volgende diagram. Zie [Categorietaxonomie](category-taxonomy.md) voor de volledige taxonomie in tekstindeling.

![Gegroepeerde lijsten van alle categorieën in de categorie taxonomie](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Voorbeelden van afbeeldingscategorisatie

De volgende JSON-respons illustreert wat Computer Vision retourneert bij het categoriseren van de voorbeeldafbeelding op basis van de visuele kenmerken.

![Een vrouw op het dak van een flatgebouw](./Images/woman_roof.png)

```json
{
    "categories": [
        {
            "name": "people_",
            "score": 0.81640625
        }
    ],
    "requestId": "bae7f76a-1cc7-4479-8d29-48a694974705",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

In de volgende tabel wordt een typische afbeeldingsset weergegeven en de categorie die door Computer Vision voor elke afbeelding wordt geretourneerd.

| Installatiekopie | Categorie |
|-------|----------|
| ![Vier mensen stelden samen als familie voor](./Images/family_photo.png) | people_group |
| ![Een puppyzitting op een grasrijk gebied](./Images/cute_dog.png) | animal_dog |
| ![Een persoon die zich op een bergrots bij zonsondergang bevindt](./Images/mountain_vista.png) | outdoor_mountain |
| ![Een stapel van broodrollen op een lijst](./Images/bread.png) | food_bread |

## <a name="use-the-api"></a>De API gebruiken

De categorisatiefunctie maakt deel uit van de API Voor het analyseren van [afbeelding.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) U deze API aanroepen via een native SDK of via REST-oproepen. Opnemen `Categories` in de parameter **visualFeatures** query. Wanneer u vervolgens de volledige JSON-respons krijgt, ontleden `"categories"` u gewoon de tekenreeks voor de inhoud van de sectie.

* [Snelstart: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Snelstart: een afbeelding analyseren (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>Volgende stappen

Leer de bijbehorende concepten van [het taggen](concept-tagging-images.md) van afbeeldingen en [het beschrijven van afbeeldingen.](concept-describing-images.md)
