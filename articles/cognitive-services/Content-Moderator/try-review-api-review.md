---
title: Moderatiebeoordelingen maken met REST API-console - ContentModerator
titleSuffix: Azure Cognitive Services
description: Gebruik de API's voor Azure-inhoudsmoderatoren om afbeeldings- of tekstbeoordelingen te maken voor menselijke moderatie.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: pafarley
ms.openlocfilehash: a9726e41a84926d00d48b51e31f534a3d8c2fe0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72757144"
---
# <a name="create-human-reviews-rest"></a>Menselijke beoordelingen maken (REST)

[Beoordelingen](./review-api.md#reviews) slaan inhoud op en tonen inhoud die menselijke moderators kunnen beoordelen. Wanneer een gebruiker een beoordeling voltooit, worden de resultaten naar een opgegeven callback-eindpunt verzonden. In deze handleiding leert u hoe u beoordelingen instellen met behulp van de REST-API's van de beoordeling via de API-console. Zodra u de structuur van de API's begrijpt, u deze oproepen eenvoudig naar elk REST-compatibel platform porten.

## <a name="prerequisites"></a>Vereisten

- Meld u aan of maak een account aan op de site van het [hulpprogramma Inhoudsmoderator.](https://contentmoderator.cognitive.microsoft.com/)

## <a name="create-a-review"></a>Een beoordeling maken

Als u een beoordeling wilt maken, gaat u naar de referentiepagina **[Controleren - API maken](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** en selecteert u de knop voor uw sleutelgebied (u dit vinden in de URL van eindpunt op de pagina **Referenties** van het [hulpprogramma Controleren).](https://contentmoderator.cognitive.microsoft.com/) Hiermee wordt de API-console gestart, waar u eenvoudig REST API-aanroepen samenstellen en uitvoeren.

![Beoordeling - Regioselectie krijgen](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Rest-oproepparameters invoeren

Voer waarden in voor **teamName**en **Ocp-Apim-Subscription-Key:**

- **teamName:** de team-id die u hebt gemaakt bij het instellen van uw [account met het beoordelingsprogramma](https://contentmoderator.cognitive.microsoft.com/) (gevonden in het veld **Id** op het scherm Referenties van uw gereedschap Controleren).
- **Ocp-Apim-Subscription-Key:** Uw contentmoderatorsleutel. U vindt dit op het tabblad **Instellingen** van het [gereedschap Controleren](https://contentmoderator.cognitive.microsoft.com).

### <a name="enter-a-review-definition"></a>Een beoordelingsdefinitie invoeren

Bewerk het vak **Hoofdtekst aanvragen** om de JSON-aanvraag in te voeren met de volgende velden:

- **Metagegevens:** aangepaste sleutelwaardeparen die moeten worden geretourneerd naar uw callback-eindpunt. Als de sleutel een korte code is die is gedefinieerd in het [gereedschap Controleren,](https://contentmoderator.cognitive.microsoft.com)wordt deze weergegeven als een tag.
- **Inhoud:** In het geval van inhoud voor afbeeldingen en video's is dit een URL-tekenreeks die naar de inhoud wijst. Voor tekstinhoud is dit de werkelijke tekenreeks.
- **ContentId:** een aangepaste id-tekenreeks. Deze tekenreeks wordt doorgegeven aan de API en wordt teruggestuurd via de callback. Het is handig voor het koppelen van interne id's of metagegevens aan de resultaten van een moderatietaak.
- **CallbackEndpoint**: (Optioneel) De URL om terugbelgegevens te ontvangen wanneer de beoordeling is voltooid.

De standaardaanvraagbody toont voorbeelden van de verschillende typen beoordelingen die u maken:

```json
[Image]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Image",
    "Content": "<Content Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Text]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Text",
    "Content": "<Your Text Content>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Video]
[
  {
    "VideoFrames":[
      {
          "Id": "<Frame Id>",
          "Timestamp": "<Frame Timestamp",
          "FrameImage":"<Frame Image URL",
          "Metadata": [
            {
              "Key": "<Key>",
              "Value": "<Value"
            }
          ],
          "ReviewerResultTags": [
          ]
    ], 
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      },
      //For encrypted Videos
        {
          "Key": "protectedType",
          "Value": "AES or FairPlay or Widevine or Playready"
        },
        {
          "Key": "authenticationToken",
          "Value": "your viewtoken(In case of Video Indexer AES encryption type, this value is viewtoken from breakdown json)"
        },
      //For FairPlay encrypted type video include certificateUrl as well
        {
          "Key": "certificateUrl",
          "Value": "your certificate url"
        }
    ],
    "Type": "Video",
    "Content": "<Stream Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>",
    [Optional]
    "Timescale": "<Timescale of the video>
  }
]
```

### <a name="submit-your-request"></a>Uw aanvraag indienen
  
Selecteer **Verzenden**. Als de bewerking **Response status** slaagt, `200 OK`is de status Antwoord en wordt in het vak **Antwoord-inhoud** een id weergegeven voor de controle. Kopieer deze id naar gebruik in de volgende stappen.

![Controleren - Het inhoudsvak consolerespons maken geeft de controle-id weer](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Bekijk de nieuwe review

Selecteer in het [gereedschap Controleren](https://contentmoderator.cognitive.microsoft.com)de optie**Afbeeldingstekstvideo**/**Video** **Image**/ **controleren** > (afhankelijk van welke inhoud u hebt gebruikt). De inhoud die je hebt geüpload, moet worden weergegeven, klaar voor menselijke beoordeling.

![Het hulpmiddelbeeld van het overzicht van een voetbalbal](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Lees meer details van de beoordeling

Als u details wilt ophalen over een bestaande beoordeling, gaat u naar de referentiepagina [Controleren - API ophalen](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) en selecteert u de knop voor uw regio (het gebied waarin uw sleutel wordt beheerd).

![Werkstroom - Regioselectie opdoen](images/test-drive-region.png)

Voer de parameters voor de REST-oproep in zoals in de bovenstaande sectie. Voor deze stap is **reviewId** de unieke ID-tekenreeks die u hebt ontvangen toen u de beoordeling maakte.

![Review - Console Resultaten maken](images/test-drive-review-3.PNG)
  
Selecteer **Verzenden**. Als de bewerking slaagt, `200 OK`is de **antwoordstatus** en wordt in het vak **Antwoord-inhoud** de controledetails in JSON-indeling weergegeven, zoals:

```json
{  
  "reviewId":"201712i46950138c61a4740b118a43cac33f434",
  "subTeam":"public",
  "status":"Complete",
  "reviewerResultTags":[  
    {  
      "key":"a",
      "value":"False"
    },
    {  
      "key":"r",
      "value":"True"
    },
    {  
      "key":"sc",
      "value":"True"
    }
  ],
  "createdBy":"<teamname>",
  "metadata":[  
    {  
      "key":"sc",
      "value":"true"
    }
  ],
  "type":"Image",
  "content":"https://reviewcontentprod.blob.core.windows.net/<teamname>/IMG_201712i46950138c61a4740b118a43cac33f434",
  "contentId":"0",
  "callbackEndpoint":"<callbackUrl>"
}
```

Let op de volgende velden in het antwoord:

- **status**
- **reviewerResultTags**: Dit wordt weergegeven als tags handmatig zijn toegevoegd door het menselijke beoordelingsteam (het **veld gemaaktOp** basis van).
- **metagegevens:** Dit toont de tags die in eerste instantie in de beoordeling zijn toegevoegd, voordat het menselijke beoordelingsteam wijzigingen heeft aangebracht.

## <a name="next-steps"></a>Volgende stappen

In deze handleiding heb je geleerd hoe je contentmoderatiebeoordelingen maken met behulp van de REST API. Integreer vervolgens beoordelingen in een end-to-end moderatiescenario, zoals de [zelfstudie voor e-commercemoderatie.](./ecommerce-retail-catalog-moderation.md)