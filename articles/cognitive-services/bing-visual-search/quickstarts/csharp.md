---
title: 'Snelstart: verkrijg afbeeldingsinzichten met behulp van de REST API en C# - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Leer hoe u een afbeelding uploadt naar de Bing Visual Search-API en inzichten in de afbeelding verkrijgt.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: 07ecac46ab13058d308c17c5747701ee5ed577fc
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446669"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>Snelstart: verkrijg afbeeldingsinzichten met de Bing Visual Search REST API en C #

Deze quickstart laat zien hoe u een afbeelding uploadt naar de Bing Visual Search API en de inzichten die deze retourneert bekijken.

## <a name="prerequisites"></a>Vereisten

* Elke editie van [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Het [Json.NET framework](https://www.newtonsoft.com/json), beschikbaar als Een NuGet pakket.
* Als u Linux/MacOS gebruikt, u deze toepassing uitvoeren met [Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Een project maken en initialiseren

1. Maak in Visual Studio een nieuwe consoleoplossing met de naam BingSearchApisQuickStart. Voeg de volgende naamruimten toe aan het hoofdcodebestand:

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Voeg variabelen toe voor uw abonnementssleutel, eindpunt en pad naar de afbeelding die u wilt uploaden. `uriBase`kan het algemene eindpunt hieronder zijn of het [aangepaste eindpunt voor subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) dat wordt weergegeven in de Azure-portal voor uw bron:

    ```csharp
        const string accessKey = "<my_subscription_key>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<path_to_image>";
    ```

3. Maak een `GetImageFileName()` methode met de naam om het pad voor uw afbeelding te krijgen:
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

4. Maak een methode om de binaire gegevens van de afbeelding te krijgen:

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>De formuliergegevens maken

Als u een lokale afbeelding wilt uploaden, bouwt u eerst de formuliergegevens die u naar de API wilt verzenden. De formuliergegevens moeten `Content-Disposition` de `name` koptekst bevatten, de parameter `filename` moet worden ingesteld op 'afbeelding' en de parameter kan op elke tekenreeks worden ingesteld. De inhoud van het formulier bevat de binaire gegevens van de afbeelding. De maximale afbeeldingsgrootte die u uploaden is 1 MB.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

1. Voeg grenstekenreeksen toe om de formuliergegevens van POST op te maken. Grenstekenreeksen bepalen de begin-, eind- en nieuwe regeltekens voor de gegevens:

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

2. Gebruik de volgende variabelen om parameters toe te voegen aan de formuliergegevens:

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

3. Maak een `BuildFormDataStart()` functie met de naam om de beginvan de formuliergegevens te maken met behulp van de grenstekenreeksen en het afbeeldingspad:
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

4. Maak een `BuildFormDataEnd()` functie met de naam om het einde van de formuliergegevens te maken met behulp van de grenstekenreeksen:
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>De Bing Visual Search-API aanroepen

1. Maak een functie om het Bing Visual Search-eindpunt aan te roepen en het JSON-antwoord terug te geven. De functie neemt het begin en het einde van de formuliergegevens, `contentType` een bytearray met de afbeeldingsgegevens en een waarde.

2. Gebruik een `WebRequest` voor het opslaan van de URI, contentType waarde en headers.  

3. Gebruik `request.GetRequestStream()` om uw formulier- en afbeeldingsgegevens te schrijven en vervolgens het antwoord te krijgen. Uw functie moet vergelijkbaar zijn met de onderstaande:
        
    ```csharp
        static string BingImageSearch(string startFormData, string endFormData, byte[] image, string contentTypeValue)
        {
            WebRequest request = HttpWebRequest.Create(uriBase);
            request.ContentType = contentTypeValue;
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            request.Method = "POST";

            // Writes the boundary and Content-Disposition header, then writes
            // the image binary, and finishes by writing the closing boundary.
            using (Stream requestStream = request.GetRequestStream())
            {
                StreamWriter writer = new StreamWriter(requestStream);
                writer.Write(startFormData);
                writer.Flush();
                requestStream.Write(image, 0, image.Length);
                writer.Write(endFormData);
                writer.Flush();
                writer.Close();
            }

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            return json;
        }
    ```

## <a name="create-the-main-method"></a>De hoofdmethode maken

1. Download `Main` in de methode van uw toepassing de bestandsnaam en binaire gegevens van uw afbeelding:

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. Maak de POST-body door de grens hiervoor te bepalen. Bel `startFormData()` vervolgens `endFormData` en maak de formuliergegevens:

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. De `ContentType` waarde maken `CONTENT_TYPE_HEADER_PARAMS` op opmaak en de grens van formuliergegevens:

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. Krijg het API-antwoord door het antwoord aan te roepen `BingImageSearch()` en af te drukken:

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>Met behulp van HttpClient

Als u `HttpClient`de klasse `MultipartFormDataContent` gebruikt, u de klasse gebruiken om de formuliergegevens te bouwen. Gebruik gewoon de volgende secties van de code om de overeenkomstige methoden in het vorige voorbeeld te vervangen.

Vervang `Main` de methode door deze code:

```csharp
        static void Main()
        {
            try
            {
                Console.OutputEncoding = System.Text.Encoding.UTF8;

                if (accessKey.Length == 32)
                {
                    if (IsImagePathSet(imagePath))
                    {
                        var filename = GetImageFileName(imagePath);
                        Console.WriteLine("Getting image insights for image: " + filename);
                        var imageBinary = GetImageBinary(imagePath);

                        var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
                        var json = BingImageSearch(imageBinary, boundary, uriBase, accessKey);

                        Console.WriteLine("\nJSON Response:\n");
                        Console.WriteLine(JsonPrettyPrint(json));
                    }
                }
                else
                {
                    Console.WriteLine("Invalid Bing Visual Search API subscription key!");
                    Console.WriteLine("Please paste yours into the source code.");
                }

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }
        }
```

Vervang `BingImageSearch` de methode door deze code:

```csharp
        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response.
        /// </summary>
        static string BingImageSearch(byte[] image, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new ByteArrayContent(image), "image", "myimage");
            requestMessage.Content = content;

            var httpClient = new HttpClient();

            Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
            HttpResponseMessage httpResponse = httpRequest.Result;
            HttpStatusCode statusCode = httpResponse.StatusCode;
            HttpContent responseContent = httpResponse.Content;

            string json = null;

            if (responseContent != null)
            {
                Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                json = stringContentsTask.Result;
            }

            return json;
        }
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app voor visueel zoeken met één pagina maken](../tutorial-bing-visual-search-single-page-app.md)
