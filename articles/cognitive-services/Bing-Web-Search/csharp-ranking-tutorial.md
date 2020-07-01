---
title: Positie gebruiken om Zoek resultaten weer te geven
titleSuffix: Azure Cognitive Services
description: Laat zien hoe u het Bing RankingResponse-antwoord kunt gebruiken om Zoek resultaten weer te geven op volg orde van prioriteit.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: aahi
ms.openlocfilehash: 272a10e211e99e200b82807b188d828a9ece42d8
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85609432"
---
# <a name="build-a-console-app-search-client-in-c"></a>Een console-app zoeken-client in C bouwen #

Deze zelf studie laat zien hoe u een eenvoudige .NET Core-Console-app bouwt waarmee gebruikers de Bing Webzoekopdrachten-API kunnen doorzoeken en gerangschikte resultaten weer geven.

Deze zelf studie laat zien hoe u:

- Een eenvoudige query naar de Bing Webzoekopdrachten-API maken
- Query resultaten weer geven in gerangschikte volg orde

## <a name="prerequisites"></a>Vereisten

Als u de zelf studie wilt volgen, hebt u het volgende nodig:

* Een Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/cognitive-services/)
* Wanneer u uw Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" maakt u een Bing Search resource Maak "  target="_blank"> een Bing Search resource <span class="docon docon-navigate-external x-hidden-focus"></span> </a> in de Azure Portal om uw sleutel en eind punt op te halen. Nadat de app is geïmplementeerd, klikt **u op Ga naar resource**.
* De [Visual Studio IDE](https://www.visualstudio.com/downloads/).

## <a name="create-a-new-console-app-project"></a>Een nieuw console-app-project maken

Maak in Visual Studio een project met `Ctrl`+`Shift`+`N`.

Klik in het dialoog venster **Nieuw project** op **Visual C# > Windows Classic bureau blad > console-app (.NET Framework)**.

Noem de toepassing **MyConsoleSearchApp**en klik vervolgens op **OK**.

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>Het JSON.net NuGet-pakket toevoegen aan het project

Met JSON.net kunt u werken met de JSON-antwoorden die zijn geretourneerd door de API. Voeg het NuGet-pakket toe aan uw project:

- Klik in **Solution Explorer** met de rechter muisknop op het project en selecteer **NuGet-pakketten beheren...**.
- Zoek op het tabblad **Bladeren** naar `Newtonsoft.Json` . Selecteer de nieuwste versie en klik vervolgens op **installeren**.
- Klik op de knop **OK** in het venster **wijzigingen bekijken** .
- Sluit het tabblad Visual Studio met de titel **NuGet: MyConsoleSearchApp**.

## <a name="add-a-reference-to-systemweb"></a>Een verwijzing naar System. web toevoegen

Deze zelf studie is afhankelijk van de `System.Web` Assembly. Voeg een verwijzing naar deze assembly toe aan uw project:

- Klik in **Solution Explorer**met de rechter muisknop op **verwijzingen** en selecteer **referentie toevoegen...**
- Selecteer **assembly's > Framework**, Schuif omlaag en controleer **System. Web**
- Selecteer **OK**

## <a name="add-some-necessary-using-statements"></a>Enkele vereiste instructies gebruiken

Voor de code in deze zelf studie zijn drie extra instructies vereist. Voeg deze instructies toe onder de bestaande `using` instructies boven aan **Program.cs**:

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>De gebruiker vragen om een query

Open **Program.cs**in **Solution Explorer**. De `Main()` methode bijwerken:

```csharp
static void Main()
{
    // Get the user's query
    Console.Write("Enter Bing query: ");
    string userQuery = Console.ReadLine();
    Console.WriteLine();

    // Run the query and display the results
    RunQueryAndDisplayResults(userQuery);

    // Prevent the console window from closing immediately
    Console.WriteLine("\nHit ENTER to exit...");
    Console.ReadLine();
}
```

Deze methode:

- De gebruiker wordt gevraagd om een query
- Aanroepen `RunQueryAndDisplayResults(userQuery)` om de query uit te voeren en de resultaten weer te geven
- Wacht op invoer van de gebruiker om te voor komen dat het console venster onmiddellijk wordt gesloten.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Zoeken naar query resultaten met behulp van de Bing Webzoekopdrachten-API

Voeg vervolgens een methode toe waarmee de API wordt opgevraagd en de resultaten worden weer gegeven:

```csharp
static void RunQueryAndDisplayResults(string userQuery)
{
    try
    {
        // Create a query
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<YOUR_SUBSCRIPTION_KEY_GOES_HERE>");
        var queryString = HttpUtility.ParseQueryString(string.Empty);
        queryString["q"] = userQuery;
        var query = "https://api.cognitive.microsoft.com/bing/v7.0/search?" + queryString;

        // Run the query
        HttpResponseMessage httpResponseMessage = client.GetAsync(query).Result;

        // Deserialize the response content
        var responseContentString = httpResponseMessage.Content.ReadAsStringAsync().Result;
        Newtonsoft.Json.Linq.JObject responseObjects = Newtonsoft.Json.Linq.JObject.Parse(responseContentString);

        // Handle success and error codes
        if (httpResponseMessage.IsSuccessStatusCode)
        {
            DisplayAllRankedResults(responseObjects);
        }
        else
        {
            Console.WriteLine($"HTTP error status code: {httpResponseMessage.StatusCode.ToString()}");
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(e.Message);
    }
}
```

Deze methode:

- Hiermee maakt `HttpClient` u een query voor de webzoekopdrachten-API
- Hiermee wordt de `Ocp-Apim-Subscription-Key` http-header ingesteld die door Bing wordt gebruikt om de aanvraag te verifiëren
- Voert de aanvraag uit en gebruikt JSON.net om de resultaten te deserialiseren
- Aanroepen `DisplayAllRankedResults(responseObjects)` om alle resultaten weer te geven in de rang orde

Zorg ervoor dat u de waarde van instelt `Ocp-Apim-Subscription-Key` op uw abonnements sleutel.

## <a name="display-ranked-results"></a>Gerangschikte resultaten weer geven

Bekijk een voor beeld van een zoek opdracht op het web voordat u kunt zien hoe de resultaten in de rang orde worden weer gegeven:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346...",
        "totalEstimatedMatches" : 982000,
        "value" : [{
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
            "name" : "Contoso Sailing Club - Seattle",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/contososailingsea...",
            "snippet" : "Come sail with Contoso in Seattle...",
            "dateLastCrawled" : "2017-04-07T02:25:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#WebPages.6",
            "name" : "Contoso Sailing Lessons - Official Site",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/www.constososailinglessonsseat...",
            "snippet" : "Contoso sailing lessons in Seattle...",
            "dateLastCrawled" : "2017-04-09T14:30:00"
        },

        ...

        ],
        "someResultsRemoved" : true
    },
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#RelatedSearches",
        "value" : [{
            "text" : "sailing lessons",
            "displayText" : "sailing lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346E..."
        }

        ...

        ]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            }

            ...

            ]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}
