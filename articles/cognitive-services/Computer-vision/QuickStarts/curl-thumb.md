---
title: 'Snelstart: Een miniatuur genereren - REST, cURL'
titleSuffix: Azure Cognitive Services
description: In deze snelstart maakt u een miniatuur van een afbeelding met behulp van de Computer Vision-API en cURL.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 05/20/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 77850de4f2582c86281c58c32e1689fd8741126e
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/18/2020
ms.locfileid: "84987406"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-curl"></a>Quick Start: een miniatuur genereren met behulp van de Computer Vision REST API en krul

In deze Quick Start genereert u een miniatuur van een installatie kopie met behulp van de Computer Vision REST API. U geeft de gewenste hoogte en breedte op. deze kunnen verschillen van het aspect rantsoen van de invoer afbeelding. Computer Vision maakt gebruik van slimme bijsnijding om het interesse gebied op intelligente wijze te identificeren en om de coördinaten rond die regio te genereren.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/cognitive-services/) 
* [Ezelsoor](https://curl.haxx.se/)
* Wanneer u uw Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" maakt u een computer vision resource Maak "  target="_blank"> een computer vision resource <span class="docon docon-navigate-external x-hidden-focus"></span> </a> in de Azure Portal om uw sleutel en eind punt op te halen. Nadat de app is geïmplementeerd, klikt **u op Ga naar resource**.
  * U hebt de sleutel en het eind punt nodig van de resource die u maakt om de toepassing te verbinden met de Computer Vision-service. U plakt uw sleutel en het eind punt in de onderstaande code verderop in de Quick Start.
  * U kunt de gratis prijs categorie ( `F0` ) gebruiken om de service te proberen en later te upgraden naar een betaalde laag voor productie.

## <a name="create-and-run-the-sample-command"></a>Een voorbeeldopdracht maken en uitvoeren

U kunt het voorbeeld maken en uitvoeren aan de hand van de volgende stappen:

1. Kopieer de volgende opdracht naar een teksteditor.
1. Breng waar nodig de volgende wijzigingen in de opdracht aan:
    1. Vervang de waarde van `<subscriptionKey>` door uw abonnementssleutel.
    1. Vervang de waarde van `<thumbnailFile>` door het pad en de naam van het bestand waarin u de miniatuur opslaat.
    1. Vervang het eerste deel van de aanvraag-URL ( `westcentralus` ) door de tekst in uw eigen eind punt-URL.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Wijzig eventueel de afbeeldings-URL in de aanvraagtekst (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) in een URL van een andere afbeelding van waaruit u de miniatuur genereert.
1. Open een opdrachtpromptvenster.
1. Plak de opdracht van de tekst editor in het opdracht prompt venster.
1. Druk op ENTER om het programma uit te voeren.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westus.api.cognitive.microsoft.com/vision/v3.0/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>Het antwoord bekijken

Een geslaagd antwoord schrijft de miniatuurafbeelding naar het bestand dat is opgegeven in `<thumbnailFile>`. Als de aanvraag mislukt, bevat het antwoord een foutcode en een bericht om u te helpen bepalen wat er mis is gegaan. Als de aanvraag lijkt te slagen, maar de gemaakte miniatuur geen geldig afbeeldings bestand is, kan het zijn dat uw abonnements sleutel ongeldig is.

## <a name="next-steps"></a>Volgende stappen

Verken de Computer Vision-API voor het analyseren van een afbeelding, het detecteren van beroemdheden en bezienswaardigheden, het maken van een miniatuur en het extra heren van gedrukte en handgeschreven tekst. Als u snel wilt experimenteren met de Computer Vision-API, gebruikt u de [Open API-testconsole](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20c).

> [!div class="nextstepaction"]
> [De Computer Vision-API verkennen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f21b)
