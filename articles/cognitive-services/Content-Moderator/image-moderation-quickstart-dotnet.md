---
title: 'Quick Start: afbeeldingen analyseren voor ongewenste inhoud C# in-content moderator'
titleSuffix: Azure Cognitive Services
description: Afbeeldings inhoud voor verschillende aanstootgevende materialen analyseren met behulp van de Content Moderator SDK voor .NET
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 13b0952f38fb0c8c922be415f782b3a0a0861729
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931739"
---
# <a name="quickstart-analyze-images-for-objectionable-content-in-c"></a>Quick Start: afbeeldingen analyseren voor aanstootgevende inhoud inC#

In dit artikel vindt u informatie en codevoorbeelden om aan de slag te gaan met de [Content Moderator SDK voor .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/). U leert hoe u kunt scannen op inhoud voor volwassenen of ongepaste, Extraheer bare tekst en menselijke gezichten met het oog op het beheer van potentieel aanstootgevend materiaal.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 

## <a name="prerequisites"></a>Vereisten

- Een abonnementssleutel voor Content Moderator. Volg de instructies in [Create a cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) om u te abonneren op content moderator. Vervolgens kunt u [omgevings variabelen maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel en het eind punt-URL, respectievelijk met de naam `CONTENT_MODERATOR_SUBSCRIPTION_KEY` en `CONTENT_MODERATOR_ENDPOINT`.
- Een versie van [Visual Studio 2015 of 2017](https://www.visualstudio.com/downloads/)


> [!NOTE]
> In deze handleiding wordt een gratis Content Moderator-abonnement gebruikt. Raadpleeg de pagina [Prijzen en beperkingen](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/) voor informatie over wat bij elke abonnementslaag wordt geleverd.

## <a name="create-the-visual-studio-project"></a>Het Visual Studio-project maken

1. Maak in Visual Studio een nieuw **Console-app (.NET Framework)** -project en noem het **ImageModeration**. 
1. Als uw oplossing nog meer projecten bevat, selecteert u deze als het enkele opstartproject.
1. Download de vereiste NuGet-pakketten. Klik met de rechtermuisknop op uw project in Solution Explorer en selecteer **NuGet-pakketten beheren**. Zoek en installeer vervolgens de volgende pakketten:
    - Microsoft.Azure.CognitiveServices.ContentModerator
    - Microsoft.Rest.ClientRuntime
    - Newtonsoft.Json

## <a name="add-image-moderation-code"></a>Code voor afbeeldingstoezicht toevoegen

Vervolgens kopieert en plakt u de code uit deze handleiding in uw project om een eenvoudig scenario voor inhoudstoezicht te implementeren.

### <a name="include-namespaces"></a>Naamruimten opnemen

Voeg aan het begin van het bestand *Program.cs* de volgende `using`-instructies toe.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?name=snippet_using)]

### <a name="create-the-content-moderator-client"></a>De Content Moderator-client maken

Voeg de volgende code toe aan het bestand *Program.cs* om een Content Moderator-clientprovider voor uw abonnement te maken. Voeg de klasse toe naast de klasse **Program** , in dezelfde naam ruimte. U moet de velden **AzureBaseURL** en **CMSubscriptionKey** bijwerken met de waarden van uw eind punt-URL en abonnements sleutel. U vindt deze in het Azure Portal op het tabblad **Quick Start** van uw resource.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?name=snippet_client)]


### <a name="set-up-input-and-output-targets"></a>Invoer- en uitvoerdoelen instellen

Voeg de volgende statische velden toe aan de klasse **Program** in _Program.cs_. Deze velden geven de bestanden voor de inhoud van de invoer installatie kopie en de uitvoer van JSON-inhoud.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?name=snippet_fields)]

U moet het invoer bestand *ImageFiles. txt* maken en het bijbehorende pad dienovereenkomstig bijwerken (relatieve paden zijn relatief ten opzichte van de uitvoerings Directory). Open _ImageFiles.txt_ en voeg de URL's van de te beheren afbeeldingen toe. In deze snelstart worden de volgende URL's als voorbeeldinvoer gebruikt.

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="create-a-class-to-handle-results"></a>Een klasse maken voor het afhandelen van resultaten

