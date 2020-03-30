---
title: Concepten voor live gebeurtenissen en live-uitvoer in Azure Media Services v3
titleSuffix: Azure Media Services
description: In dit onderwerp vindt u een overzicht van live-gebeurtenissen en live-uitgangen in Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2020
ms.author: juliako
ms.openlocfilehash: e6f2ad2c5c30e3c75e8d3588e386ea14e8e3350b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065947"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Live-evenementen en live-uitgangen in mediaservices

Met Azure Media Services u live-evenementen leveren aan uw klanten in de Azure-cloud. Als u uw live streaming-evenementen wilt instellen in Media Services v3, moet u inzicht krijgen in de concepten die in dit artikel worden besproken.

> [!TIP]
> Voor klanten die migreren vanuit Media Services v2 API's, vervangt de entiteit **Live Event** **Kanaal** in v2 en vervangt **Live Output** het **programma**.

## <a name="live-events"></a>Livegebeurtenissen

[Livegebeurtenissen](https://docs.microsoft.com/rest/api/media/liveevents) zijn verantwoordelijk voor het opnemen en verwerken van de live videofeeds. Wanneer u een Live Event maakt, wordt een primair en secundair invoereindpunt gemaakt dat u gebruiken om een live signaal te verzenden vanaf een externe encoder. De externe live-encoder stuurt de bijdragefeed naar dat invoereindpunt met behulp van het [RTMP-](https://www.adobe.com/devnet/rtmp.html) of [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) -invoerprotocol (gefragmenteerd-MP4). Voor het RTMP-ingest-protocol kan de inhoud`rtmp://`in de duidelijke ( )`rtmps://`of veilig versleuteld op de draad worden verzonden( ). Voor het smooth streaming-ingest-protocol zijn `http://` de `https://`ondersteunde URL-schema's of .  

## <a name="live-event-types"></a>Typen live gebeurtenissen

Een [Live Event](https://docs.microsoft.com/rest/api/media/liveevents) kan worden ingesteld op een *pass-through* (een on-premises live encoder stuurt een meervoudige bitrate stream) of *live codering* (een on-premises live encoder stuurt een enkele bitrate stream). De typen worden ingesteld tijdens het maken met [LiveEventEncodingType:](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype)

* **LiveEventEncodingType.None**: Een on-premises live encoder stuurt een meervoudige bitrate stream. De ingenomen stream gaat door het Live Event zonder verdere verwerking. Ook wel de pass-through mode.
* **LiveEventEncodingType.Standard**: Een on-premises live encoder stuurt één bitrate stream naar de Live Event en Media Services maakt meerdere bitrate streams. Als de bijdragefeed een resolutie van 720p of hoger heeft, codeert de **standaard720p-voorinstelling** een set van paren met 6 resolutie/bitrates.
* **LiveEventEncodingType.Premium1080p**: Een on-premises live encoder stuurt één bitratestream naar het Live Event en Media Services maakt meerdere bitrate streams. De standaardinstelling 1080p geeft de uitvoerset van sets van sets van pixels/bitratesparen op.

### <a name="pass-through"></a>Pass-through

![Live-gebeurtenis doorgeven met het voorbeelddiagram mediaservices](./media/live-streaming/pass-through.svg)

Bij gebruik van de pass-through **livegebeurtenis** bent u afhankelijk van de on-premises live-encoder voor het genereren van een multiple-bitrate videostream en het verzenden ervan als de bijdragefeed voor de livegebeurtenis (met behulp van RTMP of een gefragmenteerd MP4-protocol). De livegebeurtenis voert de binnenkomende videostream vervolgens zonder verdere verwerking uit. Zo'n pass-through Live Event is geoptimaliseerd voor langlopende live events of 24x365 lineaire live streaming. Bij het maken van dit type livegebeurtenis, geeft u None op (LiveEventEncodingType.None).

U kunt de bijdragefeed verzenden bij een resolutie tot maximaal 4K en een framesnelheid van 60 frames per seconde, met ofwel H.264/AVC- of H.265/HEVC-videocodecs, en AAC (AAC-LC, HE-AACv1 of HE-AACv2)-audiocodec. Zie [Vergelijking van typen live-evenementen](live-event-types-comparison.md)voor meer informatie .

> [!NOTE]
> Het gebruik van een pass-through-methode is de meest economische manier om live streaming te doen wanneer u meerdere gebeurtenissen over een lange periode doet, en u hebt al geïnvesteerd in on-premises encoders. Zie de details over de [prijzen](https://azure.microsoft.com/pricing/details/media-services/).
>

Zie een voorbeeld van .NET-code in [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Live Encoding  

![live codering met mediaservices-voorbeelddiagram](./media/live-streaming/live-encoding.svg)

Wanneer u live-codering met Media Services gebruikt, configureert u uw on-premises live-encoder om één bitrate-video te verzenden als bijdragefeed voor de Live-gebeurtenis (met RTMP- of Fragmented-Mp4-protocol). Vervolgens stelt u een Live Event in, zodat deze die binnenkomende stream van één bitrate codeert naar een [videostream met meerdere bitrates](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)en de uitvoer beschikbaar maakt voor levering om apparaten af te spelen via protocollen zoals MPEG-DASH, HLS en Smooth Streaming.

Wanneer u live codering gebruikt, u de bijdragefeed alleen verzenden op resoluties tot 1080p-resolutie met een framesnelheid van 30 frames per seconde, met H.264/AVC-videocodec en AAC (AAC-LC, HE-AACv1 of HE-AACv2) audiocodec. Houd er rekening mee dat pass-through Live Events resoluties tot 4K met 60 frames per seconde kunnen ondersteunen. Zie [Vergelijking van typen live-evenementen](live-event-types-comparison.md)voor meer informatie .

De resoluties en bitrates in de uitvoer van de levende encoder worden bepaald door de voorinstelling. Als u een **standaard** live-encoder gebruikt (LiveEventEncodingType.Standard), geeft de *standaard720p-voorinstelling* een set van zes resolutie/bitrateparen op, gaande van 720p bij 3,5 Mbps tot 192p bij 200 kbps. Als u anders een **Premium1080p-levenscoder** gebruikt (LiveEventEncodingType.Premium1080p), geeft de standaardvoorinstelling *1080p* een set van zes resolutie/bitsnelheidparen op, gaande van 1080p bij 3,5 Mbps tot 180p bij 200 kbps. Zie [Systeemwaarden](live-event-types-comparison.md#system-presets) voor meer informatie.

> [!NOTE]
> Als u de voorinstelling voor live codering wilt aanpassen, opent u een ondersteuningsticket via azure-portal. Geef de gewenste tabel met resolutie en bitrates op. Controleer of er slechts één laag op 720p is (als je een voorinstelling aanvraagt voor een standaard live encoder) of op 1080p (als je een voorinstelling aanvraagt voor een Premium1080p live encoder) en maximaal 6 lagen.

## <a name="creating-live-events"></a>Live-evenementen maken

### <a name="options"></a>Opties

Wanneer u een live-evenement maakt, u de volgende opties opgeven:

* Het streaming-protocol voor de livegebeurtenis (momenteel worden de protocollen RTMP en Smooth Streaming ondersteund).<br/>U de protocoloptie niet wijzigen terwijl de live-gebeurtenis of de bijbehorende live-uitvoer worden uitgevoerd. Als u verschillende protocollen nodig hebt, maakt u een afzonderlijk Live-evenement voor elk streamingprotocol.  
* Wanneer u de gebeurtenis maakt, u opgeven om deze automatisch te starten. <br/>Wanneer autostart is ingesteld op True, wordt de Live gebeurtenis gestart na het maken ervan. De facturering begint zodra het live-evenement wordt gestart. U moet expliciet Stop aanroepen in de resource van de livegebeurtenis om verdere facturering stop te zetten. Je het evenement ook starten wanneer je klaar bent om te beginnen met streamen.

    Zie [Live Event states and billing](live-event-states-billing.md) (Statussen en facturering voor livegebeurtenissen) voor meer informatie.

* IP-beperkingen voor de opname en voorbeeldweergave. U kunt de IP-adressen definiëren die zijn toegestaan om een video van deze livegebeurtenis op te nemen. Toegestane IP-adressen kunnen worden opgegeven als één IP-adres (bijvoorbeeld 10.0.0.1), een IP-adresbereik met een IP-adres en een CIDR-subnetmasker (bijvoorbeeld 10.0.0.1/22) of een IP-adresbereik met een IP-adres en een decimaal subnetmasker met punten (bijvoorbeeld , ' 10.0.0.1(255.255.252.0)').<br/>Als er geen IP-adressen zijn opgegeven en er geen regeldefinitie is, is er geen IP-adres toegestaan. Als u IP-adres(sen) wilt toestaan, maakt u een regel en stelt u 0.0.0.0/0 in.<br/>De IP-adressen moeten zich in een van de volgende formaten bevinden: IpV4-adres met vier nummers of CIDR-adresbereik.

    Als u bepaalde IP's op uw eigen firewalls wilt inschakelen of invoer wilt beperken tot uw live-gebeurtenissen naar Azure IP-adressen, downloadt u een JSON-bestand uit [IP-adresbereiken](https://www.microsoft.com/download/details.aspx?id=41653)van Azure Datacenter. Selecteer de sectie **Details** op de pagina voor meer informatie over dit bestand.
    
* Wanneer u het evenement maakt, u ervoor kiezen om Live Transcripties in te schakelen. <br/> Live transcriptie is standaard uitgeschakeld. U deze eigenschap niet wijzigen terwijl het live-evenement of de bijbehorende live-uitvoer wordt uitgevoerd. 

### <a name="naming-rules"></a>Naamgevingsregels

* Max live evenement naam is 32 tekens.
* De naam moet dit [regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) patroon volgen: `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`.

Zie ook [Streaming Endpoints naamgevingsconventies](streaming-endpoint-concept.md#naming-convention).

> [!TIP]
> Om de uniciteit van uw live-evenementnaam te garanderen, u een GUID genereren en vervolgens alle koppeltekens en krullende haakjes verwijderen (indien aanwezig). De string zal uniek zijn in alle live-evenementen en de lengte is gegarandeerd 32.

## <a name="live-event-ingest-urls"></a>Url's voor live gebeurtenissen

Zodra het Live Event is gemaakt, u url's krijgen die u aan de live on-premises encoder verstrekt. De live-encoder gebruikt deze URL's voor het invoeren van een live-stream. Zie [Aanbevolen on-premises live encoders voor](recommended-on-premises-live-encoders.md)meer informatie.

U kunt niet-vanity-URL's en vanity-URL's gebruiken.

> [!NOTE] 
> Als u een inname-URL voorspellend wilt maken, stelt u de modus 'ijdelheid' in.

* URL van niet-ijdelheid

    Url van niet-ijdelheid is de standaardmodus in Media Services v3. U krijgt de livegebeurtenis mogelijk snel, maar de opname-URL is alleen bekend als de livegebeurtenis wordt gestart. De URL wordt gewijzigd als u de livegebeurtenis stopt of start. <br/>Non-Vanity is handig in scenario's waarin een eindgebruiker wil streamen met behulp van een app waar de app zo snel mogelijk een live-evenement wil ontvangen en het hebben van een dynamische inname-URL geen probleem is.

    Als een client-app geen url hoeft te genereren voordat de Live-gebeurtenis wordt gemaakt, laat Media Services het Access-token voor de live-gebeurtenis automatisch genereren.

* Url van ijdelheid

    De vanity-modus heeft de voorkeur van grote media-omroepen die gebruik maken van hardware-broadcast-encoders en hun encoders niet opnieuw willen configureren wanneer ze het Live Event starten. Ze willen een voorspellende URL, die in de loop van de tijd niet verandert.

    Als u deze modus `vanityUrl` `true` wilt opgeven, stelt `false`u in op de tijd van het maken (standaard is ). Je moet ook je eigen`LiveEventInput.accessToken`toegangstoken () doorgeven bij het maken van de tijd. U geeft de tokenwaarde op om een willekeurig token in de URL te vermijden. Het toegangstoken moet een geldige GUID-tekenreeks zijn (met of zonder de koppeltekens). Zodra de modus is ingesteld, kan deze niet meer worden bijgewerkt.

    Het toegangstoken moet uniek zijn in uw datacenter. Als uw app een ijdelheids-URL moet gebruiken, wordt het aanbevolen om altijd een nieuwe GUID-instantie voor uw toegangstoken te maken (in plaats van bestaande GUID opnieuw te gebruiken).

    Gebruik de volgende API's om de URL van Ijdelheid in te schakelen `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`en het toegangstoken in te stellen op een geldige GUID (bijvoorbeeld).  

    |Taal|Ijdelheids-URL inschakelen|Toegangstoken instellen|
    |---|---|---|
    |REST|[properties.vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--ijdelheid-url](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--access-token](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput.AccessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>Naamgevingsregels voor live-opname-URL's

* De *willekeurige* tekenreeks hieronder is een 128-bits hexadecimaal getal (bestaande uit 32 tekens, van 0-9 en a-f).
* *uw toegangstoken:* de geldige GUID-tekenreeks die u instelt bij het gebruik van de ijdelheidsmodus. Bijvoorbeeld `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *stroomnaam:* geeft de stroomnaam voor een specifieke verbinding aan. De waarde van de streamnaam wordt meestal toegevoegd door de live encoder die u gebruikt. U de levende encoder configureren om elke naam te gebruiken om de verbinding te beschrijven, bijvoorbeeld: video1_audio1, video2_audio1, stream.

#### <a name="non-vanity-url"></a>URL van niet-ijdelheid

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>Url van ijdelheid

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>URL van voorbeeld voorbeeld live gebeurtenis

Zodra het live-evenement de bijdragefeed begint te ontvangen, u het voorbeeldeindpunt gebruiken om te bekijken en te valideren dat u de live stream ontvangt voordat u verder wordt gepubliceerd. Nadat je hebt gecontroleerd of de previewstream goed is, kun je het Live Event gebruiken om de live stream beschikbaar te maken voor levering via een of meer (vooraf gemaakte) Streaming Endpoints. Maak hiervoor een nieuwe [Live Output](https://docs.microsoft.com/rest/api/media/liveoutputs) op het Live Event.

> [!IMPORTANT]
> Zorg ervoor dat de video naar de url van de preview stroomt voordat u verdergaat!

## <a name="live-event-long-running-operations"></a>Langlopende bewerkingen van live-evenementen

Zie [langlopende bewerkingen](media-services-apis-overview.md#long-running-operations)voor meer informatie .

## <a name="live-outputs"></a>Live-uitvoer

Zodra je de stream naar het Live-evenement hebt gestreamd, kun je de streaming-gebeurtenis beginnen door een [Asset,](https://docs.microsoft.com/rest/api/media/assets) [Live Output](https://docs.microsoft.com/rest/api/media/liveoutputs)en [Streaming Locator](https://docs.microsoft.com/rest/api/media/streaminglocators)te maken. Live Output archiveert de stream en maakt deze beschikbaar voor kijkers via het [Streaming Endpoint.](https://docs.microsoft.com/rest/api/media/streamingendpoints)  

Zie [Een cloud-DVR gebruiken](live-event-cloud-dvr.md)voor gedetailleerde informatie over Live-uitvoer.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

Zie het [artikel over veelgestelde vragen.](frequently-asked-questions.md#live-streaming)

## <a name="ask-questions-and-get-updates"></a>Stel vragen en ontvang updates

Bekijk het communityartikel [van Azure Media Services](media-services-community.md) om verschillende manieren te zien waarop u vragen stellen, feedback geven en updates ontvangen over Media Services.

## <a name="next-steps"></a>Volgende stappen

[Zelfstudie voor live streaming](stream-live-tutorial-with-api.md)
