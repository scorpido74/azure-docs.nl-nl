---
title: Concepten voor beoordelingen, werkstromen en vacatures - Inhoudsmoderator
titleSuffix: Azure Cognitive Services
description: In dit artikel leert u over de kernconcepten van de reviewtool; beoordelingen, werkstromen en taken.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 1aba86efb9ea76fbf060e80b47f9f2f6cdf8ee71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221148"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Beoordelingen van inhoudsbeheer, werkstromen en taken

Content Moderator combineert machine-ondersteunde moderatie met human-in-the-loop mogelijkheden om een optimaal moderatieproces te creëren voor real-world scenario's. Het doet dit via de cloud-based [Review tool](https://contentmoderator.cognitive.microsoft.com). In deze handleiding leert u over de kernconcepten van de beoordelingstool: beoordelingen, workflows en taken.

## <a name="reviews"></a>Beoordelingen

In een beoordeling wordt inhoud geüpload naar het gereedschap Controleren en wordt deze weergegeven onder het tabblad **Controleren.** Vanaf hier kunnen gebruikers de toegepaste tags wijzigen en hun eigen aangepaste tags toepassen. Wanneer een gebruiker een beoordeling indient, worden de resultaten naar een opgegeven callback-eindpunt verzonden en wordt de inhoud van de site verwijderd.

![Website van beoordelingsprogramma's geopend in een browser op het tabblad Controleren](./Review-Tool-user-Guide/images/image-workflow-review.png)

Bekijk de [handleiding voor het hulpprogramma Controleren](./review-tool-user-guide/review-moderated-images.md) om aan de slag te gaan met het maken van beoordelingen of bekijk de [API-handleiding voor](./try-review-api-review.md) rust om te leren hoe u dit programmatisch doen.

## <a name="workflows"></a>Werkstromen

Een werkstroom is een op de cloud gebaseerd aangepast filter voor inhoud. Werkstromen kunnen verbinding maken met verschillende services om inhoud op verschillende manieren te filteren en vervolgens de juiste actie te ondernemen. Met de contentmoderatorconnector kan een werkstroom automatisch moderatietags toepassen en beoordelingen maken met ingediende inhoud.

### <a name="view-workflows"></a>Werkstromen weergeven

Als u uw bestaande werkstromen wilt weergeven, gaat u naar het [gereedschap Controleren](https://contentmoderator.cognitive.microsoft.com/) en selecteert u**Werkstromen** **instellingen** > .

![Standaardwerkstroom](images/default-workflow-listed.PNG)

Workflows kunnen volledig worden omschreven als JSON-tekenreeksen, waardoor ze programmatisch toegankelijk zijn. Als u de optie **Bewerken** voor uw werkstroom selecteert en vervolgens het **tabblad JSON** selecteert, ziet u een JSON-expressie als volgt:

```json
{
    "Type": "Logic",
    "If": {
        "ConnectorName": "moderator",
        "OutputName": "isAdult",
        "Operator": "eq",
        "Value": "true",
        "Type": "Condition"
        },
    "Then": {
    "Perform": [
    {
        "Name": "createreview",
        "CallbackEndpoint": null,
        "Tags": []
    }
    ],
    "Type": "Actions"
    }
}
```

Bekijk de [handleiding voor het hulpprogramma Controleren](./review-tool-user-guide/workflows.md) om aan de slag te gaan met het maken en gebruiken van werkstromen, of bekijk de [API-handleiding voor rust](./try-review-api-workflow.md) om te leren hoe u dit programmatisch doen.

## <a name="jobs"></a>Taken

Een moderatietaak dient als een soort wrapper voor de functionaliteit van contentmoderatie, workflows en reviews. De taak scant uw inhoud met behulp van de CONTENT Moderator image moderation API of text moderation API en controleert deze vervolgens aan de hand van de aangewezen werkstroom. Op basis van de werkstroomresultaten kan het al dan niet een beoordeling maken voor de inhoud in het [hulpprogramma Controleren](./review-tool-user-guide/human-in-the-loop.md). Hoewel zowel beoordelingen als workflows kunnen worden gemaakt en geconfigureerd met hun respectievelijke API's, u met de taak-API een gedetailleerd rapport van het hele proces verkrijgen (dat naar een opgegeven callback-eindpunt kan worden verzonden).

Bekijk de [API-handleiding voor rust](./try-review-api-job.md) om aan de slag te gaan met taken.

## <a name="next-steps"></a>Volgende stappen

* Test de [Job API-console](try-review-api-job.md)en gebruik de monsters van de REST API-code. Als u bekend bent met Visual Studio en C#, raadpleegt u ook de [Snelstart Van Jobs .NET](moderation-jobs-quickstart-dotnet.md). 
* Ga voor beoordelingen aan de slag met de [Review API-console](try-review-api-review.md)en gebruik de monsters van de REST API-code. Bekijk dan het gedeelte beoordelingen van de [.NET quickstart](dotnet-sdk-quickstart.md).
* Voor videorecensies gebruikt u de [quickstart videobeoordeling](video-reviews-quickstart-dotnet.md)en leert u hoe [u transcripties toevoegt aan de videobeoordeling](video-transcript-reviews-quickstart-dotnet.md).
