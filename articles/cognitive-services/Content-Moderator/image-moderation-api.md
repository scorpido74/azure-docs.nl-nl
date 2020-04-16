---
title: Image Moderation - Content Moderator
titleSuffix: Azure Cognitive Services
description: Gebruik de machine-ondersteunde beeldmoderatie en human-in-the-loop Review-tool van Content Moderator om afbeeldingen te modereren voor inhoud voor volwassenen en racy.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 36777208dc8ac179f1aaf345c374a33001e3f8bd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404267"
---
# <a name="learn-image-moderation-concepts"></a>Concepten voor beeldbeheer leren

Gebruik de machine-ondersteunde beeldmoderatie en [human-in-the-loop Review-tool van](Review-Tool-User-Guide/human-in-the-loop.md) Content Moderator om afbeeldingen te modereren voor inhoud voor volwassenen en racy. Scan afbeeldingen op tekstinhoud en extraheer die tekst en detecteer gezichten. U afbeeldingen koppelen aan aangepaste lijsten en verdere actie ondernemen.

## <a name="evaluating-for-adult-and-racy-content"></a>Evalueren voor inhoud voor volwassenen en racy

Met de bewerking **Evalueren** wordt een vertrouwensscore tussen 0 en 1 geretourneerd. Het retourneert ook booleaanse gegevens gelijk aan waar of onwaar. Deze waarden voorspellen of de afbeelding potentiële inhoud voor volwassenen of racy bevat. Wanneer u de API aanroept met uw afbeelding (bestand of URL), bevat het geretourneerde antwoord de volgende informatie:

    "ImageModeration": {
      .............
      "adultClassificationScore": 0.019196987152099609,
      "isImageAdultClassified": false,
      "racyClassificationScore": 0.032390203326940536,
      "isImageRacyClassified": false,
      ............
      ],

> [!NOTE]
> 
> - `isImageAdultClassified` geeft de mogelijke aanwezigheid weer van afbeeldingen die in bepaalde situaties als seksueel expliciet of erotisch kunnen worden beschouwd.
> - `isImageRacyClassified` geeft de mogelijke aanwezigheid weer van afbeeldingen die in bepaalde situaties als suggestief of voor volwassenen kunnen worden beschouwd.
> - De scores liggen tussen 0 en 1. Hoe hoger de score, hoe hoger het model voorspelt dat de categorie van toepassing kan zijn. Deze preview is gebaseerd op een statistisch model in plaats van handmatig gecodeerde resultaten. We raden u aan te testen met uw eigen inhoud om te bepalen hoe elke categorie aansluit bij uw vereisten.
> - De booleaanse waarden zijn waar of onwaar, afhankelijk van de interne scoredrempels. Klanten moeten beoordelen of ze deze waarde moeten gebruiken of aangepaste drempels moeten bepalen op basis van hun inhoudsbeleid.

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Tekst detecteren met ocr (Optical Character Recognition)

De operatie **Optical Character Recognition (OCR)** voorspelt de aanwezigheid van tekstinhoud in een afbeelding en extraheert deze onder andere voor tekstmatiging. U de taal opgeven. Als u geen taal opgeeft, wordt de detectie standaard weergegeven in het Engels.

Het antwoord bevat de volgende informatie:
- De oorspronkelijke tekst.
- De gedetecteerde tekstelementen met hun betrouwbaarheidsscores.

Voorbeelduittreksel:

    "TextDetection": {
      "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
      },
      .........
      "language": "eng",
      "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
      "candidates": []
    },


## <a name="detecting-faces"></a>Gezichten detecteren

Het detecteren van gezichten helpt bij het detecteren van persoonlijke gegevens, zoals gezichten in de afbeeldingen. U detecteert potentiële gezichten en het aantal potentiële gezichten in elke afbeelding.

Een antwoord bevat deze informatie:

- Gezichten tellen
- Lijst met gedetecteerde gezichten

Voorbeelduittreksel:


    "FaceDetection": {
       ......
      "result": true,
      "count": 2,
      "advancedInfo": [
      .....
      ],
      "faces": [
        {
          "bottom": 598,
          "left": 44,
          "right": 268,
          "top": 374
        },
        {
          "bottom": 620,
          "left": 308,
          "right": 532,
          "top": 396
        }
      ]
    }

## <a name="creating-and-managing-custom-lists"></a>Aangepaste lijsten maken en beheren

In veel online community's kunnen aanstootgevende items meerdere keren worden gedeeld in de volgende dagen, weken en maanden nadat gebruikers afbeeldingen of andere soorten inhoud hebben geüpload. De kosten van het herhaaldelijk scannen en filteren van dezelfde afbeelding of zelfs licht gewijzigde versies van de afbeelding op meerdere plaatsen kunnen duur en foutgevoelig zijn.

In plaats van dezelfde afbeelding meerdere keren te modereren, voegt u de aanstootgevende afbeeldingen toe aan uw aangepaste lijst met geblokkeerde inhoud. Op die manier vergelijkt uw contentmoderatiesysteem binnenkomende afbeeldingen met uw aangepaste lijsten en stopt u verdere verwerking.

> [!NOTE]
> Er is een maximumlimiet van **5 afbeeldingslijsten** waarbij elke lijst **niet meer dan 10.000 afbeeldingen mag bevatten**.
>

De Inhoudsmoderator biedt een complete [API voor het beheer](try-image-list-api.md) van afbeeldingen met bewerkingen voor het beheren van lijsten met aangepaste afbeeldingen. Begin met de [API-console voor afbeeldingslijsten](try-image-list-api.md) en gebruik de monsters van de REST API-code. Bekijk ook de [afbeeldingslijst .NET snelstart](image-lists-quickstart-dotnet.md) als u bekend bent met Visual Studio en C#.

## <a name="matching-against-your-custom-lists"></a>Overeenkomen met uw aangepaste lijsten

Met de bewerking Match u inkomende afbeeldingen op een vage manier afstemmen op een van uw aangepaste lijsten, die zijn gemaakt en beheerd met behulp van de bewerkingen Lijst.

Als er een overeenkomst wordt gevonden, retourneert de bewerking de id en de moderatietags van de overeenkomende afbeelding. Het antwoord bevat deze informatie:

- Wedstrijdscore (tussen 0 en 1)
- Gematchte afbeelding
- Afbeeldingstags (toegewezen tijdens eerdere moderatie)
- Afbeeldingslabels

Voorbeelduittreksel:

    {
    ..............,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169490,
            "Source": "169642",
            "Tags": [],
            "Label": "Sports"
        }
    ],
    ....
    }

## <a name="review-tool"></a>Beoordelingsprogramma

Voor meer genuanceerde gevallen gebruikt u de tool Content Moderator [Review](Review-Tool-User-Guide/human-in-the-loop.md) en de API om de moderatieresultaten en -inhoud in de beoordeling voor uw menselijke moderators weer te geven. Ze bekijken de door machines toegewezen tags en bevestigen hun definitieve beslissingen.

![Beoordeling van afbeelding voor menselijke beoordelaars](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>Volgende stappen

Test station de [Image Moderation API-console](try-image-api.md) en gebruik de REST API-code samples. Bekijk ook de sectie Beeldmatiging van de [.NET SDK snelstart](dotnet-sdk-quickstart.md) als u bekend bent met Visual Studio en C#.
