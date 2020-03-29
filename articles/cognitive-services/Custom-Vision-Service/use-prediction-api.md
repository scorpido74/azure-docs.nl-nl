---
title: 'Gebruik een voorspellingseindpunt om afbeeldingen programmatisch te testen met classificatie: Custom Vision'
titleSuffix: Azure Cognitive Services
description: Lees hoe u de API gebruikt om afbeeldingen programmatisch te testen met uw Custom Vision Service-classificatie.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: anroth
ms.openlocfilehash: dcb12da680d70e1f0ce4cd763bee340bb3416c6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169951"
---
# <a name="use-your-model-with-the-prediction-api"></a>Uw model gebruiken met de voorspellings-API

Nadat u uw model hebt getraind, u afbeeldingen programmatisch testen door ze in te dienen bij het eindpunt van de Voorspellings-API.

> [!NOTE]
> In dit document ziet u hoe u een afbeelding bij de voorspellings-API kunt indienen met behulp van C#. Zie de [verwijzing naar de voorspellingsAPI](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)voor meer informatie en voorbeelden .

## <a name="publish-your-trained-iteration"></a>Uw getrainde iteratie publiceren

Op de [Custom Vision-webpagina](https://customvision.ai) selecteert u uw project en vervolgens selecteert u het tabblad __Prestaties__.

Als u afbeeldingen wilt indienen bij de Voorspellings-API, moet u eerst uw iteratie publiceren voor voorspelling, wat kan worden gedaan door __Publiceren__ te selecteren en een naam voor de gepubliceerde iteratie op te geven. Hierdoor is uw model toegankelijk voor de Voorspellings-API van uw Azure-bron voor Custom Vision.

![Het prestatietabblad wordt weergegeven, met een rode rechthoek rond de knop Publiceren.](./media/use-prediction-api/unpublished-iteration.png)

Zodra uw model is gepubliceerd, ziet u een label 'Gepubliceerd' worden weergegeven naast uw iteratie in de linkerzijbalk en wordt de naam ervan weergegeven in de beschrijving van de iteratie.

![Het prestatietabblad wordt weergegeven, met een rode rechthoek rond het label Gepubliceerd en de naam van de gepubliceerde iteratie.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>De URL en voorspellingssleutel ophalen

Zodra uw model is gepubliceerd, u de vereiste informatie ophalen door __de URL van Voorspelling te__selecteren. Hiermee wordt een dialoogvenster geopend met informatie voor het gebruik van de Voorspellings-API, inclusief de __URL voor voorspelling__ en __voorspellingssleutel.__

![Het prestatietabblad wordt weergegeven met een rode rechthoek rond de knop URL van voorspelling.](./media/use-prediction-api/published-iteration-prediction-url.png)

![Het prestatietabblad wordt weergegeven met een rode rechthoek rond de URL-waarde Voor het gebruiken van een afbeeldingsbestand en de waarde Voorspelling-sleutel.](./media/use-prediction-api/prediction-api-info.png)


In deze handleiding gebruikt u een lokale afbeelding, dus kopieer de URL onder **Als u een afbeeldingsbestand** hebt naar een tijdelijke locatie. Kopieer ook de bijbehorende __Prediction-Key-waarde.__

## <a name="create-the-application"></a>De toepassing maken

1. Maak in Visual Studio een nieuwe C#-consoletoepassing.

1. Gebruik de volgende code als de hoofdtekst van het bestand __Program.cs__.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "<Your prediction key>");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "<Your prediction URL>";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

1. Voer de volgende informatie in:
   * Stel `namespace` het veld in op de naam van uw project.
   * Vervang de `<Your prediction key>` tijdelijke aanduiding door de sleutelwaarde die u eerder hebt opgehaald.
   * Vervang de `<Your prediction URL>` tijdelijke aanduiding door de URL die u eerder hebt opgehaald.

## <a name="run-the-application"></a>De toepassing uitvoeren

Wanneer u de toepassing uitvoert, wordt u gevraagd een pad naar een afbeeldingsbestand in de console in te voeren. De afbeelding wordt vervolgens ingediend bij de Voorspellings-API en de voorspellingsresultaten worden geretourneerd als een tekenreeks met JSON-indeling. Het volgende is een voorbeeldreactie.

```json
{
    "Id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "Project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "Iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "Created":"2019-03-20T16:47:31.322Z",
    "Predictions":[
        {"TagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","TagName":"cat", "Probability":1.0},
        {"TagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","TagName":"dog", "Probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>Volgende stappen

In deze handleiding hebt u geleerd hoe u afbeeldingen verzenden naar uw aangepaste afbeeldingsclassificatie/detector en een antwoord programmatisch ontvangen met de C# SDK. Leer vervolgens hoe u end-to-end-scenario's voltooien met C#, of aan de slag gaan met een SDK met een andere taal.

* [Snelstart: .NET SDK](csharp-tutorial.md)
* [Snelstart: Python SDK](python-tutorial.md)
* [Snelstart: Java SDK](java-tutorial.md)
* [Snelstart: Node SDK](node-tutorial.md)
* [Snelstart: Go SDK](go-tutorial.md)
