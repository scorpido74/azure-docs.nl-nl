---
title: De Video Indexer-API gebruiken
titleSuffix: Azure Media Services
description: In dit artikel wordt beschreven hoe u aan de slag met de Azure Media Services Video Indexer API.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/03/2020
ms.author: juliako
ms.openlocfilehash: 8b6d160f71bfe8b2e5c447296d511b54ce6542c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245848"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Zelfstudie: de Video Indexer-API gebruiken

Video Indexer consolideert verschillende audio- en video-kunstmatige intelligentie (AI)-technologieën die door Microsoft worden aangeboden tot één geïntegreerde service, waardoor de ontwikkeling eenvoudiger wordt. De API's zijn ontworpen om ontwikkelaars in staat te stellen zich te concentreren op het consumeren van Media AI-technologieën zonder zich zorgen te maken over schaal, wereldwijd bereik, beschikbaarheid en betrouwbaarheid van cloudplatforms. U de API gebruiken om uw bestanden te uploaden, gedetailleerde video-inzichten te krijgen, URL's met insluitbare inzichten en widgets voor spelers te krijgen, en meer.

Wanneer u een Video Indexer-account maakt, u een gratis proefaccount kiezen (waarbij u een bepaald aantal gratis indexeringsminuten krijgt) of een betaalde optie (waarbij u niet beperkt bent door het quotum). Met een gratis proefperiode biedt Video Indexer tot 600 minuten gratis indexering aan websitegebruikers en tot 2400 minuten gratis indexering aan API-gebruikers. Met een betaalde optie maakt u een Video Indexer-account dat is [verbonden met uw Azure-abonnement en een Azure Media Services-account.](connect-to-azure.md) U betaalt zowel voor de geïndexeerde minuten als voor kosten verbonden aan het Azure Media Services-account.

