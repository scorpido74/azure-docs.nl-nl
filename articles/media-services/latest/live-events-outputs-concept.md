---
title: Live Events en live outputs concepten in Azure Media Services v3
titleSuffix: Azure Media Services
description: Dit onderwerp bevat een overzicht van Live Events en live outputs in Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 9a32cd4db9a4c4dbd2b5f36c16feef4717790c3c
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291463"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Live-evenementen en live-uitvoer in Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Met Azure Media Services kunt u live gebeurtenissen aan uw klanten leveren via de Azure-Cloud. Als u uw live streaming-gebeurtenissen in Media Services v3 wilt instellen, moet u de concepten begrijpen die in dit artikel worden besproken.

> [!TIP]
> Voor klanten die migreren van Media Services v2 Api's, vervangt de **live gebeurtenis** entiteit het **kanaal** in v2 en de **Live uitvoer** vervangt het **programma**.

## <a name="live-events"></a>Livegebeurtenissen

[Livegebeurtenissen](/rest/api/media/liveevents) zijn verantwoordelijk voor het opnemen en verwerken van de live videofeeds. Wanneer u een live gebeurtenis maakt, wordt er een primair en secundair invoer eindpunt gemaakt dat u kunt gebruiken om een live signaal van een extern coderings programma te verzenden. Met het externe Live coderings programma wordt de bijdrage feed naar het invoer eindpunt verzonden met behulp van het [RTMP](https://www.adobe.com/devnet/rtmp.html) -of [Smooth streaming](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) (gefragmenteerde-MP4) invoer protocol. Voor het RTMP-opname protocol kan de inhoud worden verzonden in de Clear ( `rtmp://` ) of veilig versleuteld op de kabel ( `rtmps://` ). Voor het Smooth Streaming opname protocol zijn de ondersteunde URL-schema's `http://` of `https://` .  

## <a name="live-event-types"></a>Live gebeurtenis typen

Een [live-gebeurtenis](/rest/api/media/liveevents) kan worden ingesteld op een *Pass-Through* -(een on-premises Live coderings programma verzendt een multi-bitrate stroom) of *Live encoding* (een on-premises Live Encoder verzendt een stream met één bitsnelheid). De typen worden tijdens het maken ingesteld met behulp van [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType. none**: een on-premises Live coderings programma verzendt een stream met meerdere bitrates. De opgenomen stroom slaagt via de live-gebeurtenis zonder verdere verwerking. Ook wel de Pass-Through-modus genoemd.
* **LiveEventEncodingType. Standard**: een on-premises Live coderings programma verzendt een stream met één bitsnelheid naar de live-gebeurtenis en Media Services maakt meerdere bitrate-streams. Als de bijdrage-feed van 720p of hoger is, wordt met de voor instelling **Default720p** een set van 6 omzetting/bitrate-paren gecodeerd.
* **LiveEventEncodingType. Premium1080p**: een on-premises Live coderings programma verzendt een stream met één bitsnelheid naar de live-gebeurtenis en Media Services maakt meerdere bitrate-streams. De Default1080p-voor instelling bepaalt de uitvoerset van de paren Resolution/bitrates.

### <a name="pass-through"></a>Pass-through

![Pass-through live-gebeurtenis met Media Services voorbeeld diagram](./media/live-streaming/pass-through.svg)

Bij gebruik van de pass-through **livegebeurtenis** bent u afhankelijk van de on-premises live-encoder voor het genereren van een multiple-bitrate videostream en het verzenden ervan als de bijdragefeed voor de livegebeurtenis (met behulp van RTMP of een gefragmenteerd MP4-protocol). De livegebeurtenis voert de binnenkomende videostream vervolgens zonder verdere verwerking uit. Een dergelijke door gang werkende gebeurtenis is geoptimaliseerd voor langlopende Live-gebeurtenissen of 24x365e lineaire live-streaming. Bij het maken van dit type livegebeurtenis, geeft u None op (LiveEventEncodingType.None).

U kunt de bijdragefeed verzenden bij een resolutie tot maximaal 4K en een framesnelheid van 60 frames per seconde, met ofwel H.264/AVC- of H.265/HEVC-videocodecs, en AAC (AAC-LC, HE-AACv1 of HE-AACv2)-audiocodec. Zie vergelijking van live- [gebeurtenis typen](live-event-types-comparison.md)voor meer informatie.

> [!NOTE]
> Het gebruik van een Pass-Through-methode is de meest rendabele manier om live streamen uit te voeren wanneer u meerdere gebeurtenissen gedurende een lange periode uitvoert, en u hebt al geïnvesteerd in on-premises encoders. Zie de details over de [prijzen](https://azure.microsoft.com/pricing/details/media-services/).
>

Zie een .NET-code voorbeeld in [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Live Encoding  

![voorbeeld diagram van Live encoding met Media Services](./media/live-streaming/live-encoding.svg)

Wanneer u live encoding met Media Services gebruikt, configureert u uw on-premises Live coderings programma voor het verzenden van een video met één bitsnelheid als de bijdrage aan de live gebeurtenis (met RTMP-of gefragmenteerd-MP4-Protocol). Vervolgens stelt u een live gebeurtenis in, zodat deze de inkomende single-bitrate stroom naar een [video stroom met meerdere bitsnelheden](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)codeert en de uitvoer beschikbaar maakt voor het afspelen van apparaten via protocollen zoals MPEG-Dash, HLS en Smooth streaming.

Wanneer u gebruikmaakt van Live encoding, kunt u de invoer van de bijdrage alleen verzenden naar oplossingen tot 1080p-resolutie met een frame snelheid van 30 frames per seconde, met H. 264/AVC Video codec en AAC (AAC-LC, HE-AACv1 of AACv2) audiocodec. Houd er rekening mee dat door gegeven Live-gebeurtenissen oplossingen kunnen ondersteunen van Maxi maal 4.000 tot 60 frames per seconde. Zie vergelijking van live- [gebeurtenis typen](live-event-types-comparison.md)voor meer informatie.

De resoluties en bitrates die zijn opgenomen in de uitvoer van het Live coderings programma, worden bepaald door de voor instelling. Als u gebruikmaakt van een **standaard** Live coderings programma (LiveEventEncodingType. Standard), geeft de *Default720p* -voor instelling een set van zes omzettings/bitsnelheid-paren aan, van 720p tot 3,5 Mbps omlaag tot 192p bij 200 kbps. Als u een **Premium1080p** Live coderings programma (LiveEventEncodingType. Premium1080p) gebruikt, wordt met de voor instelling voor *Default1080p* een set van zes omzettings-en frequentie paren opgegeven, van 1080p tot 3,5 Mbps omlaag tot 180p met 200 kbps. Zie [Systeemwaarden](live-event-types-comparison.md#system-presets) voor meer informatie.

> [!NOTE]
> Als u de voor instelling voor Live encoding wilt aanpassen, opent u een ondersteunings ticket via Azure Portal. Geef de gewenste tabel met omzetting en bitsnelheden op. Controleer of er slechts één laag is op 720p (als u een voor instelling voor een Standard-coderings programma aanvraagt) of op 1080p (als u een voor instelling voor een Premium1080p Live Encoder wilt aanvragen) en 6 lagen.

## <a name="creating-live-events"></a>Live-gebeurtenissen maken

### <a name="options"></a>Opties

Wanneer u een live gebeurtenis maakt, kunt u de volgende opties opgeven:

* Het streaming-protocol voor de livegebeurtenis (momenteel worden de protocollen RTMP en Smooth Streaming ondersteund).<br/>U kunt de protocoloptie niet wijzigen terwijl de livegebeurtenis of de daaraan gekoppelde live-uitvoer worden uitgevoerd. Als u verschillende protocollen nodig hebt, maakt u een afzonderlijke live-gebeurtenis voor elk streaming-protocol.  
* Bij het maken van de gebeurtenis kunt u opgeven dat deze automatisch wordt gestart. <br/>Wanneer autostart is ingesteld op True, wordt de Live gebeurtenis gestart na het maken ervan. De facturering begint zodra de live-gebeurtenis wordt gestart. U moet expliciet Stop aanroepen in de resource van de livegebeurtenis om verdere facturering stop te zetten. U kunt ook de gebeurtenis starten wanneer u klaar bent om te streamen.

    Zie [Live Event states and billing](live-event-states-billing.md) (Statussen en facturering voor livegebeurtenissen) voor meer informatie.

* IP-beperkingen voor de opname en voorbeeldweergave. U kunt de IP-adressen definiëren die zijn toegestaan om een video van deze livegebeurtenis op te nemen. Toegestane IP-adressen kunnen worden opgegeven als één IP-adres (bijvoorbeeld 10.0.0.1), een IP-adresbereik met een IP-adres en een CIDR-subnetmasker (bijvoorbeeld 10.0.0.1/22) of een IP-adresbereik met een IP-adres en een decimaal subnetmasker met punten (bijvoorbeeld , ' 10.0.0.1(255.255.252.0)').<br/>Als geen IP-adressen zijn opgegeven en er geen regeldefinitie bestaat, zijn er geen IP-adressen toegestaan. Als u IP-adres(sen) wilt toestaan, maakt u een regel en stelt u 0.0.0.0/0 in.<br/>De IP-adressen moeten een van de volgende indelingen hebben: IpV4-adres met 4 cijfers of een CIDR-adresbereik.

    Als u bepaalde Ip's wilt inschakelen op uw eigen firewalls of als u invoer wilt beperken voor uw Live-gebeurtenissen in azure IP-adressen, downloadt u een JSON-bestand van de IP-adresbereiken van het [Azure-Data Center](https://www.microsoft.com/download/details.aspx?id=41653). Selecteer de sectie **Details** op de pagina voor meer informatie over dit bestand.
    
* Wanneer u de gebeurtenis maakt, kunt u ervoor kiezen om live-transcripties in te scha kelen. <br/> Live transcriptie is standaard uitgeschakeld. U kunt deze eigenschap niet wijzigen als de live-gebeurtenis of de gekoppelde actieve uitvoer bewerkingen worden uitgevoerd. 

### <a name="naming-rules"></a>Naamgevingsregels

* De maximale naam van een live-gebeurtenis is 32 tekens.
* De naam moet volgen op dit [regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) -patroon: `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` .

Zie ook [naam conventies voor streaming-eind punten](streaming-endpoint-concept.md#naming-convention).

> [!TIP]
> Als u de unieke naam van uw live-evenement wilt waarborgen, kunt u een GUID genereren en vervolgens alle afbreek streepjes en accolades verwijderen (indien van toepassing). De teken reeks is uniek voor alle Live-gebeurtenissen en de lengte ervan is gegarandeerd 32.

## <a name="live-event-ingest-urls"></a>Url's voor Live Event opname

Zodra de live gebeurtenis is gemaakt, kunt u opname-Url's ophalen die u aanbiedt aan de Live on-premises encoder. De live-encoder gebruikt deze URL's voor het invoeren van een live-stream. Zie [Aanbevolen on-premises Live coderings](recommended-on-premises-live-encoders.md)Programma's voor meer informatie.

U kunt niet-vanity-URL's en vanity-URL's gebruiken.

> [!NOTE] 
> Stel de vanity-modus in om een URL voor opnemen voorspellend te maken.

* Niet-Vanity-URL

    Niet-Vanity-URL is de standaard modus in Media Services v3. U krijgt de livegebeurtenis mogelijk snel, maar de opname-URL is alleen bekend als de livegebeurtenis wordt gestart. De URL wordt gewijzigd als u de livegebeurtenis stopt of start. <br/>Niet-Vanity is handig in scenario's wanneer een eind gebruiker wil streamen met behulp van een app waarin de app een live gebeurtenis spoed wil ontvangen en een dynamische opname-URL geen problemen vormt.

    Als een client-app geen opname-URL hoeft te genereren voordat de live-gebeurtenis wordt gemaakt, laat Media Services automatisch het toegangs token genereren voor de live-gebeurtenis.

* Vanity-URL

    De Vanity-modus wordt aanbevolen door grote media-broadcasters die gebruikmaken van hardware broadcast encoders en ze hun encoders niet opnieuw moeten configureren wanneer ze de live-gebeurtenis starten. Deze broadcasters willen een voorspellende opname-URL die in de loop van de tijd niet verandert.
    
    > [!NOTE]
    > In de Azure Portal de Vanity-URL de naam '*permanente invoer-URL*'.

    Als u deze modus in de API wilt opgeven, moet u instellen op de `vanityUrl` `true` aanmaak tijd (standaard instelling `false` ). U moet ook uw eigen toegangs token () door geven `LiveEventInput.accessToken` tijdens de aanmaak tijd. U geeft de token waarde op om een wille keurig token in de URL te vermijden. Het toegangs token moet een geldige GUID-teken reeks zijn (met of zonder de afbreek streepjes). Zodra de modus is ingesteld, kan deze niet worden bijgewerkt.

    Het toegangs token moet uniek zijn in uw Data Center. Als uw app een Vanity-URL moet gebruiken, is het raadzaam om altijd een nieuw GUID-exemplaar voor uw toegangs token te maken (in plaats van een bestaande GUID opnieuw te gebruiken).

    Gebruik de volgende Api's om de Vanity-URL in te scha kelen en het toegangs token in te stellen op een geldige GUID (bijvoorbeeld `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"` ).  

    |Taal|Vanity-URL inschakelen|Toegangstoken instellen|
    |---|---|---|
    |REST|[Eigenschappen. vanityUrl](/rest/api/media/liveevents/create#liveevent)|[LiveEventInput. accessToken](/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--Vanity-URL](/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--Access-token](/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput. AccessToken](/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>Naamgevingsregels voor live-opname-URL's

* De *willekeurige* tekenreeks hieronder is een 128-bits hexadecimaal getal (bestaande uit 32 tekens, van 0-9 en a-f).
* *uw toegangs token*: de geldige GUID-teken reeks die u instelt wanneer u de Vanity-modus gebruikt. Bijvoorbeeld `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *Stream name*: geeft de naam van de stream voor een specifieke verbinding aan. De waarde van de stroom naam wordt meestal toegevoegd door de live encoder die u gebruikt. U kunt het Live coderings programma zodanig configureren dat elke naam wordt gebruikt om de verbinding te beschrijven, bijvoorbeeld: "video1_audio1", "video2_audio1", "Stream".

#### <a name="non-vanity-url"></a>Niet-Vanity-URL

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>Vanity-URL

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>Preview-URL voor Live Event

Zodra de live-gebeurtenis begint met het ontvangen van de contributie, kunt u het preview-eind punt gebruiken om te bekijken en te controleren of u de Live Stream ontvangt voordat u verder gaat publiceren. Nadat u hebt gecontroleerd of de voorbeeld stroom goed is, kunt u de live-gebeurtenis gebruiken om de live-stream beschikbaar te maken voor levering via een of meer (vooraf gemaakte) streaming-eind punten. U kunt dit doen door een nieuwe [Live-uitvoer](/rest/api/media/liveoutputs) te maken voor de live-gebeurtenis.

> [!IMPORTANT]
> Zorg ervoor dat de video stroomt naar de Preview-URL voordat u doorgaat.

## <a name="live-event-long-running-operations"></a>Langlopende bewerkingen voor Live Event

Zie [langlopende bewerkingen](media-services-apis-overview.md#long-running-operations)voor meer informatie.

## <a name="live-outputs"></a>Live-uitvoer

Zodra u de stroom naar de live gebeurtenis hebt gestroomd, kunt u de streaming-gebeurtenis starten door een [Asset](/rest/api/media/assets), [Live output](/rest/api/media/liveoutputs)en [streaming-Locator](/rest/api/media/streaminglocators)te maken. Live uitvoer archiveert de stream en maakt deze beschikbaar voor gebruikers via het [streaming-eind punt](/rest/api/media/streamingendpoints).  

Zie [een Cloud-DVR gebruiken](live-event-cloud-dvr.md)voor meer informatie over live-uitvoer.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

Zie het artikel [Veelgestelde vragen](frequently-asked-questions.md#live-streaming) .

## <a name="ask-questions-and-get-updates"></a>Vragen stellen en updates ophalen

Ga naar het artikel van de [Azure Media Services-community](media-services-community.md) voor verschillende manieren om vragen te stellen, feedback te geven en updates voor Media Services op te halen.

## <a name="next-steps"></a>Volgende stappen

[Zelf studie over live streamen](stream-live-tutorial-with-api.md)
