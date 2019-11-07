---
title: 'Snelstartgids: een lokale installatie kopie analyseren-REST,C#'
titleSuffix: Azure Cognitive Services
description: In deze snelstart analyseert u een lokale afbeelding met behulp van de Computer Vision-API met C#.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8e984c76544d2c9a56a2ec93de50593c746bb18e
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647943"
---
# <a name="quickstart-analyze-a-local-image-using-the-computer-vision-rest-api-and-c"></a>Snelstartgids: een lokale installatie kopie analyseren met behulp van de Computer Vision REST API enC#

In deze quickstart analyseert u een lokaal opgeslagen afbeelding om visuele kenmerken te verkrijgen met behulp van de REST-API van Computer Vision. Met de methode [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) (afbeelding analyseren) kunt u visuele kenmerken verkrijgen op basis van de afbeeldingsinhoud.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

- U hebt [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) of hoger nodig.
- U moet beschikken over een abonnementssleutel voor Computer Vision. U kunt een gratis proef versie verkrijgen van [Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Of volg de instructies in [Create a cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) om u te abonneren op computer vision en uw sleutel op te halen. Vervolgens kunt u [omgevings variabelen maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel-en service-eindpunt reeks, respectievelijk met de naam `COMPUTER_VISION_SUBSCRIPTION_KEY` en `COMPUTER_VISION_ENDPOINT`.

## <a name="create-and-run-the-sample-application"></a>De voorbeeldtoepassing maken en uitvoeren

Voer de volgende stappen uit om het voorbeeld in Visual Studio te maken:

1. Maak een nieuwe Visual Studio-oplossing in Visual Studio met behulp C# van de sjabloon Visual console App (.NET Framework).
1. Installeer het pakket Newtonsoft.Json NuGet.
    1. Klik in het menu op **Extra**, selecteer **NuGet Package Manager** en vervolgens **NuGet-pakketten voor oplossing beheren**.
    1. Klik op het tabblad **Bladeren** en typ 'Newtonsoft.Json' in het vak **Zoeken**.
    1. Selecteer **Newtonsoft.Json** wanneer dit wordt weergegeven en klik vervolgens op het selectievakje naast uw projectnaam en op **Installeren**.
1. Voer het programma uit.
1. Voer bij de prompt het pad naar een lokale afbeelding in.

```csharp
using Newtonsoft.Json.Linq;
using System;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;

namespace CSHttpClientSample
{
    static class Program
    {
        // Add your Computer Vision subscription key and endpoint to your environment variables.
        static string subscriptionKey = Environment.GetEnvironmentVariable("COMPUTER_VISION_SUBSCRIPTION_KEY");

        static string endpoint = Environment.GetEnvironmentVariable("COMPUTER_VISION_ENDPOINT");
        
        // the Analyze method endpoint
        static string uriBase = endpoint + "vision/v2.1/analyze";

        static void Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Analyze an image:");
            Console.Write(
                "Enter the path to the image you wish to analyze: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                // Call the REST API method.
                Console.WriteLine("\nWait a moment for the results to appear.\n");
                MakeAnalysisRequest(imageFilePath).Wait();
            }
            else
            {
                Console.WriteLine("\nInvalid file path");
            }
            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets the analysis of the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file to analyze.</param>
        static async Task MakeAnalysisRequest(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameters. A third optional parameter is "details".
                // The Analyze Image method returns information about the following
                // visual features:
                // Categories:  categorizes image content according to a
                //              taxonomy defined in documentation.
                // Description: describes the image content with a complete
                //              sentence in supported languages.
                // Color:       determines the accent color, dominant color, 
                //              and whether an image is black & white.
                string requestParameters =
                    "visualFeatures=Categories,Description,Color";

                // Assemble the URI for the REST API method.
                string uri = uriBase + "?" + requestParameters;

                HttpResponseMessage response;

                // Read the contents of the specified local image
                // into a byte array.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                // Add the byte array as an octet stream to the request body.
                using (ByteArrayContent content = new ByteArrayContent(byteData))
                {
                    // This example uses the "application/octet-stream" content type.
                    // The other content types you can use are "application/json"
                    // and "multipart/form-data".
                    content.Headers.ContentType =
                        new MediaTypeHeaderValue("application/octet-stream");

                    // Asynchronously call the REST API method.
                    response = await client.PostAsync(uri, content);
                }

                // Asynchronously get the JSON response.
                string contentString = await response.Content.ReadAsStringAsync();

                // Display the JSON response.
                Console.WriteLine("\nResponse:\n\n{0}\n",
                    JToken.Parse(contentString).ToString());
            }
            catch (Exception e)
            {
                Console.WriteLine("\n" + e.Message);
            }
        }

        /// <summary>
        /// Returns the contents of the specified file as a byte array.
        /// </summary>
        /// <param name="imageFilePath">The image file to read.</param>
        /// <returns>The byte array of the image data.</returns>
        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            // Open a read-only file stream for the specified file.
            using (FileStream fileStream =
                new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
            {
                // Read the file's contents into a byte array.
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
}
```

## <a name="examine-the-response"></a>Het antwoord bekijken

Een geslaagd antwoord wordt geretourneerd in JSON-indeling. De voorbeeldtoepassing parseert en geeft een geslaagd antwoord weer in het consolevenster dat vergelijkbaar is met het volgende voorbeeld:

```json
{
    "categories": [
        {
            "name": "abstract_",
            "score": 0.00390625
        },
        {
            "name": "others_",
            "score": 0.0234375
        },
        {
            "name": "outdoor_",
            "score": 0.00390625
        }
    ],
    "description": {
        "tags": [
            "road",
            "building",
            "outdoor",
            "street",
            "night",
            "black",
            "city",
            "white",
            "light",
            "sitting",
            "riding",
            "man",
            "side",
            "empty",
            "rain",
            "corner",
            "traffic",
            "lit",
            "hydrant",
            "stop",
            "board",
            "parked",
            "bus",
            "tall"
        ],
        "captions": [
            {
                "text": "a close up of an empty city street at night",
                "confidence": 0.7965622853462756
            }
        ]
    },
    "requestId": "dddf1ac9-7e66-4c47-bdef-222f3fe5aa23",
    "metadata": {
        "width": 3733,
        "height": 1986,
        "format": "Jpeg"
    },
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": [
            "Black",
            "Grey"
        ],
        "accentColor": "666666",
        "isBWImg": true
    }
}
```

## <a name="next-steps"></a>Volgende stappen

Een eenvoudige Windows-toepassing verkennen die Computer Vision gebruikt om optische tekenherkenning (OCR) uit te voeren; slim bijgesneden miniaturen maken; plus visuele kenmerken, inclusief gezichten, in een afbeelding detecteren, categoriseren, labelen en beschrijven.

> [!div class="nextstepaction"]
> [Zelfstudie voor Computer Vision API met C#](../Tutorials/CSharpTutorial.md)
