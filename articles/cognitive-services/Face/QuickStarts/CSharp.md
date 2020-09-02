---
title: 'Snelstart: Gezichten in een afbeelding detecteren met de Azure REST API en C#'
titleSuffix: Azure Cognitive Services
description: In deze snelstart gebruikt u de Azure Face REST API met C# om gezichten in een afbeelding te detecteren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: fe6def8a935fcc6f093c4489409a3bcde829ac10
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89294931"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-c"></a>Snelstart: Gezichten in een afbeelding detecteren met de Face REST API en C#

In deze quickstart gebruikt u de Azure Face REST API met C# om menselijke gezichten in een afbeelding te detecteren.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Een Face-resource maken"  target="_blank">maakt u een Face-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Face-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
- Een versie van [Visual Studio](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Het Visual Studio-project maken

1. Maak in Visual Studio een nieuw **Console-app (.NET Framework)**-project en noem het **FaceDetection**.
1. Als uw oplossing nog meer projecten bevat, selecteert u deze als het enkele opstartproject.

## <a name="add-face-detection-code"></a>Gezichtsdetectiecode toevoegen

Open het bestand *Program.cs* van het nieuwe project. Hier voegt u de code toe voor het laden van afbeeldingen en detecteren van gezichten.

### <a name="include-namespaces"></a>Naamruimten opnemen

Voeg aan het begin van het bestand *Program.cs* de volgende `using`-instructies toe.

```csharp
using System;
using System.IO;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
```

### <a name="add-essential-fields"></a>Essentiële velden toevoegen

Voeg de klasse **Program** toe, die de volgende velden bevat. Met deze gegevens wordt aangegeven hoe de Face-service moet worden verbonden en waar de invoergegevens kunnen worden opgehaald. U moet het veld `subscriptionKey` bijwerken met de waarde van uw abonnementssleutel en u moet mogelijk de tekenreeks `uriBase` zo wijzigen dat deze de eindpunttekenreeks van uw resource bevat.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

```csharp
namespace DetectFace
{
    class Program
    {

        // Replace <Subscription Key> with your valid subscription key.
        const string subscriptionKey = "<Subscription Key>";

        // replace <myresourcename> with the string found in your endpoint URL
        const string uriBase =
            "https://<myresourcename>.cognitiveservices.azure.com/face/v1.0/detect";
```

### <a name="receive-image-input"></a>Invoer van de afbeelding ontvangen

Voeg de volgende code toe aan de methode **Main** in de klasse **Program**. Met deze code wordt een prompt naar de console geschreven waarin de gebruiker wordt gevraagd een afbeeldings-URL in te voeren. Vervolgens wordt een andere methode, **MakeAnalysisRequest**, aangeroepen om de afbeelding op die locatie te verwerken.

```csharp
        static void Main(string[] args)
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Detect faces:");
            Console.Write(
                "Enter the path to an image with faces that you wish to analyze: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                try
                {
                    MakeAnalysisRequest(imageFilePath);
                    Console.WriteLine("\nWait a moment for the results to appear.\n");
                }
                catch (Exception e)
                {
                    Console.WriteLine("\n" + e.Message + "\nPress Enter to exit...\n");
                }
            }
            else
            {
                Console.WriteLine("\nInvalid file path.\nPress Enter to exit...\n");
            }
            Console.ReadLine();
        }
```

### <a name="call-the-face-detection-rest-api"></a>De REST API voor de gezichtsdetectie aanroepen

Voeg de volgende methode toe aan de klasse **Program**. Hiermee wordt een REST-aanroep naar de Face-API gemaakt om informatie over het gezicht te detecteren in de externe afbeelding (de tekenreeks `requestParameters` geeft aan welke gezichtskenmerken moeten worden opgehaald). Vervolgens worden de uitvoergegevens naar een JSON-tekenreeks geschreven.

In de volgende stappen definieert u de hulpmethoden.

```csharp
        // Gets the analysis of the specified image by using the Face REST API.
        static async void MakeAnalysisRequest(string imageFilePath)
        {
            HttpClient client = new HttpClient();

            // Request headers.
            client.DefaultRequestHeaders.Add(
                "Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request parameters. A third optional parameter is "details".
            string requestParameters = "returnFaceId=true&returnFaceLandmarks=false" +
                "&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses," +
                "emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

            // Assemble the URI for the REST API Call.
            string uri = uriBase + "?" + requestParameters;

            HttpResponseMessage response;

            // Request body. Posts a locally stored JPEG image.
            byte[] byteData = GetImageAsByteArray(imageFilePath);

            using (ByteArrayContent content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json"
                // and "multipart/form-data".
                content.Headers.ContentType =
                    new MediaTypeHeaderValue("application/octet-stream");

                // Execute the REST API call.
                response = await client.PostAsync(uri, content);

                // Get the JSON response.
                string contentString = await response.Content.ReadAsStringAsync();

                // Display the JSON response.
                Console.WriteLine("\nResponse:\n");
                Console.WriteLine(JsonPrettyPrint(contentString));
                Console.WriteLine("\nPress Enter to exit...");
            }
        }
```

### <a name="process-the-input-image-data"></a>De invoergegevens van de afbeelding verwerken

Voeg de volgende methode toe aan de klasse **Program**. Met deze methode wordt de afbeelding op de opgegeven URL geconverteerd naar een bytematrix.

```csharp
        // Returns the contents of the specified file as a byte array.
        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            using (FileStream fileStream =
                new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
            {
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
```

### <a name="parse-the-json-response"></a>Het JSON-antwoord parseren

Voeg de volgende methode toe aan de klasse **Program**. Met deze methode wordt de JSON-invoer gemakkelijker leesbaar gemaakt. Met uw app worden deze tekenreeksgegevens naar de console geschreven. U kunt de klasse en naamruimte vervolgens sluiten.

```csharp
        // Formats the given JSON string by adding line breaks and indents.
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            int offset = 0;
            int indentLength = 3;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\'':
                        if (quote) ignore = !ignore;
                        break;
                }

                if (quote)
                    sb.Append(ch);
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (ch != ' ') sb.Append(ch);
                            break;
                    }
                }
            }

            return sb.ToString().Trim();
        }
    }
}
```

## <a name="run-the-app"></a>De app uitvoeren

Bij een geslaagd antwoord worden de Face-gegevens weergegeven in een makkelijk leesbare JSON-indeling. Bijvoorbeeld:

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
      "faceRectangle": {
         "top": 131,
         "left": 177,
         "width": 162,
         "height": 162
      },
      "faceAttributes": {
         "smile": 0.0,
         "headPose": {
            "pitch": 0.0,
            "roll": 0.1,
            "yaw": -32.9
         },
         "gender": "female",
         "age": 22.9,
         "facialHair": {
            "moustache": 0.0,
            "beard": 0.0,
            "sideburns": 0.0
         },
         "glasses": "NoGlasses",
         "emotion": {
            "anger": 0.0,
            "contempt": 0.0,
            "disgust": 0.0,
            "fear": 0.0,
            "happiness": 0.0,
            "neutral": 0.986,
            "sadness": 0.009,
            "surprise": 0.005
         },
         "blur": {
            "blurLevel": "low",
            "value": 0.06
         },
         "exposure": {
            "exposureLevel": "goodExposure",
            "value": 0.67
         },
         "noise": {
            "noiseLevel": "low",
            "value": 0.0
         },
         "makeup": {
            "eyeMakeup": true,
            "lipMakeup": true
         },
         "accessories": [

         ],
         "occlusion": {
            "foreheadOccluded": false,
            "eyeOccluded": false,
            "mouthOccluded": false
         },
         "hair": {
            "bald": 0.0,
            "invisible": false,
            "hairColor": [
               {
                  "color": "brown",
                  "confidence": 1.0
               },
               {
                  "color": "black",
                  "confidence": 0.87
               },
               {
                  "color": "other",
                  "confidence": 0.51
               },
               {
                  "color": "blond",
                  "confidence": 0.08
               },
               {
                  "color": "red",
                  "confidence": 0.08
               },
               {
                  "color": "gray",
                  "confidence": 0.02
               }
            ]
         }
      }
   }
]
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een eenvoudige .NET-consoletoepassing gemaakt die gebruikmaakt van REST-aanroepen met de Azure Face-service voor het detecteren van gezichten in een afbeelding en het retourneren van de kenmerken. Lees het naslagmateriaal bij de Face-API voor meer informatie over de ondersteunde scenario's.

> [!div class="nextstepaction"]
> [Face-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
