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
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: d60f1101fc858e9dcaa22162be97417544e13693
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81405049"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-curl"></a>Quick Start: een miniatuur genereren met behulp van de Computer Vision REST API en krul

In deze Quick Start genereert u een miniatuur van een installatie kopie met behulp van de Computer Vision REST API. U geeft de gewenste hoogte en breedte op. deze kunnen verschillen van het aspect rantsoen van de invoer afbeelding. Computer Vision maakt gebruik van slimme bijsnijding om het interesse gebied op intelligente wijze te identificeren en om de coördinaten rond die regio te genereren.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

- U moet [cURL](https://curl.haxx.se/windows) hebben.
- U moet beschikken over een abonnementssleutel voor Computer Vision. U kunt een gratis proef versie verkrijgen van [Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Of volg de instructies in [Create a cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) om u te abonneren op computer vision en uw sleutel op te halen.

## <a name="create-and-run-the-sample-command"></a>Een voorbeeldopdracht maken en uitvoeren

U kunt het voorbeeld maken en uitvoeren aan de hand van de volgende stappen:

1. Kopieer de volgende opdracht naar een teksteditor.
1. Breng waar nodig de volgende wijzigingen in de opdracht aan:
    1. Vervang de waarde van `<subscriptionKey>` door uw abonnementssleutel.
    1. Vervang de waarde van `<thumbnailFile>` door het pad en de naam van het bestand waarin u de miniatuur opslaat.
    1. Vervang het eerste deel van de aanvraag-URL`westcentralus`() door de tekst in uw eigen eind punt-URL.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Wijzig eventueel de afbeeldings-URL in de aanvraagtekst (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) in een URL van een andere afbeelding van waaruit u de miniatuur genereert.
1. Open een opdrachtpromptvenster.
1. Plak de opdracht van de teksteditor in het opdrachtpromptvenster en voer de opdracht uit.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.1/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>Het antwoord bekijken

Een geslaagd antwoord schrijft de miniatuurafbeelding naar het bestand dat is opgegeven in `<thumbnailFile>`. Als de aanvraag mislukt, bevat het antwoord een foutcode en een bericht om u te helpen bepalen wat er mis is gegaan. Als de aanvraag lijkt te slagen, maar de gemaakte miniatuur geen geldig afbeeldings bestand is, kan het zijn dat uw abonnements sleutel ongeldig is.

## <a name="next-steps"></a>Volgende stappen

Verken de Computer Vision-API voor het analyseren van een afbeelding, het detecteren van beroemdheden en bezienswaardigheden, het maken van een miniatuur en het extra heren van gedrukte en handgeschreven tekst. Als u snel wilt experimenteren met de Computer Vision-API, gebruikt u de [Open API-testconsole](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [De Computer Vision-API verkennen](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
