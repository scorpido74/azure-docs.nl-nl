---
title: 'Quickstart: Een afbeelding analyseren - SDK, C#'
titleSuffix: Azure Cognitive Services
description: In deze snelstart analyseert u een afbeelding met behulp van de Computer Vision Windows C#-clientbibliotheek.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 59641973a953d1db501f7b660b04df900bc55cb0
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141020"
---
# <a name="quickstart-analyze-an-image-using-the-computer-vision-sdk-and-c"></a>Snelstartgids: Een afbeelding analyseren met de Computer Vision-SDK en C#

In deze quickstart analyseert u zowel een lokale als een externe afbeelding om visuele kenmerken te extraheren met behulp van de Computer Vision-clientbibliotheek voor C#. U kunt eventueel de code in deze handleiding downloaden als een volledige voorbeeld-app uit de [Cognitive Services Csharp Vision](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision)-opslagplaats op GitHub.

## <a name="prerequisites"></a>Vereisten

* Een Computer Vision-abonnements sleutel. U kunt een abonnementssleutel voor een gratis proefversie downloaden van [Cognitive Services proberen](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Of volg de instructies in [Create a cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) om u te abonneren op computer vision en uw sleutel op te halen. Vervolgens kunt u [omgevings variabelen maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel-en service- `COMPUTER_VISION_SUBSCRIPTION_KEY` eindpunt teken reeks, respectievelijk met de naam en. `COMPUTER_VISION_ENDPOINT`
* Een versie van [Visual Studio 2015 of 2017](https://www.visualstudio.com/downloads/).
* Het NuGet-pakket van de [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision)-clientbibliotheek. U hoeft het pakket niet te downloaden. Hieronder vindt u de installatie-instructies.

## <a name="create-and-run-the-sample-application"></a>De voorbeeldtoepassing maken en uitvoeren

U kunt het voorbeeld uitvoeren aan de hand van de volgende stappen:

1. Maak in Visual Studio een nieuwe Visual C#-console-app.
1. Installeer het NuGet-pakket van de Computer Vision-client-bibliotheek.
    1. Klik in het menu op **Extra**, selecteer **NuGet Package Manager** en vervolgens **NuGet-pakketten voor oplossing beheren**.
    1. Klik op het tabblad **Bladeren** en typ 'Microsoft.Azure.CognitiveServices.Vision.ComputerVision' in het vak **Zoeken**.
    1. Selecteer **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** wanneer dit wordt weergegeven, klik op het selectievakje naast uw projectnaam en klik op **Installeren**.
1. Vervang de inhoud van *Program.cs* door de volgende code. De methoden `AnalyzeImageAsync` en `AnalyzeImageInStreamAsync` gebruiken de [Analyse Image REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) voor respectievelijk externe en lokale afbeeldingen.

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading.Tasks;

    namespace ImageAnalyze
    {
        class Program
        {
            // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
            private const string subscriptionKey = "<SubscriptionKey>";

            // localImagePath = @"C:\Documents\LocalImage.jpg"
            private const string localImagePath = @"<LocalImage>";

            private const string remoteImageUrl =
                "https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg";

            // Specify the features to return
            private static readonly List<VisualFeatureTypes> features =
                new List<VisualFeatureTypes>()
            {
                VisualFeatureTypes.Categories, VisualFeatureTypes.Description,
                VisualFeatureTypes.Faces, VisualFeatureTypes.ImageType,
                VisualFeatureTypes.Tags
            };

            static void Main(string[] args)
            {
                ComputerVisionClient computerVision = new ComputerVisionClient(
                    new ApiKeyServiceClientCredentials(subscriptionKey),
                    new System.Net.Http.DelegatingHandler[] { });

                // You must use the same region as you used to get your subscription
                // keys. For example, if you got your subscription keys from westus,
                // replace "westcentralus" with "westus".
                //
                // Free trial subscription keys are generated in the "westus"
                // region. If you use a free trial subscription key, you shouldn't
                // need to change the region.

                // Specify the Azure region
                computerVision.Endpoint = "https://westcentralus.api.cognitive.microsoft.com";

                Console.WriteLine("Images being analyzed ...");
                var t1 = AnalyzeRemoteAsync(computerVision, remoteImageUrl);
                var t2 = AnalyzeLocalAsync(computerVision, localImagePath);

                Task.WhenAll(t1, t2).Wait(5000);
                Console.WriteLine("Press ENTER to exit");
                Console.ReadLine();
            }

            // Analyze a remote image
            private static async Task AnalyzeRemoteAsync(
                ComputerVisionClient computerVision, string imageUrl)
            {
                if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
                {
                    Console.WriteLine(
                        "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                    return;
                }

                ImageAnalysis analysis =
                    await computerVision.AnalyzeImageAsync(imageUrl, features);
                DisplayResults(analysis, imageUrl);
            }

            // Analyze a local image
            private static async Task AnalyzeLocalAsync(
                ComputerVisionClient computerVision, string imagePath)
            {
                if (!File.Exists(imagePath))
                {
                    Console.WriteLine(
                        "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                    return;
                }

                using (Stream imageStream = File.OpenRead(imagePath))
                {
                    ImageAnalysis analysis = await computerVision.AnalyzeImageInStreamAsync(
                        imageStream, features);
                    DisplayResults(analysis, imagePath);
                }
            }

            // Display the most relevant caption for the image
            private static void DisplayResults(ImageAnalysis analysis, string imageUri)
            {
                Console.WriteLine(imageUri);
                if (analysis.Description.Captions.Count != 0)
                {
                    Console.WriteLine(analysis.Description.Captions[0].Text + "\n");
                }
                else
                {
                    Console.WriteLine("No description generated.");
                }
            }
        }
    }
    ```

1. Vervang `<Subscription Key>` door uw geldige abonnementssleutel.
1. Wijzig zo nodig `computerVision.Endpoint` in de Azure-regio die is gekoppeld aan uw abonnementssleutels.
1. Vervang `<LocalImage>` door het pad en de naam van een lokaal bestand.
1. Stel `remoteImageUrl` desgewenst in op een andere afbeeldings-URL.
1. Voer het programma uit.

## <a name="examine-the-response"></a>Het antwoord bekijken

Een geslaagd antwoord geeft het meest relevante bijschrift voor elke afbeelding. U kunt de methode `DisplayResults` wijzigen om verschillende afbeeldingsgegevens te genereren. Zie de methode [AnalyzeLocalAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions.analyzeimageinstreamasync?view=azure-dotnet) voor meer informatie.

Zie [API-snelstartgidsen: Een lokale afbeelding analyseren met C#](../QuickStarts/CSharp-analyze.md#examine-the-response) voor een voorbeeld van onbewerkte JSON-uitvoer.

```console
https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg
a large waterfall over a rocky cliff
```

## <a name="next-steps"></a>Volgende stappen

Bekijk de Computer Vision-API's die worden gebruikt om een afbeelding te analyseren, beroemdheden en oriëntatiepunten te detecteren, een miniatuur te maken en gedrukte en handgeschreven tekst te verkrijgen.

> [!div class="nextstepaction"]
> [De Computer Vision-API's bekijken](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
