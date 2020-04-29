---
title: 'Snelstartgids: Image Insights ophalen met behulp van de REST API en C#-Bing Visual Search'
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
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75446669"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>Snelstartgids: Image Insights ophalen met behulp van de Bing Visual Search REST API en C #

In deze Quick start ziet u hoe u een installatie kopie uploadt naar de Bing Visual Search-API en hoe u de inzichten die het retourneert, weergeeft.

## <a name="prerequisites"></a>Vereisten

* Alle edities van [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Het [JSON.NET-Framework](https://www.newtonsoft.com/json), beschikbaar als een NuGet-pakket.
* Als u Linux/MacOS gebruikt, kunt u deze toepassing uitvoeren met [mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Een project maken en initialiseren

1. Maak in Visual Studio een nieuwe console oplossing met de naam BingSearchApisQuickStart. Voeg de volgende naam ruimten toe aan het bestand met de hoofd code:

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Voeg variabelen toe voor de sleutel van het abonnement, het eind punt en het pad naar de installatie kopie die u wilt uploaden. `uriBase`Dit kan het globale eind punt zijn of het eind punt van het [aangepaste subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) dat wordt weer gegeven in de Azure portal voor uw resource:

    ```csharp
        const string accessKey = "<my_subscription_key>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<path_to_image>";
    ```

3. Maak een methode met `GetImageFileName()` de naam om het pad voor de installatie kopie op te halen:
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

4. Maak een methode om de binaire gegevens van de installatie kopie op te halen:

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>De formuliergegevens maken

Als u een lokale installatie kopie wilt uploaden, bouwt u eerst de formulier gegevens om deze naar de API te verzenden. De formulier gegevens moeten de `Content-Disposition` koptekst bevatten, de `name` para meter moet worden ingesteld op "afbeelding" en de `filename` para meter kan worden ingesteld op een wille keurige teken reeks. De inhoud van het formulier bevat de binaire gegevens van de installatie kopie. De maximale afbeeldings grootte die u kunt uploaden, is 1 MB.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

1. Voeg grens teken reeksen toe om de POST-formulier gegevens op te maken. Met grens teken reeksen wordt de begin-, eind-en nieuwe regel tekens voor de gegevens bepaald:

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

2. Gebruik de volgende variabelen om para meters toe te voegen aan de formulier gegevens:

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

3. Maak een functie met `BuildFormDataStart()` de naam om het begin van de formulier gegevens te maken met behulp van de grens teken reeksen en het pad naar de afbeelding:
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

4. Maak een functie met `BuildFormDataEnd()` de naam om het einde van de formulier gegevens te maken met behulp van de grens teken reeksen:
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>De Bing Visual Search-API aanroepen

1. Maak een functie om het Bing Visual Search-eind punt aan te roepen en het JSON-antwoord te retour neren. De functie neemt het begin en het einde van de formulier gegevens, een byte matrix met de afbeeldings gegevens en `contentType` een waarde.

2. Gebruik een `WebRequest` voor het opslaan van de URI, contentType waarde en headers.  

3. Gebruik `request.GetRequestStream()` om uw formulier-en afbeeldings gegevens te schrijven en ontvang vervolgens het antwoord. De functie moet er ongeveer als volgt uitzien:
        
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

## <a name="create-the-main-method"></a>De methode Main maken

1. In de `Main` methode van uw toepassing haalt u de bestands naam en binaire gegevens van uw installatie kopie op:

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. Maak de POST-body door de grens hiervoor te bepalen. Vervolgens roept `startFormData()` u `endFormData` de formulier gegevens op en maakt u deze:

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. Maak de `ContentType` waarde op basis `CONTENT_TYPE_HEADER_PARAMS` van de notatie en de grens van het formulier gegevens:

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. De API-reactie ophalen door `BingImageSearch()` het antwoord aan te roepen en af te drukken:

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>Met behulp van HttpClient

Als u gebruikt `HttpClient`, kunt u de `MultipartFormDataContent` -klasse gebruiken om de formulier gegevens te maken. Gebruik alleen de volgende code gedeelten om de bijbehorende methoden in het vorige voor beeld te vervangen.

Vervang de `Main` -methode door de volgende code:

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

Vervang de `BingImageSearch` -methode door de volgende code:

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
> [Een Visual Search Web-app met één pagina maken](../tutorial-bing-visual-search-single-page-app.md)