```

`rankingResponse`In het JSON-object ([documentatie](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse)) wordt de juiste weergave volgorde voor zoek resultaten beschreven. Het bevat een of meer van de volgende groepen met prioriteit:

- `pole`: De zoek resultaten voor het verkrijgen van de meest zicht bare behandeling (bijvoorbeeld weer gegeven boven de mainline en Sidebar).
- `mainline`: De zoek resultaten worden weer gegeven in de Mainline.
- `sidebar`: De zoek resultaten worden weer gegeven in de zijbalk. Als er geen Sidebar is, geeft u de resultaten onder het Mainline weer.

De JSON van het classificatie antwoord kan een of meer van de groepen bevatten.

Voeg in **Program.cs**de volgende methode toe om de resultaten in de juiste rang orde weer te geven:

```csharp
static void DisplayAllRankedResults(Newtonsoft.Json.Linq.JObject responseObjects)
{
    string[] rankingGroups = new string[] { "pole", "mainline", "sidebar" };

    // Loop through the ranking groups in priority order
    foreach (string rankingName in rankingGroups)
    {
        Newtonsoft.Json.Linq.JToken rankingResponseItems = responseObjects.SelectToken($"rankingResponse.{rankingName}.items");
        if (rankingResponseItems != null)
        {
            foreach (Newtonsoft.Json.Linq.JObject rankingResponseItem in rankingResponseItems)
            {
                Newtonsoft.Json.Linq.JToken resultIndex;
                rankingResponseItem.TryGetValue("resultIndex", out resultIndex);
                var answerType = rankingResponseItem.Value<string>("answerType");
                switch (answerType)
                {
                    case "WebPages":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("webPages.value"), "WebPage", "name", "url", "displayUrl", "snippet");
                        break;
                    case "News":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("news.value"), "News", "name", "url", "description");
                        break;
                    case "Images":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("images.value"), "Image", "thumbnailUrl");
                        break;
                    case "Videos":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("videos.value"), "Video", "embedHtml");
                        break;
                    case "RelatedSearches":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("relatedSearches.value"), "RelatedSearch", "displayText", "webSearchUrl");
                        break;
                }
            }
        }
    }
}
```

Deze methode:

- Lussen over de `rankingResponse` groepen die het antwoord bevat
- De items in elke groep weer geven door aan te roepen`DisplaySpecificResults(...)`

Voeg in **Program.cs**de volgende twee methoden toe:

```csharp
static void DisplaySpecificResults(Newtonsoft.Json.Linq.JToken resultIndex, Newtonsoft.Json.Linq.JToken items, string title, params string[] fields)
{
    if (resultIndex == null)
    {
        foreach (Newtonsoft.Json.Linq.JToken item in items)
        {
            DisplayItem(item, title, fields);
        }
    }
    else
    {
        DisplayItem(items.ElementAt((int)resultIndex), title, fields);
    }
}

static void DisplayItem(Newtonsoft.Json.Linq.JToken item, string title, string[] fields)
{
    Console.WriteLine($"{title}: ");
    foreach( string field in fields )
    {
        Console.WriteLine($"- {field}: {item[field]}");
    }
    Console.WriteLine();
}
```

Deze methoden werken samen om de zoek resultaten naar de-console uit te voeren.

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit. De uitvoer moet er als volgt uitzien:

```
Enter Bing query: sailing lessons seattle

WebPage:
- name: Contoso Sailing Club - Seattle
- url: https://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://contososailingsea....
- snippet: Come sail with Contoso in Seattle...

WebPage:
- name: Contoso Sailing Lessons Seattle - Official Site
- url: http://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://www.constososailinglessonsseat...
- snippet: Contoso sailing lessons in Seattle...

...
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het gebruik van classificatie om resultaten weer te geven](rank-results.md).
