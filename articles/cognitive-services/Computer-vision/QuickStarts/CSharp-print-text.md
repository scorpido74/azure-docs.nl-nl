---
title: 'Snelstart: gedrukte tekst (OCR) extraheren - REST, C #'
titleSuffix: Azure Cognitive Services
description: In deze snelstart extraheert u tekst uit een afbeelding met behulp van de Computer Vision-API met C#.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 2afec329204e6ae3eb0f04fbc51acd8b4c7ca7f5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405135"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-computer-vision-rest-api-and-c"></a>Snelstart: gedrukte tekst (OCR) extraheren met de Computer Vision REST API en C #

> [!NOTE]
> Als u Tekst in de Engelse taal extraheert, u overwegen de nieuwe [bewerking Lezen te](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text)gebruiken. Er is een [C# quickstart](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text) beschikbaar. 

In deze quickstart haalt u afgedrukte tekst met optische tekenherkenning (OCR) uit een afbeelding met behulp van de Computer Vision REST API. Met de functie [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) kunt u gedrukte tekst in een afbeelding detecteren en de herkende tekens naar een machinaal leesbare tekenstroom extraheren.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) voordat u begint.

## <a name="prerequisites"></a>Vereisten

- U hebt [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) of hoger nodig.
- U moet beschikken over een abonnementssleutel voor Computer Vision. U een gratis testsleutel krijgen van [Try Cognitive Services.](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) Of volg de instructies in [Een Cognitive Services-account maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) om u te abonneren op Computer Vision en uw sleutel te krijgen. Maak vervolgens [omgevingsvariabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de tekenreeks sleutel- `COMPUTER_VISION_SUBSCRIPTION_KEY` en `COMPUTER_VISION_ENDPOINT`serviceeindpunt, benoemd en , respectievelijk.

## <a name="create-and-run-the-sample-application"></a>De voorbeeldtoepassing maken en uitvoeren

Voer de volgende stappen uit om het voorbeeld in Visual Studio te maken:

1. Maak een nieuwe Visual Studio-oplossing in Visual Studio met behulp van het Visual C# Console App-sjabloon.
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
        
        // the OCR method endpoint
        static string uriBase = endpoint + "vision/v2.1/ocr";

        static async Task Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Optical Character Recognition:");
            Console.Write("Enter the path to an image with text you wish to read: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                // Call the REST API method.
                Console.WriteLine("\nWait a moment for the results to appear.\n");
                await MakeOCRRequest(imageFilePath);
            }
            else
            {
                Console.WriteLine("\nInvalid file path");
            }
            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets the text visible in the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file with printed text.</param>
        static async Task MakeOCRRequest(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameters. 
                // The language parameter doesn't specify a language, so the 
                // method detects it automatically.
                // The detectOrientation parameter is set to true, so the method detects and
                // and corrects text orientation before detecting text.
                string requestParameters = "language=unk&detectOrientation=true";

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
    "language": "en",
    "textAngle": -1.5000000000000335,
    "orientation": "Up",
    "regions": [
        {
            "boundingBox": "154,49,351,575",
            "lines": [
                {
                    "boundingBox": "165,49,340,117",
                    "words": [
                        {
                            "boundingBox": "165,49,63,109",
                            "text": "A"
                        },
                        {
                            "boundingBox": "261,50,244,116",
                            "text": "GOAL"
                        }
                    ]
                },
                {
                    "boundingBox": "165,169,339,93",
                    "words": [
                        {
                            "boundingBox": "165,169,339,93",
                            "text": "WITHOUT"
                        }
                    ]
                },
                {
                    "boundingBox": "159,264,342,117",
                    "words": [
                        {
                            "boundingBox": "159,264,64,110",
                            "text": "A"
                        },
                        {
                            "boundingBox": "255,266,246,115",
                            "text": "PLAN"
                        }
                    ]
                },
                {
                    "boundingBox": "161,384,338,119",
                    "words": [
                        {
                            "boundingBox": "161,384,86,113",
                            "text": "IS"
                        },
                        {
                            "boundingBox": "274,387,225,116",
                            "text": "JUST"
                        }
                    ]
                },
                {
                    "boundingBox": "154,506,341,118",
                    "words": [
                        {
                            "boundingBox": "154,506,62,111",
                            "text": "A"
                        },
                        {
                            "boundingBox": "248,508,247,116",
                            "text": "WISH"
                        }
                    ]
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Volgende stappen

Een eenvoudige Windows-toepassing verkennen die Computer Vision gebruikt om optische tekenherkenning (OCR) uit te voeren; slim bijgesneden miniaturen maken; plus visuele kenmerken, inclusief gezichten, in een afbeelding detecteren, categoriseren, labelen en beschrijven.

> [!div class="nextstepaction"]
> [Zelfstudie voor Computer Vision API met C#](../Tutorials/CSharpTutorial.md)