Voeg de volgende code toe aan *Program.cs*, naast de klasse **Program** in dezelfde naamruimte. U gebruikt een exemplaar van deze klasse om de toezichtresultaten voor alle geëvalueerde afbeeldingen vast te leggen.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?name=snippet_dataclass)]


### <a name="define-the-image-evaluation-method"></a>De methode voor de evaluatie van de afbeelding definiëren

Voeg de volgende methode toe aan de klasse **Program**. Met deze methode wordt één afbeelding op drie verschillende manieren geëvalueerd en worden de resultaten van de evaluatie geretourneerd. Als u meer wilt weten over wat deze afzonderlijke bewerkingen doen, volgt u de koppeling in de sectie [Volgende stappen](#next-steps).

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?name=snippet_evaluate)]

### <a name="load-the-input-images"></a>De invoerafbeeldingen laden

Voeg de volgende code toe aan de methode **Main** in de klasse **App**. Met deze code wordt het programma zo ingesteld dat evaluatie gegevens worden opgehaald voor elke afbeeldings-URL in het invoer bestand. Daarna worden deze gegevens naar één uitvoerbestand geschreven.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?name=snippet_main)]

## <a name="run-the-program"></a>Het programma uitvoeren

Het programma schrijft JSON-tekenreeksgegevens naar het bestand _TextModerationOutput.json_. De voorbeeldafbeeldingen die in deze snelstart worden gebruikt, geven de volgende uitvoer. Elke afbeelding heeft verschillende secties voor `ImageModeration`, `FaceDetection` en `TextDetection`, die overeenkomen met de drie API-aanroepen in uw **EvaluateImage** -methode.

```json
[{
  "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg",
  "ImageModeration": {
    "cacheID": "7733c303-3b95-4710-a41e-7a322ae81a15_636488005858745661",
    "result": false,
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_687c356d-0f00-4aeb-ae5f-c7555af80247",
    "adultClassificationScore": 0.019196987152099609,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.032390203326940536,
    "isImageRacyClassified": false,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "116"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    }
  },
  "TextDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "metadata": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "12"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_814fa162-c5d6-4ca6-997b-30ed0686ca83",
    "cacheId": "3fb69496-c64b-4de9-affd-6dd6d23f3e78_636488005876558920",
    "language": "eng",
    "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
    "candidates": []
  },
  "FaceDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_a2c40dbe-609d-4eb8-b01c-9988388804ea",
    "cacheId": "e4c0b500-ea8e-4a31-8fb3-35f98c4fbd65_636488005889528303",
    "result": false,
    "count": 0,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "11"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "faces": []
  }
},
{
  "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png",
  "ImageModeration": {
    "cacheID": "b4866aa2-5e69-44ed-806a-f9a5d618c8ae_636488005930693926",
    "result": false,
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_fdce5510-f689-4791-b081-c2ad54dcfe78",
    "adultClassificationScore": 0.0035635426174849272,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.021369094029068947,
    "isImageRacyClassified": false,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "109"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    }
  },
  "TextDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "metadata": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "46"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_08a4bc19-6010-41bb-a440-a77278e167d8",
    "cacheId": "28b37471-41b3-4f79-bd23-965498bcff51_636488005950851288",
    "language": "eng",
    "text": "",
    "candidates": []
  },
  "FaceDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_40f2ce07-14ba-47cd-ba09-58b557a89854",
    "cacheId": "ec9c1be3-99b7-4bd9-8bc4-dc958c74459f_636488005964914299",
    "result": true,
    "count": 6,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "60"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
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
      },
      {
        "bottom": 575,
        "left": 594,
        "right": 773,
        "top": 396
      },
      {
        "bottom": 563,
        "left": 812,
        "right": 955,
        "top": 420
      },
      {
        "bottom": 611,
        "left": 972,
        "right": 1151,
        "top": 432
      },
      {
        "bottom": 510,
        "left": 1232,
        "right": 1456,
        "top": 286
      }
    ]
  }
}]
```

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een eenvoudige .NET- toepassing ontwikkeld die de Content Moderator-service gebruikt om relevante informatie over een bepaald afbeeldingsvoorbeeld te retourneren. Nu leert u meer over wat de verschillende vlaggen en classificaties betekenen, zodat u kunt beslissen welke gegevens u nodig hebt en hoe uw app ermee om moet gaan.

> [!div class="nextstepaction"]
> [Afbeeldingstoezichthandleiding](image-moderation-api.md)
