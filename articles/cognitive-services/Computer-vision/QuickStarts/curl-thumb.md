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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405049"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-curl"></a>Snelstart: een miniatuur genereren met de Computer Vision REST API en cURL

In deze quickstart genereert u een miniatuur uit een afbeelding met behulp van de Computer Vision REST API. U geeft de gewenste hoogte en breedte op, die kunnen verschillen in aspectrantsoen van de invoerafbeelding. Computer Vision maakt gebruik van slimme bijsnijden om intelligent te identificeren het gebied van belang en het genereren van bijsnijden coördinaten rond die regio.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) voordat u begint.

## <a name="prerequisites"></a>Vereisten

- U moet [cURL](https://curl.haxx.se/windows) hebben.
- U moet beschikken over een abonnementssleutel voor Computer Vision. U een gratis testsleutel krijgen van [Try Cognitive Services.](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) Of volg de instructies in [Een Cognitive Services-account maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) om u te abonneren op Computer Vision en uw sleutel te krijgen.

## <a name="create-and-run-the-sample-command"></a>Een voorbeeldopdracht maken en uitvoeren

U kunt het voorbeeld maken en uitvoeren aan de hand van de volgende stappen:

1. Kopieer de volgende opdracht naar een teksteditor.
1. Breng waar nodig de volgende wijzigingen in de opdracht aan:
    1. Vervang de waarde van `<subscriptionKey>` door uw abonnementssleutel.
    1. Vervang de waarde van `<thumbnailFile>` door het pad en de naam van het bestand waarin u de miniatuur opslaat.
    1. Vervang het eerste deel van`westcentralus`de aanvraag-URL ( ) door de tekst in uw eigen eindpunt-URL.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Wijzig eventueel de afbeeldings-URL in de aanvraagtekst (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) in een URL van een andere afbeelding van waaruit u de miniatuur genereert.
1. Open een opdrachtpromptvenster.
1. Plak de opdracht van de teksteditor in het opdrachtpromptvenster en voer de opdracht uit.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.1/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>Het antwoord bekijken

Een geslaagd antwoord schrijft de miniatuurafbeelding naar het bestand dat is opgegeven in `<thumbnailFile>`. Als de aanvraag mislukt, bevat het antwoord een foutcode en een bericht om u te helpen bepalen wat er mis is gegaan. Als de aanvraag lijkt te slagen, maar de gemaakte miniatuur geen geldig afbeeldingsbestand is, kan het zijn dat uw abonnementssleutel niet geldig is.

## <a name="next-steps"></a>Volgende stappen

Verken de Computer Vision API om een afbeelding te analyseren, beroemdheden en oriëntatiepunten te detecteren, een miniatuur te maken en gedrukte en handgeschreven tekst te extraheren. Als u snel wilt experimenteren met de Computer Vision-API, gebruikt u de [Open API-testconsole](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [De Computer Vision-API verkennen](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
