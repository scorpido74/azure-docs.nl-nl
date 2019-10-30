---
title: Recensies, werk stromen en taken concepten-Content Moderator
titleSuffix: Azure Cognitive Services
description: Meer informatie over beoordelingen, werk stromen en taken
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 917636c8ae339e06321c169979611f74276ca523
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044006"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Beoordelingen, werk stromen en taken voor content toezicht

Content Moderator combineert de door de machine ondersteunde toezicht met de mogelijkheden van Human-in-the-loop om een optimaal toezicht proces te maken voor praktijk scenario's. Dit wordt gedaan via het [beoordelings programma](https://contentmoderator.cognitive.microsoft.com)op basis van de Cloud. In deze hand leiding vindt u informatie over de belangrijkste concepten van het hulp programma voor beoordeling: Recensies, werk stromen en taken.

## <a name="reviews"></a>Beoordelingen

Bij een beoordeling wordt inhoud geüpload naar het hulp programma beoordeling en wordt deze weer gegeven onder het tabblad **controleren** . Hier kunnen gebruikers de toegepaste labels wijzigen en hun eigen aangepaste labels Toep assen. Wanneer een gebruiker een beoordeling verzendt, worden de resultaten verzonden naar een opgegeven eind punt van de retour aanroep en wordt de inhoud verwijderd van de site.

![De website voor het controleren van het hulp programma open in een browser op het tabblad controleren](./Review-Tool-user-Guide/images/image-workflow-review.png)

Raadpleeg de [hand leiding voor het beoordelings programma](./review-tool-user-guide/review-moderated-images.md) om te beginnen met het maken van recensies of raadpleeg de [rest API gids](./try-review-api-review.md) voor informatie over hoe u dit kunt doen.

## <a name="workflows"></a>Werkstromen

Een werk stroom is een op de cloud gebaseerd gepersonaliseerd filter voor inhoud. Werk stromen kunnen verbinding maken met verschillende services om inhoud op verschillende manieren te filteren en vervolgens de juiste actie ondernemen. Met de Content Moderator-connector kunt u met een werk stroom automatisch toezicht Tags Toep assen en beoordelingen maken met verzonden inhoud.

### <a name="view-workflows"></a>Werk stromen weer geven

Als u uw bestaande werk stromen wilt weer geven, gaat u naar het [hulp programma controleren](https://contentmoderator.cognitive.microsoft.com/) en selecteert u **instellingen**  > **werk stromen**.

![Standaardwerk stroom](images/default-workflow-listed.PNG)

Werk stromen kunnen volledig worden beschreven als JSON-teken reeksen, waardoor ze via een programma toegankelijk zijn. Als u de optie **bewerken** selecteert voor uw werk stroom en vervolgens het tabblad **JSON** selecteert, ziet u een JSON-expressie zoals het volgende:

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

Raadpleeg de [hand leiding voor het hulp programma](./review-tool-user-guide/workflows.md) voor het maken en gebruiken van werk stromen of raadpleeg de [rest API gids](./try-review-api-workflow.md) voor informatie over hoe u dit kunt doen met programma code.

## <a name="jobs"></a>Taken

Een toezicht taak fungeert als een soort wrapper voor de functionaliteit van toezicht op inhoud, werk stromen en Beoordelingen. De taak scant uw inhoud met behulp van de Content Moderator afbeeldings toezicht-API of tekst toezicht-API en controleert deze vervolgens op de aangewezen werk stroom. Op basis van de werk stroom resultaten kan de inhoud niet worden gecontroleerd in het [beoordelings programma](./review-tool-user-guide/human-in-the-loop.md). Hoewel zowel beoordelingen als werk stromen kunnen worden gemaakt en geconfigureerd met hun respectieve Api's, kunt u met de taak-API een gedetailleerd rapport verkrijgen van het hele proces (dat kan worden verzonden naar een opgegeven call back-eind punt).

Raadpleeg de [rest API-hand leiding](./try-review-api-job.md) om aan de slag te gaan met taken.

## <a name="next-steps"></a>Volgende stappen

* Test de [taak-API-console](try-review-api-job.md)en gebruik de rest API code voorbeelden. Als u bekend bent met Visual Studio en C#raadpleegt u ook de [taken .net Quick](moderation-jobs-quickstart-dotnet.md)start. 
* Ga voor recensies naar aan de slag met de [API-console voor controle](try-review-api-review.md)en gebruik de rest API code voorbeelden. Zie de sectie Recensies van [.net Quick](dotnet-sdk-quickstart.md)start.
* Gebruik voor video revisies de [Snelstartgids voor video revisie](video-reviews-quickstart-dotnet.md)en leer hoe u [Transcripten kunt toevoegen aan de video beoordeling](video-transcript-reviews-quickstart-dotnet.md).
