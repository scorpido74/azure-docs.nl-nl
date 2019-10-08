---
title: 'Quickstart: Tekstinhoud analyseren in C# - Content Moderator'
titleSuffix: Azure Cognitive Services
description: Tekst inhoud voor verschillende aanstootgevende materialen analyseren met behulp van de Content Moderator SDK voor .NET
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: sajagtap
ms.openlocfilehash: 69c190b9ce9e5a99d39a46c147f7159ace47e4f7
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001438"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-c"></a>Quickstart: Tekstinhoud op ongewenst materiaal analyseren in C#

In dit artikel vindt u informatie en codevoorbeelden om aan de slag te gaan met de [Content Moderator SDK voor .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/). U leert hoe u op termen gebaseerde filtering en classificatie van tekst inhoud kunt uitvoeren met het oog op het beheer van potentieel aanstootgevend materiaal.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 

## <a name="prerequisites"></a>Vereisten
- Een abonnementssleutel voor Content Moderator. Volg de instructies in [Een Cognitive Services-account maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) om u te abonneren op Content Moderator en uw sleutel op te halen.
- Een versie van [Visual Studio 2015 of 2017](https://www.visualstudio.com/downloads/)

> [!NOTE]
> In deze handleiding wordt een gratis Content Moderator-abonnement gebruikt. Raadpleeg de pagina [Prijzen en beperkingen](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/) voor informatie over wat bij elke abonnementslaag wordt geleverd.

## <a name="create-the-visual-studio-project"></a>Het Visual Studio-project maken

1. Maak in Visual Studio een nieuw **Console-app (.NET Framework)** -project en noem het **TextModeration**. 
1. Als uw oplossing nog meer projecten bevat, selecteert u deze als het enkele opstartproject.
1. Haal het vereiste NuGet-pakket op. Klik met de rechter muisknop op uw project in Solution Explorer en selecteer **NuGet-pakketten beheren**. Zoek en installeer vervolgens het **`Microsoft.Azure.CognitiveServices.ContentModerator`-pakket**. U kunt ook de volgende opdracht uitvoeren vanuit de map Solution:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator
```

## <a name="add-text-moderation-code"></a>Teksttoezichtcode toevoegen

Vervolgens kopieert en plakt u de code uit deze handleiding in uw project om een eenvoudig scenario voor inhoudstoezicht te implementeren.

### <a name="include-namespaces"></a>Naamruimten opnemen

Voeg aan het begin van het bestand *Program.cs* de volgende `using`-instructies toe.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=1-8)]

### <a name="create-the-content-moderator-client"></a>De Content Moderator-client maken

Voeg de volgende code toe aan het bestand *Program.cs* om een Content Moderator-clientprovider voor uw abonnement te maken. Voeg de code toe naast de klasse **Program**, in dezelfde naamruimte. U moet de velden **AzureRegion** en **CMSubscriptionKey** bijwerken met de waarden van uw regio-id en de abonnementssleutel.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=54-77)]

### <a name="set-up-input-and-output-targets"></a>Invoer- en uitvoerdoelen instellen

Voeg de volgende statische velden toe aan de klasse **Program** in _Program.cs_. Deze velden geven de bestanden voor invoer tekst inhoud en JSON-inhoud voor uitvoer op.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=15-19)]

U moet het invoer bestand *TextFile. txt* maken en het bijbehorende pad bijwerken (de paden zijn relatief ten opzichte van de uitvoerings Directory). Open _TextFile.txt_ en voeg de tekst toe waarop u toezicht wilt houden. In deze snelstart wordt de volgende voorbeeldtekst gebruikt:

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 
0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="load-the-input-text"></a>De invoertekst laden

Voeg de volgende code aan de methode **Hoofd**. De **ScreenText**-methode is de essentiële bewerking. De parameters ervan bepalen welke bewerkingen voor inhoudtoezicht worden uitgevoerd. In dit voorbeeld wordt de methode geconfigureerd om het volgende te doen:
- Mogelijk grof taalgebruik in de tekst te detecteren.
- De tekst te normaliseren en typfouten automatisch te corrigeren.
- Persoons gegevens, zoals telefoon nummers van het Amerikaanse en UK, en e-mail adressen van de Verenigde Staten, detecteren.
- Machine-learningmodellen gebruiken om de tekst onder te verdelen in drie categorieën.

Als u meer wilt weten over wat deze bewerkingen doen, volgt u de koppeling in de sectie [Volgende stappen](#next-steps).

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=23-48)]

## <a name="run-the-program"></a>Het programma uitvoeren

Het programma schrijft JSON-tekenreeksgegevens naar het bestand _TextModerationOutput.txt_. De voorbeeldtekst die wordt gebruikt in deze snelstart geeft de volgende uitvoer:

```json
Autocorrect typos, check for matching terms, PII, and classify.
{
"OriginalText": "\"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).\"",
"NormalizedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
"AutoCorrectedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
"Misrepresentation": null,
    
"Classification": {
        "Category1": {
        "Score": 1.5113095059859916E-06
        },
        "Category2": {
        "Score": 0.12747249007225037
        },
        "Category3": {
        "Score": 0.98799997568130493
        },
        "ReviewRecommended": true
  },
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "PII": {
        "Email": [
            {
            "Detected": "abcdef@abcd.com",
            "SubType": "Regular",
            "Text": "abcdef@abcd.com",
            "Index": 33
            }
            ],
        "IPA": [
            {
            "SubType": "IPV4",
            "Text": "255.255.255.255",
            "Index": 73
            }
            ],
        "Phone": [
            {
            "CountryCode": "US",
            "Text": "6657789887",
            "Index": 57
            },
            {
            "CountryCode": "US",
            "Text": "870 608 4000",
            "Index": 211
            },
            {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 207
            },
            {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 227
            },
            {
        "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 244
            }
            ],
         "Address": [{
             "Text": "1 Microsoft Way, Redmond, WA 98052",
             "Index": 89
                }]
    },
  "Language": "eng",
  "Terms": [
    {
        "Index": 22,
        "OriginalIndex": 22,
        "ListId": 0,
        "Term": "crap"
    }
  ],
  "TrackingId": "9392c53c-d11a-441d-a874-eb2b93d978d3"
}
```

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een eenvoudige .NET- toepassing ontwikkeld die de Content Moderator-service gebruikt om relevante informatie over een bepaald tekstvoorbeeld te retourneren. Nu leert u meer over wat de verschillende vlaggen en classificaties betekenen, zodat u kunt beslissen welke gegevens u nodig hebt en hoe uw app ermee om moet gaan.

> [!div class="nextstepaction"]
> [Teksttoezichthandleiding](text-moderation-api.md)