In dit artikel wordt uitgelegd hoe ontwikkelaars kunnen profiteren van de [Video Indexer-API](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>Abonneren op de API

1. Meld u aan bij de [Video Indexer-ontwikkelaarsportal](https://api-portal.videoindexer.ai/).
    
    ![Aanmelden bij Video Indexer Developer Portal](./media/video-indexer-use-apis/video-indexer-api01.png)

   > [!Important]
   > * U moet dezelfde provider gebruiken als voor het aanmelden bij Video Indexer.
   > * Persoonlijke Google- en Microsoft-accounts (Outlook/Live) kunnen alleen worden gebruikt voor proefaccounts. Accounts die gekoppeld zijn met Azure vereisen Azure Active Directory.
   > * Er kan slechts één actief account per e-mail zijn. Als een gebruiker zich user@gmail.com probeert aan te user@gmail.com melden bij LinkedIn en later bij Google, wordt er een foutpagina weergegeven waarin staat dat de gebruiker al bestaat.

2. Abonneer u.

    Selecteer het tabblad [Producten.](https://api-portal.videoindexer.ai/products) Selecteer vervolgens Autorisatie en abonneer u.
    
    ![Tabblad Producten in Video Indexer Developer Portal](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > Nieuwe gebruikers worden automatisch geabonneerd op Autorisatie.
    
    Zodra u zich abonneert, u uw abonnement en uw primaire en secundaire sleutels zien. De sleutels moeten beveiligd zijn. De sleutels mogen alleen door uw servercode gebruikt worden. Ze moeten niet beschikbaar zijn aan de clientkant (.js, .html, enzovoort).

    ![Abonnement en sleutels in Video Indexer Developer Portal](./media/video-indexer-use-apis/video-indexer-api03.png)

> [!TIP]
> Een gebruiker van Video Indexer kan met één abonnementssleutel verbinding maken met meerdere Video Indexer-accounts. U kunt deze Video Indexer-accounts vervolgens koppelen aan verschillende Media Services-accounts.

## <a name="obtain-access-token-using-the-authorization-api"></a>Tokens verkrijgen met de Authorization-API

Zodra u zich abonneert op de Autorisatie-API, u toegangstokens verkrijgen. Deze toegangstokens worden gebruikt voor verificatie op basis van de Operations-API.

Elke aanroep naar de Operations-API moet worden gekoppeld aan een toegangstoken dat overeenkomt met het autorisatiebereik van de aanroep.

- Gebruikersniveau: Met toegangstokens op gebruikersniveau u bewerkingen uitvoeren op **gebruikersniveau.** Hiermee kunt u bijvoorbeeld gekoppelde accounts ophalen.
- Accountniveau: Met toegangstokens op accountniveau u bewerkingen uitvoeren op **accountniveau** of op **videoniveau.** Upload bijvoorbeeld video' s, zet alle video's op een rijtje, krijg video-inzichten, enzovoort.
- Videoniveau: Met toegangstokens op videoniveau u bewerkingen uitvoeren op een specifieke **video.** Krijg bijvoorbeeld video-inzichten, download bijschriften, krijg widgets, enzovoort.

U bepalen of deze tokens alleen-lezen zijn of dat ze bewerken toestaan door **allowEdit=true/false**op te geven.

Voor de meeste server-naar-serverscenario's gebruikt u waarschijnlijk hetzelfde **accounttoken,** omdat het zowel **accountbewerkingen** als **videobewerkingen** dekt. Als u echter van plan bent om clientzijdegesprekken te voeren naar Video Indexer (bijvoorbeeld vanuit JavaScript), wilt u een **videotoegangstoken** gebruiken om te voorkomen dat clients toegang krijgen tot het hele account. Dat is ook de reden dat bij het insluiten van Video Indexer-clientcode in uw client (bijvoorbeeld met **behulp van Get Insights Widget** of Get Player **Widget),** u een **videotoegangstoken** moet opgeven.

Om het eenvoudiger te maken, kunt u **Authorization-API** > **GetAccounts** gebruiken om uw accounts op te halen zonder eerst een gebruikerstoken te verkrijgen. U kunt verzoeken om de accounts op te halen met behulp van geldige tokens, zodat u een extra aanroep om een accounttoken op te halen kunt overslaan.

Toegangstokens verlopen na één uur. Zorg ervoor dat uw token geldig is voordat u de Operations-API gebruikt. Als deze verloopt, roept u de Autorisatie-API opnieuw aan om een nieuw toegangstoken te krijgen.

U bent klaar om te beginnen met de integratie met de API. Zoek [de gedetailleerde beschrijving van elke Video Indexer-REST API](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>Account-id

De parameter Account-id is vereist in alle operationele API-aanroepen. Account-id is een GUID die kan worden verkregen op een van de volgende manieren:

* Gebruik de **Video Indexer-website** om de account-id op te halen:

    1. Ga naar de [Video Indexer](https://www.videoindexer.ai/)-website en meld u aan.
    2. Ga naar de pagina **Instellingen**.
    3. Kopieer de account-id.

        ![Instellingen voor video-indexeren en account-id](./media/video-indexer-use-apis/account-id.png)

* Gebruik de **Video Indexer-ontwikkelaarsportal** om de account-id programmatisch op te halen.

    Gebruik de [API account opdoen.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account?)

    > [!TIP]
    > U kunt de toegangstokens voor de accounts genereren door `generateAccessTokens=true` te definiëren.

* Haal de account-id op uit de URL van een pagina van de speler in uw account.

    Wanneer u een video bekijkt, wordt de id weergegeven na de sectie `accounts` en voor de sectie `videos`.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Aanbevelingen

In deze sectie vindt u enkele aanbevelingen bij het gebruik van de Video Indexer-API.

- Als u van plan bent een video te uploaden, wordt aanbevolen om het bestand op een openbare netwerklocatie te plaatsen (bijvoorbeeld OneDrive). Haal de link naar de video op en geef de URL op als de parameter van het uploadbestand.

    De URL die is opgegeven met Video Indexer moet verwijzen naar een mediabestand (audio of video). Sommige links die worden gegenereerd door OneDrive zijn voor een HTML-pagina die het bestand bevat. Een eenvoudige verificatie voor de URL is om deze in een browser te plakken, als het bestand begint te downloaden, is het waarschijnlijk een goede URL. Als de browser een visualisatie rendert, is dit waarschijnlijk geen koppeling naar een bestand, maar naar een HTML-pagina.

- Als u de API die video-inzichten ophaalt voor de opgegeven video aanroept, krijgt u gedetailleerde JSON-uitvoer als resultaat. [Informatie over de geretourneerde JSON in dit onderwerp](video-indexer-output-json-v2.md).

## <a name="code-sample"></a>Codevoorbeeld

Het volgende C#-codefragment toont het gebruik van alle Video Indexer-API's samen.

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2";
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer =newbyte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(newByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

  Debug.WriteLine("");
  Debug.WriteLine("State:");
  Debug.WriteLine(processingState);

  // job is finished
  if (processingState != "Uploaded" && processingState != "Processing")
  {
      Debug.WriteLine("");
      Debug.WriteLine("Full JSON:");
      Debug.WriteLine(videoGetIndexResult);
      break;
  }
}

// search for the video
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="see-also"></a>Zie ook

- [Overzicht van Video Indexer](video-indexer-overview.md)
- [Regio's](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Volgende stappen

- [Details van de uitvoer JSON onderzoeken](video-indexer-output-json-v2.md)
- Bekijk de [voorbeeldcode](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/API) die een belangrijk aspect van het uploaden en indexeren van een video aantoont. Het volgen van de code geeft u een goed idee van hoe u onze API gebruiken voor basisfunctionaliteiten. Zorg ervoor dat u de inline opmerkingen te lezen en let op onze best practices adviezen.

