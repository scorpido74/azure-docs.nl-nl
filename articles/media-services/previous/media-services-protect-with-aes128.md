---
title: Gebruik AES-128 dynamische versleuteling en de key delivery service | Microsoft Documenten
description: In dit onderwerp ziet u hoe u dynamisch versleutelen met AES-128 en de belangrijke leveringsservice gebruiken.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 01153317b49e4543f10faa517bce7bcc01ce22d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269729"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Dynamische AES-128-versleuteling en de sleutelleveringsservice gebruiken
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [Php](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>  

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

U Media Services gebruiken om HTTP Live Streaming (HLS) en Smooth Streaming versleuteld met de AES te leveren met behulp van 128-bits encryptiesleutels. Media Services biedt ook de belangrijke leveringsservice die versleutelingssleutels levert aan geautoriseerde gebruikers. Als u wilt dat Media Services een asset versleutelt, koppelt u een versleutelingssleutel aan het item en configureert u ook autorisatiebeleid voor de sleutel. Wanneer een stream door een speler wordt aangevraagd, gebruikt Media Services de opgegeven sleutel om uw inhoud dynamisch te versleutelen met behulp van AES-versleuteling. Voor het ontsleutelen van de stream, wordt door de speler de sleutel van de sleutelleveringsservice aangevraagd. Als u wilt bepalen of de gebruiker gemachtigd is om de sleutel te krijgen, evalueert de service het autorisatiebeleid dat u voor de sleutel hebt opgegeven.

Media Services ondersteunt meerdere manieren om gebruikers te verifiëren die sleutels aanvragen. Het autorisatiebeleid voor inhoudssleutels kan een of meer autorisatiebeperkingen hebben: beperking voor openen of tokenbeperkingen. Het beleid met de tokenbeperking moet vergezeld gaan van een token dat is uitgegeven door een beveiligingstokenservice (STS). Media Services ondersteunt tokens in de indelingen [Simple Web Tokens](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) en [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Zie [Het autorisatiebeleid voor inhoudssleutels configureren](media-services-protect-with-aes128.md#configure_key_auth_policy) voor meer informatie.

Als u dynamische versleuteling wilt gebruiken, moet u een asset hebben die een set multi-bitrate MP4-bestanden of multi-bitrate Smooth Streaming-bronbestanden bevat. U moet ook het leveringsbeleid voor het item configureren (later in dit artikel beschreven). Vervolgens zorgt de server voor streaming on demand er op basis van de indeling die is opgegeven in de streaming-URL voor dat de stream wordt geleverd in het protocol dat u hebt geselecteerd. Als gevolg hiervan hoeft u alleen de bestanden op te slaan en te betalen in één opslagformaat. Media Services bouwt en verwerkt het juiste antwoord op basis van aanvragen van een client.

Dit artikel is handig voor ontwikkelaars die werken aan toepassingen die beveiligde media leveren. In het artikel ziet u hoe u de service voor het leveren van sleutels configureren met autorisatiebeleid, zodat alleen geautoriseerde clients versleutelingssleutels kunnen ontvangen. Het laat ook zien hoe dynamische encryptie te gebruiken.

Zie [dit blogbericht](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)voor informatie over het versleutelen van inhoud met de Advanced Encryption Standard (AES) voor levering aan Safari op macOS.
Zie [deze video](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption)voor een overzicht van hoe u uw media-inhoud beschermen met AES-versleuteling.


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>AES-128 dynamische versleuteling en key delivery service workflow

Voer de volgende algemene stappen uit wanneer u uw assets versleutelt met AES met behulp van de service voor het leveren van de sleutel van Media Services en ook met behulp van dynamische versleuteling:

1. [Maak een asset en upload bestanden naar het item.](media-services-protect-with-aes128.md#create_asset)

2. [Codeer het element dat het bestand bevat op de adaptieve bitrate MP4-set](media-services-protect-with-aes128.md#encode_asset).

3. [Maak een inhoudssleutel en koppel deze aan het gecodeerde element](media-services-protect-with-aes128.md#create_contentkey). De inhoudssleutel bevat in Media Services de versleutelingssleutel van de asset.

4. [Het autorisatiebeleid van de inhoudssleutel configureren](media-services-protect-with-aes128.md#configure_key_auth_policy). U moet het autorisatiebeleid voor de inhoudssleutel configureren. De client moet voldoen aan het beleid voordat de inhoudssleutel aan de client wordt geleverd.

5. [Configureer het leveringsbeleid voor een actief](media-services-protect-with-aes128.md#configure_asset_delivery_policy). De configuratie van het leveringsbeleid omvat de URL voor het verkrijgen van sleutels en een initialisatievector (IV). (AES-128 vereist dezelfde IV voor encryptie en decryptie.) De configuratie omvat ook het leveringsprotocol (bijvoorbeeld MPEG-DASH, HLS, Smooth Streaming of alles) en het type dynamische versleuteling (bijvoorbeeld envelop of geen dynamische versleuteling).

    U kunt voor dezelfde asset een ander beleid voor elk protocol toepassen. U kunt bijvoorbeeld PlayReady-versleuteling toepassen op Smooth/DASH en een AES Envelope op HLS. Protocollen die niet zijn gedefinieerd in een leveringsbeleid, worden geblokkeerd voor streaming. (Een voorbeeld hiervan is als u één beleid toevoegt dat alleen HLS als protocol opgeeft.) De uitzondering is als u helemaal geen beleid voor de levering van activa hebt gedefinieerd. In dat geval is streaming voor alle protocollen toegestaan.

6. [Maak een OnDemand locator](media-services-protect-with-aes128.md#create_locator) om een streaming URL te krijgen.

Het artikel laat ook zien [hoe een clienttoepassing een sleutel kan aanvragen bij de key delivery service.](media-services-protect-with-aes128.md#client_request)

U vindt een compleet [.NET-voorbeeld](media-services-protect-with-aes128.md#example) aan het einde van het artikel.

De volgende afbeelding geeft een illustratie van de eerder beschreven werkstroom. Hier wordt de token voor verificatie gebruikt.

![Bescherm met AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

De rest van dit artikel bevat uitleg, codevoorbeelden en koppelingen naar onderwerpen die u laten zien hoe u de eerder beschreven taken uitvoeren.

## <a name="current-limitations"></a>Huidige beperkingen
Als u het leveringsbeleid voor uw asset toevoegt of bijwerkt, moet u een eventuele locator verwijderen en een nieuwe locator maken.

## <a name="create-an-asset-and-upload-files-into-the-asset"></a><a id="create_asset"></a>Een asset maken en bestanden uploaden naar het item
Als u uw video's wilt beheren, coderen en streamen, moet u eerst uw inhoud uploaden naar Media Services. Wanneer uw inhoud is geüpload, wordt deze veilig opgeslagen in de cloud voor verdere verwerking en streaming. 

Zie [Upload Files into a Media Services account](media-services-dotnet-upload-files.md) (Bestanden uploaden naar een Media Services-account) voor gedetailleerde informatie.

## <a name="encode-the-asset-that-contains-the-file-to-the-adaptive-bitrate-mp4-set"></a><a id="encode_asset"></a>De asset die het bestand bevat coderen naar de Adaptive Bitrate MP4-set
Bij dynamische versleuteling maakt u een asset die een set multi-bitrate MP4-bestanden of multi-bitrate Smooth Streaming-bronbestanden bevat. Vervolgens zorgt de on-demand streamingserver, op basis van de opgegeven indeling in het manifest- of fragmentverzoek, ervoor dat u de stream ontvangt in het door u geselecteerde protocol. Vervolgens hoeft u de bestanden alleen op te slaan en te betalen in één opslagformaat. Media Services bouwt en verwerkt het juiste antwoord op basis van aanvragen van een client. Zie [Dynamic packaging overview](media-services-dynamic-packaging-overview.md) (Overzicht van dynamische pakketten).

>[!NOTE]
>Wanneer uw Media Services-account is gemaakt, wordt er een standaard streaming-eindpunt met de status Gestopt aan uw account toegevoegd. Als u inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eindpunt van waar u inhoud wilt streamen, de status Wordt uitgevoerd hebben. 
>
>Om dynamische verpakkingen en dynamische versleuteling te gebruiken, moet uw asset ook een set adaptieve bitrate MP4's of adaptieve bitrate Smooth Streaming-bestanden bevatten.

Zie [Encode an asset by using Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) (Een asset coderen met Media Encoder Standard) voor instructies voor het coderen.

## <a name="create-a-content-key-and-associate-it-with-the-encoded-asset"></a><a id="create_contentkey"></a>Een inhoudssleutel maken en koppelen aan het gecodeerde item
In Media Services bevat de inhoudssleutel de sleutel waarmee u een asset wilt coderen.

Zie [Create content key](media-services-dotnet-create-contentkey.md) (Inhoudssleutels maken) voor meer informatie.

## <a name="configure-the-content-keys-authorization-policy"></a><a id="configure_key_auth_policy"></a>Het autorisatiebeleid van de inhoudssleutel configureren
Media Services ondersteunt meerdere manieren om gebruikers te verifiëren die sleutels aanvragen. U moet het autorisatiebeleid voor de inhoudssleutel configureren. De klant (speler) moet aan het beleid voldoen voordat de sleutel aan de klant kan worden geleverd. Het autorisatiebeleid voor inhoudssleutel kan een of meer autorisatiebeperkingen hebben, open, tokenbeperking of IP-beperking.

Zie [Een autorisatiebeleid voor inhoudssleutels configureren](media-services-dotnet-configure-content-key-auth-policy.md) voor meer informatie.

## <a name="configure-an-asset-delivery-policy"></a><a id="configure_asset_delivery_policy"></a>Een leveringsbeleid voor assets configureren
Configureer het leveringsbeleid voor uw asset. De configuratie van het leveringsbeleid voor assets omvat onder andere het volgende:

* De belangrijkste acquisitie-URL. 
* De initialisatievector (IV) die u moet gebruiken voor de envelopversleuteling. AES-128 vereist dezelfde IV voor encryptie en decryptie. 
* Het protocol voor het leveren van assets (bijvoorbeeld MPEG-DASH, HLS, Smooth Streaming of alle).
* Het type dynamische versleuteling (bijvoorbeeld AES-envelop) of geen dynamische versleuteling. 

Zie [Een leveringsbeleid voor assets configureren](media-services-dotnet-configure-asset-delivery-policy.md) voor meer informatie.

## <a name="create-an-ondemand-streaming-locator-to-get-a-streaming-url"></a><a id="create_locator"></a>Een OnDemand-streaminglocator maken om een streaming-URL te verkrijgen
U moet de gebruiker voorzien van de streaming-URL voor Smooth Streaming, DASH of HLS.

> [!NOTE]
> Als u het leveringsbeleid voor uw asset toevoegt of bijwerkt, moet u een eventuele locator verwijderen en een nieuwe locator maken.
> 
> 

Zie [Build a streaming URL](media-services-deliver-streaming-content.md) (Een streaming-URL bouwen) voor instructies over het publiceren van een asset en bouwen van een streaming-URL.

## <a name="get-a-test-token"></a>Een test-token ophalen
Haal op basis van de tokenbeperking een test-token op die is gebruikt voor het sleutelautorisatiebeleid.

```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word "Bearer" in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
```

U kunt de [Azure Media Services Player](https://aka.ms/azuremediaplayer) gebruiken om uw stream te testen.

## <a name="how-can-your-client-request-a-key-from-the-key-delivery-service"></a><a id="client_request"></a>Hoe kan uw klant een sleutel aanvragen bij de key delivery service?
In de vorige stap hebt u de URL gemaakt die naar een manifestbestand verwijst. Uw klant moet de benodigde informatie uit de streaming manifestbestanden halen om een verzoek te doen aan de key delivery service.

### <a name="manifest-files"></a>Manifestbestanden
De client moet de URL (die ook inhoudssleutel-ID [kid]) waarde bevat uit het manifestbestand extraheren. De client probeert vervolgens de encryptiesleutel van de key delivery service te krijgen. De client moet ook de IV-waarde extraheren en gebruiken om de stream te decoderen. In het volgende `<Protection>` fragment wordt het element van het manifest Vloeiend streamen weergegeven:

```xml
    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>
```

In het geval van HLS wordt het hoofdmanifest opgesplitst in segmentbestanden. 

Het wortelmanifest is bijvoorbeeld:\/http: /test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl). Het bevat een lijst met segmentbestandsnamen.

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Als u een van de segmentbestanden opent in een\/teksteditor (bijvoorbeeld http: /test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-apla), het bevat #EXT-X-KEY, wat aangeeft dat het bestand versleuteld is.

    #EXTM3U
    #EXT-X-VERSION:4
    #EXT-X-ALLOW-CACHE:NO
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-TARGETDURATION:9
    #EXT-X-KEY:METHOD=AES-128,
    URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
         kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
            IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
    #EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
    #EXTINF:8.425708,no-desc
    Fragments(video=0,format=m3u8-aapl)
    #EXT-X-ENDLIST

>[!NOTE] 
>Als u van plan bent om een AES-versleutelde HLS in Safari te spelen, zie [deze blog](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>Vraag de sleutel aan bij de belangrijke bezorgservice

In de volgende code ziet u hoe u een aanvraag verzendt naar de klantenservice voor de belangrijkste bezorging van Media Services met behulp van een sleutellevering Uri (die uit het manifest is gehaald) en een token. (Dit artikel legt niet uit hoe je SWTs te krijgen van een STS.)

```csharp
    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);

        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;

        var response = request.GetResponse();

        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }

        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();

        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }
```

## <a name="protect-your-content-with-aes-128-by-using-net"></a>Bescherm uw inhoud met AES-128 met behulp van .NET

### <a name="create-and-configure-a-visual-studio-project"></a>Maak en configureer een Visual Studio-project.

1. Stel uw ontwikkelomgeving in en vul in het bestand app.config de verbindingsinformatie in, zoals beschreven in [Media Services ontwikkelen met .NET](media-services-dotnet-how-to-use.md).

2. Voeg de volgende elementen toe aan appInstellingen, zoals gedefinieerd in uw app.config-bestand:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

### <a name="example"></a><a id="example"></a>Voorbeeld

Overschrijf de code in uw Program.cs-bestand met de code die wordt weergegeven in deze sectie.
 
>[!NOTE]
>Er is een limiet van 1.000.000 beleidsregels voor verschillende Media Services-beleidsregels (bijvoorbeeld voor locatorbeleid of ContentKeyAuthorizationPolicy). Gebruik dezelfde beleids-id als u altijd dezelfde dagen/toegangsmachtigingen gebruikt. Een voorbeeld is een beleid voor locators die zijn bedoeld om aanwezig te blijven gedurende een lange periode (niet-uploadbeleidsregels). Zie de sectie Toegangsbeleid beperken in [Activa en gerelateerde entiteiten beheren met de Media Services .NET SDK](media-services-dotnet-manage-entities.md#limit-access-policies)voor meer informatie.

Zorg ervoor dat variabelen zo worden bijgewerkt dat ze verwijzen naar de mappen waar uw invoerbestanden zich bevinden.

[!code-csharp[Main](../../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
