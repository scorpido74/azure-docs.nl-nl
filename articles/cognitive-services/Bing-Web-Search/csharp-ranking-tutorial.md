---
title: Rang gebruiken om zoekresultaten weer te geven
titleSuffix: Azure Cognitive Services
description: Hier wordt uitgelegd hoe u het Antwoord van Bing RankingResponse gebruiken om zoekresultaten in rangvolgorde weer te geven.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: 1c8e0bb136fddeb84dc991e63a761378b38cc470
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382324"
---
# <a name="build-a-console-app-search-client-in-c"></a>Een zoekclient voor console-apps bouwen in C #

In deze zelfstudie ziet u hoe u een eenvoudige .NET Core-console-app maken waarmee gebruikers de Bing Web Search API kunnen opvragen en gerangschikte resultaten kunnen weergeven.

In deze zelfstudie ziet u hoe u:

- Maak een eenvoudige query naar de Bing Web Search API
- Queryresultaten in gerangschikte volgorde weergeven

## <a name="prerequisites"></a>Vereisten

Om mee te gaan met de tutorial, moet je het volgende doen:

- Visual Studio. Als je het niet hebt, [download en installeer je de gratis Visual Studio 2017 Community Edition.](https://www.visualstudio.com/downloads/)
- Een abonnementssleutel voor de Bing Web Search API. Als u geen sleutel hebt, kunt u zich [registreren voor een gratis proefversie](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

## <a name="create-a-new-console-app-project"></a>Een nieuw console-app-project maken

Maak in Visual Studio een project met `Ctrl`+`Shift`+`N`.

Klik in het dialoogvenster **Nieuw project** op **Visual C# > Windows Classic Desktop > Console App (.NET Framework).**

Geef de toepassing **MyConsoleSearchApp**een naam en klik op **OK**.

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>Voeg het JSON.net Nuget-pakket toe aan het project

JSON.net u werken met de JSON-antwoorden die door de API zijn geretourneerd. Voeg het NuGet-pakket toe aan uw project:

- Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **NuGet-pakketten beheren...**.
- Zoek `Newtonsoft.Json`op het tabblad **Bladeren** naar . Selecteer de nieuwste versie en klik op **Installeren**.
- Klik op de knop **OK** in het venster **Wijzigingen controleren.**
- Sluit het tabblad Visual Studio met de titel **NuGet: MyConsoleSearchApp**.

## <a name="add-a-reference-to-systemweb"></a>Een verwijzing naar System.Web toevoegen

Deze tutorial is `System.Web` gebaseerd op de montage. Voeg een verwijzing naar deze assemblage toe aan uw project:

- Klik in **Solution Explorer**met de rechtermuisknop op **Referenties** en selecteer **Naslagwaarde toevoegen...**
- Selecteer **> Framework samenstellen**en scroll naar beneden en controleer **System.Web**
- Selecteer **OK**

## <a name="add-some-necessary-using-statements"></a>Enkele noodzakelijke instructies toevoegen

De code in deze zelfstudie vereist drie extra instructies. Voeg deze instructies `using` toe onder de bestaande verklaringen boven aan **Program.cs:**

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>De gebruiker om een query vragen

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

- Vraagt de gebruiker om een query
- Oproepen `RunQueryAndDisplayResults(userQuery)` om de query uit te voeren en de resultaten weer te geven
- Wacht op de invoer van de gebruiker om te voorkomen dat het consolevenster onmiddellijk sluit.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Zoeken naar queryresultaten met de Bing Web Search API

Voeg vervolgens een methode toe die de API opvraagt en de resultaten weergeeft:

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

- Maakt `HttpClient` een query om de Web Search API op te vragen
- Hiermee `Ocp-Apim-Subscription-Key` stelt u de HTTP-koptekst in die Bing gebruikt om de aanvraag te verifiëren
- Voert de aanvraag uit en gebruikt JSON.net om de resultaten te deserialiseren
- Oproepen `DisplayAllRankedResults(responseObjects)` om alle resultaten in gerangschikte volgorde weer te geven

Zorg ervoor dat u `Ocp-Apim-Subscription-Key` de waarde van uw abonnementssleutel instelt.

## <a name="display-ranked-results"></a>Gerangschikte resultaten weergeven

Voordat u laat zien hoe u de resultaten in gerangschikte volgorde weergeeft, bekijkt u een voorbeeldvan het zoekantwoord op het web:

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

Het `rankingResponse` JSON-object ([documentatie](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse)) beschrijft de juiste weergavevolgorde voor zoekresultaten. Het bevat een of meer van de volgende, geprioritteerde groepen:

- `pole`: De zoekresultaten om de meest zichtbare behandeling te krijgen (bijvoorbeeld boven de hoofdlijn en zijbalk).
- `mainline`: De zoekresultaten worden weergegeven in de hoofdlijn.
- `sidebar`: De zoekresultaten worden weergegeven in de zijbalk. Als er geen zijbalk is, geeft u de resultaten onder de hoofdlijn weer.

De ranking reactie JSON kan een of meer van de groepen.

Voeg **in Program.cs**de volgende methode toe om de resultaten in de juiste volgorde weer te geven:

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

- Lussen over `rankingResponse` de groepen die het antwoord bevat
- Geeft de items in elke groep weer door aan te roepen`DisplaySpecificResults(...)`

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

Deze methoden werken samen om de zoekresultaten naar de console uit te zetten.

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

Lees meer over [het gebruik van ranking om resultaten weer te geven.](rank-results.md)
