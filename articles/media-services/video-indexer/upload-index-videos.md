---
title: Video's uploaden en indexeren met Video Indexer
titleSuffix: Azure Media Services
description: In dit onderwerp ziet u hoe u API's gebruikt om uw video's te uploaden en indexeren met Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/10/2019
ms.author: juliako
ms.openlocfilehash: 255c98965026266348a66bb98a1741eaf04a1d38
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839142"
---
# <a name="upload-and-index-your-videos"></a>Uw video's uploaden en indexeren  

Wanneer u Video's uploadt met Video Indexer-API, hebt u de volgende opties voor uploaden: 

* uw video uploaden via een URL (aanbevolen),
* het videobestand verzenden als een bytematrix in de aanvraagbody.
* Bestaande Azure Media Services activa gebruiken door de [activa-id](https://docs.microsoft.com/azure/media-services/latest/assets-concept) op te geven (alleen ondersteund in betaalde accounts).

In het artikel ziet u hoe u de API [Video uploaden](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) gebruikt voor het uploaden en indexeren van uw video's op basis van een URL. De voorbeeldcode in het artikel bevat de opmerkingen in de code die laten zien hoe u de bytematrix uploadt. <br/>In het artikel komen enkele van de parameters aan bod die u op de API kunt instellen om het proces en de uitvoer van de API te wijzigen.

Als uw video eenmaal is geüpload, wordt de video eventueel door Video Indexer gecodeerd (besproken in het artikel). Wanneer u een Video Indexer-account maakt, kunt u kiezen uit een gratis proefversie (waarmee u een bepaald aantal gratis minuten indexering krijgt) of een betaalde optie (zonder quotumlimiet). Bij de gratis proefversie biedt Video Indexer websitegebruikers maximaal 600 minuten aan gratis indexering en API-gebruikers maximaal 2400 minuten gratis indexering. Met betaalde optie maakt u een Video Indexer-account dat is [verbonden met uw Azure-abonnement en een Azure Media Services-account](connect-to-azure.md). U betaalt zowel voor de geïndexeerde minuten als voor kosten verbonden aan het Media-account. 

## <a name="uploading-considerations-and-limitations"></a>Overwegingen en beperkingen uploaden
 
- Een naam van de video mag niet langer zijn dan 80 tekens.
- Wanneer u uw video uploadt op basis van de URL (voor keur), moet het eind punt worden beveiligd met TLS 1,2 (of hoger).
- De upload grootte met de URL-optie is beperkt tot 30 GB.
- De lengte van de aanvraag-URL is beperkt tot 6144 tekens waarbij de lengte van de URL van de query teken reeks is beperkt tot 4096 tekens.
- De upload grootte met de byte matrix optie is beperkt tot 2 GB.
- De byte matrix optie verkeert na 30 minuten.
- De URL die is opgenomen in de `videoURL` para meter moet worden gecodeerd.
- Het indexeren van Media Services assets heeft dezelfde beperking als het indexeren van een URL.
- Video Indexer heeft een maximale duur limiet van vier uur voor één bestand.

> [!Tip]
> Het verdient aanbeveling om .NET Framework versie 4.6.2 of hoger te gebruiken omdat oudere versies van .NET Framework niet standaard gebruikmaken van TLS 1.2.
>
> Als u een oudere versie van .NET Framework moet gebruiken, voegt u deze regel toe aan uw code voordat u de REST API-aanroept:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="configurations-and-params"></a>Configuraties en parameters

Deze sectie beschrijft een aantal van de optionele parameters en wanneer u deze het best instelt.

### <a name="externalid"></a>externalID 

Met deze parameter kunt u een ID die is gekoppeld aan de video opgeven. De ID kan worden toegepast op externe VCM-systeemintegratie (Video Content Management). De video's die zich in de Video Indexer-portal bevinden, kunnen worden doorzocht met behulp van de opgegeven externe ID.

### <a name="callbackurl"></a>callbackUrl

Een URL die wordt gebruikt om de klant (met een POST-aanvraag) op de hoogte te stellen van de volgende gebeurtenissen:

- Statuswijziging indexering: 
    - Eigenschappen:    
    
        |Naam|Beschrijving|
        |---|---|
        |id|De video-ID|
        |state|De videostatus|  
    - Voor beeld: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed
- Personen geïdentificeerd in de video:
  - Eigenschappen
    
      |Naam|Beschrijving|
      |---|---|
      |id| De video-ID|
      |faceId|De gezichts-id die wordt weergegeven in de video-index|
      |knownPersonId|De persoons-id die uniek is in een gezichtsmodel|
      |personName|De naam van de persoon|
        
    - Voor beeld: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&knownPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 

#### <a name="notes"></a>Opmerkingen

- Video Indexer retourneert alle bestaande parameters die zijn opgegeven in de oorspronkelijke URL.
- De opgegeven URL moet worden gecodeerd.

### <a name="indexingpreset"></a>indexingPreset

Gebruik deze parameter als onbewerkte of externe opnamen achtergrondgeluiden bevatten. Deze parameter wordt gebruikt voor het configureren van het indexeringsproces. U kunt de volgende waarden opgeven:

- `Default`: indexeren en inzichten extraheren met behulp van zowel audio als video
- `AudioOnly`: indexeren en inzichten extraheren met behulp van alleen audio (video wordt genegeerd)
- `DefaultWithNoiseReduction`: indexeren en inzichten extraheren met behulp van zowel audio als video, waarbij algoritmen voor ruisvermindering worden toegepast op de audiostroom

De prijs is afhankelijk van de geselecteerde optie voor indexering.  

### <a name="priority"></a>priority

Video's worden geïndexeerd door Video Indexer op basis van de prioriteit. Gebruik de parameter **priority** voor het specificeren van de indexeringsprioriteit. De volgende waarden zijn geldig: **Laag**, **Normaal** (standaard) en **Hoog**.

De parameter **priority** wordt alleen ondersteund voor betaalde accounts.

### <a name="streamingpreset"></a>streamingPreset

Zodra uw video is geüpload, codeert Video Indexer eventueel de video. Vervolgens gaat Video Indexer door naar het indexeren en analyseren van de video. Wanneer Video Indexer klaar is met analyseren, ontvangt u een melding met de video-ID.  

Wanneer u de API [Video uploaden](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) of [Video opnieuw indexeren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) gebruikt, is een van de optionele parameters `streamingPreset`. Als u `streamingPreset` instelt op `Default`, `SingleBitrate` of `AdaptiveBitrate`, wordt het coderingsproces geactiveerd. Wanneer de indexerings- en coderingstaken klaar zijn, wordt de video gepubliceerd zodat u uw video ook kunt streamen. Het streaming-eindpunt vanwaar u de video wilt streamen, moet de status **Wordt uitgevoerd** hebben.

Om de indexerings- en coderingstaken uit te voeren, heeft het [Azure Media Services-account dat is gekoppeld aan uw Video Indexer-account](connect-to-azure.md) gereserveerde eenheden nodig. Zie [Mediaverwerking schalen](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) voor meer informatie. Aangezien deze rekentaken intensief zijn, wordt eenheidstype S3 aanbevolen. Het aantal gereserveerde eenheden bepaalt het maximale aantal taken dat parallel kan worden uitgevoerd. De aanbevolen basislijn is 10 gereserveerde S3-eenheden. 

Als u uw video alleen wilt indexeren, maar deze niet wilt coderen, stelt u `streamingPreset` in op `NoStreaming`.

### <a name="videourl"></a>videoUrl

Een URL van het video-/audiobestand dat moet worden geïndexeerd. De URL moet verwijzen naar een mediabestand (HTML-pagina's worden niet ondersteund). Het bestand kan worden beveiligd door een toegangstoken dat is geleverd als onderdeel van de URI en het eindpunt voor het bestand moet worden beveiligd met TLS 1.2 of hoger. De URL moet worden gecodeerd. 

Als de `videoUrl` niet is opgegeven, verwacht de Video Indexer dat u het bestand als inhoud met meerdere delen of formulier opgeeft.

## <a name="code-sample"></a>Codevoorbeeld

Het volgende C#-codefragment toont het gebruik van alle Video Indexer-API's samen.

```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var location = "westus2";
    var apiKey = "...";

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    // take the relevant account, here we simply take the first
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Debug.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =new byte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(new ByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video ID from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Debug.WriteLine("Uploaded");
    Debug.WriteLine("Video ID:");
    Debug.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

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
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Debug.WriteLine("");
    Debug.WriteLine("Search:");
    Debug.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Debug.WriteLine("Insights Widget url:");
    Debug.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
    Debug.WriteLine("");
    Debug.WriteLine("Player Widget url:");
    Debug.WriteLine(playerWidgetLink);
}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```
## <a name="common-errors"></a>Algemene fouten

De statuscodes in de volgende tabel kunnen worden geretourneerd door de uploadbewerking.

|Statuscode|ErrorType (in hoofdtekst van antwoord)|Beschrijving|
|---|---|---|
|400|VIDEO_ALREADY_IN_PROGRESS|Dezelfde video wordt al verwerkt in het opgegeven account.|
|400|VIDEO_ALREADY_FAILED|Dezelfde video kon minder dan twee uur geleden niet worden verwerkt in het opgegeven account. API-clients moeten ten minste twee uur wachten voordat ze een video opnieuw uploaden.|

## <a name="next-steps"></a>Volgende stappen

[De Azure Video Indexer-uitvoer controleren die door de API is geproduceerd](video-indexer-output-json-v2.md)
