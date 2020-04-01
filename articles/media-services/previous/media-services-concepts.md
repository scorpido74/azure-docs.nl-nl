---
title: Azure Media Services-concepten | Microsoft Documenten
description: In dit artikel vindt u een kort overzicht van Microsoft Azure Media Services-concepten en koppelingen naar andere artikelen voor meer informatie.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 260ddccc1a1b0bd4090284025b79e20ff5ce4fdc
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475244"
---
# <a name="azure-media-services-concepts"></a>Azure Media Services-concepten 

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

Dit onderwerp geeft een overzicht van de belangrijkste Media Services concepten.

## <a name="assets-and-storage"></a><a id="assets"/>Activa en opslag
### <a name="assets"></a>Assets
Een [asset](https://docs.microsoft.com/rest/api/media/operations/asset) bevat digitale bestanden (waaronder video, audio, afbeeldingen, miniatuurverzamelingen, teksttracks en bestanden met ondertiteling) en de metagegevens over deze bestanden. Nadat de digitale bestanden zijn geüpload naar een asset, kunnen ze worden gebruikt in de mediaservices coderingen en streaming workflows.

Een asset wordt toegewezen aan een blobcontainer in het Azure Storage-account en de bestanden in het asset worden opgeslagen als blokblobs in die container. Paginablobs worden niet ondersteund door Azure Media Services.

Bij het bepalen welke media-inhoud u wilt uploaden en opslaan in een actief, zijn de volgende overwegingen van toepassing:

* Een asset mag slechts één unieke instantie van media-inhoud bevatten. Bijvoorbeeld een enkele bewerking van een tv-aflevering, film of advertentie.
* Een actief mag geen meerdere weergaven of bewerkingen van een audiovisueel bestand bevatten. Een voorbeeld van een onjuist gebruik van een asset zou proberen om meer dan een tv-aflevering, advertentie of meerdere camerahoeken op te slaan vanuit een enkele productie in een asset. Het opslaan van meerdere weergaven of bewerkingen van een audiovisueel bestand in een asset kan leiden tot problemen bij het indienen van coderingstaken, het streamen en beveiligen van de levering van het item later in de workflow.  

### <a name="asset-file"></a>Assetbestand
Een [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) vertegenwoordigt een echt video- of audiobestand dat is opgeslagen in een blobcontainer. Een assetbestand is altijd gekoppeld aan een actief en een actief kan een of meer bestanden bevatten. De encoder-taak Media Services mislukt als een assetbestandobject niet is gekoppeld aan een digitaal bestand in een blobcontainer.

De **instantie AssetFile** en het werkelijke mediabestand zijn twee verschillende objecten. De instantie AssetFile bevat metagegevens over het mediabestand, terwijl het mediabestand de werkelijke media-inhoud bevat.

U moet niet proberen de inhoud van blobcontainers te wijzigen die door Media Services zijn gegenereerd zonder API's van mediaservice te gebruiken.

### <a name="asset-encryption-options"></a>Opties voor assetversleuteling
Afhankelijk van het type inhoud dat u wilt uploaden, opslaan en leveren, biedt Media Services verschillende versleutelingsopties waaruit u kiezen.

>[!NOTE]
>Er wordt geen versleuteling gebruikt. Dit is de standaardwaarde. Bij gebruik van deze optie is uw inhoud niet beveiligd tijdens het transport of in rust in opslag.

Als u van plan bent een MP4 te leveren met behulp van progressieve download, gebruikt u deze optie om uw inhoud te uploaden.

**StorageEncrypted** - Gebruik deze optie om uw duidelijke inhoud lokaal te versleutelen met behulp van AES 256-bits versleuteling en deze vervolgens te uploaden naar Azure Storage, waar deze in rust versleuteld wordt opgeslagen. Activa die worden beschermd met opslagversleuteling worden automatisch onversleuteld en in een versleuteld bestandssysteem geplaatst voordat ze worden gecodeerd en worden optioneel opnieuw versleuteld voordat ze worden geüpload als een nieuw uitvoerelement. De primaire use case voor opslagversleuteling is wanneer u uw hoogwaardige invoermediabestanden wilt beveiligen met sterke versleuteling in rust op schijf. 

Als u een opslagversleuteld activum wilt leveren, moet u het leveringsbeleid van het activum configureren, zodat Media Services weet hoe u uw inhoud wilt leveren. Voordat uw asset kan worden gestreamd, verwijdert de streamingserver de opslagversleuteling en streamt u uw inhoud met behulp van het opgegeven leveringsbeleid (bijvoorbeeld AES, PlayReady of geen versleuteling). 

**CommonEncryptionProtected** - Gebruik deze optie als u inhoud wilt versleutelen (of uploaden met algemene versleuteling of PlayReady DRM (bijvoorbeeld Smooth Streaming protected met PlayReady DRM).

**EnvelopeEncryptionProtected** - Gebruik deze optie als u HTTP Live Streaming (HLS) versleuteld wilt beschermen (of uploaden) versleuteld met Advanced Encryption Standard (AES). Als u HLS uploadt die al versleuteld is met AES, moet deze zijn versleuteld door Transform Manager.

### <a name="access-policy"></a>Toegangsbeleid
Een [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy) definieert machtigingen (zoals lezen, schrijven en lijst) en de duur van de toegang tot een asset. U zou meestal een AccessPolicy-object doorgeven aan een locator die vervolgens wordt gebruikt om toegang te krijgen tot de bestanden in een asset.

>[!NOTE]
>Er geldt een limiet van 1.000.000 beleidsregels voor verschillende AMS-beleidsitems (bijvoorbeeld voor Locator-beleid of ContentKeyAuthorizationPolicy). U moet dezelfde beleids-id gebruiken als u altijd dezelfde dagen/toegangsmachtigingen gebruikt, bijvoorbeeld beleidsregels voor locators die zijn bedoeld om gedurende een lange periode gehandhaafd te blijven (niet-upload-beleidsregels). Raadpleeg [dit](media-services-dotnet-manage-entities.md#limit-access-policies) onderwerp voor meer informatie.

### <a name="blob-container"></a>Blobcontainer
Een blobcontainer biedt een groepering van een reeks blobs. Blob-containers worden gebruikt in Media Services als grenspunt voor toegangscontrole en SAS-locators (Shared Access Signature) op assets. Een Azure Storage-account kan een onbeperkt aantal blobcontainers bevatten. Een container kan een onbeperkt aantal blobs bevatten.

>[!NOTE]
> U moet niet proberen de inhoud van blobcontainers te wijzigen die door Media Services zijn gegenereerd zonder API's van mediaservice te gebruiken.
> 
> 

### <a name="locators"></a><a id="locators"/>Locators
[Locator](https://docs.microsoft.com/rest/api/media/operations/locator)s bieden een toegangspunt om toegang te krijgen tot de bestanden in een asset. Een toegangsbeleid wordt gebruikt om de machtigingen en duur te definiëren die een client toegang heeft tot een bepaald item. Locators kunnen veel tot één relatie hebben met een toegangsbeleid, zodat verschillende locators verschillende begintijden en verbindingstypen kunnen bieden aan verschillende clients, terwijl ze allemaal dezelfde machtigings- en duurinstellingen gebruiken; Vanwege een beperking van het beleid voor gedeelde toegang die is ingesteld door Azure-opslagservices, u echter niet meer dan vijf unieke locators tegelijk aan een bepaald item hebben. 

Media Services ondersteunt twee soorten locators: OnDemandOrigin-locators, die worden gebruikt om media te streamen (bijvoorbeeld MPEG DASH, HLS of Smooth Streaming) of media- en SAS URL-locators geleidelijk te downloaden, die worden gebruikt om mediabestanden te uploaden of te downloaden naar\van Azure-opslag. 

>[!NOTE]
>De lijstmachtiging (AccessPermissions.List) mag niet worden gebruikt bij het maken van een OnDemandOrigin-locator. 

### <a name="storage-account"></a>Storage-account
Alle toegang tot Azure Storage gebeurt via een opslagaccount. Een Media Service-account kan worden gekoppeld aan een of meer opslagaccounts. Een account kan een onbeperkt aantal containers bevatten, zolang de totale grootte onder de 500 TB per opslagaccount ligt.  Media Services biedt tooling op SDK-niveau, zodat u meerdere opslagaccounts beheren en de verdeling van uw assets tijdens het uploaden naar deze accounts laden op basis van statistieken of willekeurige distributie. Zie Werken met [Azure Storage](https://msdn.microsoft.com/library/azure/dn767951.aspx)voor meer informatie. 

## <a name="jobs-and-tasks"></a>Taken en taken
Een [taak](https://docs.microsoft.com/rest/api/media/operations/job) wordt meestal gebruikt om één audio-/videopresentatie te verwerken (bijvoorbeeld indexeren of coderen). Als u meerdere video's verwerkt, maakt u een taak voor elke video die moet worden gecodeerd.

Een taak bevat metagegevens over de uit te voeren verwerking. Elke taak bevat een of meer [taaktaken](https://docs.microsoft.com/rest/api/media/operations/task)die een atoomverwerkingstaak, de invoerelementen, uitvoeractiva, een mediaprocessor en de bijbehorende instellingen opgeven. Taken binnen een taak kunnen aan elkaar worden geketend, waarbij de uitvoerasset van één taak wordt gegeven als invoerelement voor de volgende taak. Op deze manier kan één taak alle verwerking bevatten die nodig is voor een mediapresentatie.

## <a name="encoding"></a><a id="encoding"></a>Encoding
Azure Media Services biedt meerdere opties voor het coderen van media in de cloud.

Wanneer u begint met Media Services, is het belangrijk om het verschil tussen codecs en bestandsindelingen te begrijpen.
Codecs zijn de software die de compressie-/decompressiealgoritmen implementeert, terwijl bestandsindelingen containers zijn die de gecomprimeerde video bevatten.

Media Services biedt dynamische verpakkingen waarmee u uw adaptieve bitrate MP4- of Smooth Streaming-gecodeerde inhoud leveren in streamingformaten die worden ondersteund door Media Services (MPEG DASH, HLS, Smooth Streaming) zonder dat u opnieuw hoeft te verpakken in deze streamingformaten.

Om te profiteren van [dynamische verpakking,](media-services-dynamic-packaging-overview.md)moet u uw mezzanine (bron) bestand coderen in een set van adaptieve bitrate MP4-bestanden of adaptieve bitrate Smooth Streaming bestanden en hebben ten minste een standaard of premium streaming eindpunt in gestarte staat.

Media Services ondersteunt de volgende on-demand encoders die in dit artikel worden beschreven:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Media Encoder Premium Workflow](media-services-encode-asset.md#media-encoder-premium-workflow)

Zie [Encoders voor](media-services-encode-asset.md)informatie over ondersteunde encoders .

## <a name="live-streaming"></a>Live streamen
In Azure Media Services vertegenwoordigt een kanaal een pijplijn voor het verwerken van live streaming-inhoud. Een kanaal ontvangt op twee manieren live-invoerstreams:

* Een on-premises live encoder stuurt multi-bitrate RTMP of Smooth Streaming (Fragmented MP4) naar het Kanaal. U de volgende live-encoders gebruiken die multi-bitrate Smooth Streaming uitvoeren: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco en Elemental. De volgende live encoders output RTMP: Adobe Flash Live Encoder, [Telestream Wirecast](media-services-configure-wirecast-live-encoder.md), Teradek, Haivision en Tricaster encoders. De ingenomen streams passeren kanalen zonder verdere transcodering en codering. Desgevraagd levert Media Services de stream aan klanten.
* Een enkele bitrate stream (in een van de volgende indelingen: RTMP, of Smooth Streaming (Fragmented MP4)) wordt verzonden naar het kanaal dat is ingeschakeld om live codering uit te voeren met Media Services. Het kanaal codeert de inkomende single-bitrate stream vervolgens live naar een (adaptieve) multi-bitrate videostream. Desgevraagd levert Media Services de stream aan klanten.

### <a name="channel"></a>Kanaal
In Media Services zijn [Channel](https://docs.microsoft.com/rest/api/media/operations/channel)s verantwoordelijk voor de verwerking van live streaming content. Een kanaal biedt een invoereindpunt (inname VAN URL) dat u vervolgens aan een live transcoder verstrekt. Het kanaal ontvangt live input streams van de live transcoder en maakt het beschikbaar voor streaming via een of meer StreamingEndpoints. Kanalen bieden ook een voorbeeldeindpunt (preview-URL) dat u gebruikt om uw stream te bekijken en te valideren voordat u verder wordt verwerkt en geleverd.

U de inname-URL en de url van het voorbeeld krijgen wanneer u het kanaal maakt. Om deze URL's te krijgen, hoeft het kanaal niet in de beginstatus te staan. Wanneer u klaar bent om gegevens van een live transcoder in het kanaal te duwen, moet het kanaal worden gestart. Zodra de live transcoder gegevens begint in te nemen, u een voorbeeld van uw stream bekijken.

Elk Media Services-account kan meerdere kanalen, meerdere programma's en meerdere StreamingEndpoints bevatten. Afhankelijk van de bandbreedte en beveiligingsbehoeften kunnen StreamingEndpoint-services worden toegewezen aan een of meer kanalen. Elk StreamingEndpoint kan uit elk kanaal worden verwijderd.

### <a name="program-event"></a>Programma (evenement)
Met een [programma (evenement)](https://docs.microsoft.com/rest/api/media/operations/program) u de publicatie en opslag van segmenten in een live stream beheren. Kanalen beheren programma's (gebeurtenissen). De kanaal- en programmarelatie is vergelijkbaar met traditionele media waar een kanaal een constante stroom van inhoud heeft en een programma is ondergebracht bij een bepaalde getimede gebeurtenis op dat kanaal.
U het aantal uren opgeven dat u de opgenomen inhoud voor het programma wilt behouden door de eigenschap **ArchiveWindowLength in** te stellen. Deze waarde kan worden ingesteld van minimaal 5 minuten tot maximaal 25 uur.

ArchiveWindowLength bepaalt ook de maximale hoeveelheid tijd die clients terug in de tijd kunnen zoeken vanuit de huidige live-positie. Programma's kunnen in de opgegeven tijdsduur worden uitgevoerd, maar de inhoud die achter de lengte van het venster valt, wordt altijd verwijderd. De waarde van deze eigenschap bepaalt ook hoe lang de clientmanifesten kunnen groeien.

Elk programma (gebeurtenis) is gekoppeld aan een asset. Als u het programma wilt publiceren, moet u een locator maken voor het bijbehorende actief. Met deze locator kunt u een streaming-URL maken die u aan uw clients kunt leveren.

Een kanaal ondersteunt maximaal drie gelijktijdig actieve programma's, zodat u meerdere archieven van dezelfde binnenkomende stream kunt maken. Hierdoor kunt u verschillende onderdelen van een gebeurtenis naar behoefte publiceren en archiveren. Zo kan het voor uw bedrijf nodig zijn zes uur van een programma te archiveren, maar alleen de laatste tien minuten uit te zenden. Hiervoor moet u twee gelijktijdig actieve programma's maken. Het ene programma wordt ingesteld om zes uur van de gebeurtenis te archiveren, maar het programma wordt niet gepubliceerd. Het andere programma wordt ingesteld om tien minuten te archiveren en dit programma wordt wel gepubliceerd.

Zie voor meer informatie:

* [Werken met kanalen die zijn ingeschakeld om live codering uit te voeren met Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Werken met kanalen die multibitrate live stream ontvangen van on-premises encoders](media-services-live-streaming-with-onprem-encoders.md)
* [Quota en beperkingen](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>Inhoud beschermen
### <a name="dynamic-encryption"></a>Dynamische versleuteling
Met Azure Media Services u uw media beveiligen vanaf het moment dat uw computer wordt verlaten via opslag, verwerking en levering. Met Media Services u uw inhoud dynamisch versleuteld leveren met Advanced Encryption Standard (AES) (met behulp van 128-bits versleutelingssleutels) en algemene versleuteling (CENC) met PlayReady en/of Widevine DRM. Media Services biedt ook een service voor het leveren van AES-sleutels en PlayReady-licenties aan geautoriseerde clients.

Momenteel u de volgende streamingformaten versleutelen: HLS, MPEG DASH en Smooth Streaming. U progressieve downloads niet versleutelen.

Als u wilt dat Media Services een asset versleutelt, moet u een versleutelingssleutel (CommonEncryption of EnvelopeEncryption) koppelen aan uw asset en ook het autorisatiebeleid voor de sleutel configureren.

Als u een opslagversleuteld asset wilt streamen, moet u het leveringsbeleid van het actief configureren om aan te geven hoe u uw asset wilt leveren.

Wanneer een stream wordt aangevraagd door een speler, gebruikt Media Services de opgegeven sleutel om uw inhoud dynamisch te versleutelen met behulp van een envelopversleuteling (met AES) of algemene versleuteling (met PlayReady of Widevine). Om de stream te decoderen, zal de speler de sleutel aanvragen bij de belangrijke leveringsservice. Als u wilt beslissen of de gebruiker gemachtigd is om de sleutel te krijgen, evalueert de service het autorisatiebeleid dat u voor de sleutel hebt opgegeven.

### <a name="token-restriction"></a>Tokenbeperking
Het autorisatiebeleid voor inhoudssleutel kan een of meer autorisatiebeperkingen bevatten: open, tokenbeperking of IP-beperking. Het beleid met de tokenbeperking moet vergezeld gaan van een token dat is uitgegeven door Secure Token Service (STS). Media Services ondersteunt tokens in de indelingen Simple Web Tokens (SWT) en JSON Web Token (JWT). Media Services biedt geen Secure Token Services. U een aangepaste STS maken. De STS moet zijn geconfigureerd om een token te maken dat is ondertekend met de opgegeven sleutel- en uitgifteclaims die u hebt opgegeven in de configuratie van tokenbeperkingen. De mediaservices-service voor het leveren van de sleutel (of licentie) retourneert de gevraagde sleutel (of licentie) aan de client als het token geldig is en de claims in het token overeenkomen met de aanvragen die zijn geconfigureerd voor de sleutel (of licentie).

Wanneer u het beleid met tokenbeperkte beleidshebt geconfigureerd, moet u de primaire verificatiesleutel, de emittent en de doelgroepparameters opgeven. De primaire verificatiesleutel bevat de sleutel waarmee het token is ondertekend, uitgever is de beveiligde tokenservice die het token uitgeeft. De doelgroep (ook wel scope genoemd) beschrijft de intentie van het token of de bron waartoe het token toegang geeft. De service voor het leveren van de sleutel van Media Services valideert dat deze waarden in het token overeenkomen met de waarden in de sjabloon.

Raadpleeg voor meer informatie de volgende artikelen:
- [Inhoudsoverzicht beveiligen](media-services-content-protection-overview.md)
- [Bescherm met AES-128](media-services-protect-with-aes128.md)
- [Bescherm met PlayReady/Widevine](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>Leveren
### <a name="dynamic-packaging"></a><a id="dynamic_packaging"/>Dynamische verpakking
Wanneer u met Media Services werkt, is het raadzaam om uw mezzaninebestanden te coderen in een adaptieve bitrate MP4-set en vervolgens de set om te zetten naar het gewenste formaat met behulp van de [Dynamische verpakking.](media-services-dynamic-packaging-overview.md)

### <a name="streaming-endpoint"></a>Streaming-eindpunt
Een StreamingEndpoint vertegenwoordigt een streamingservice die inhoud rechtstreeks kan leveren aan een clientplayertoepassing of aan een CONTENT Delivery Network (CDN) voor verdere distributie (Azure Media Services biedt nu de Azure CDN-integratie.) De uitgaande stream van een streaming endpoint-service kan een live stream zijn, of een video on-demand Asset in uw Media Services-account. Klanten van Media Services kiezen ofwel een **Standard**-streaming-eindpunt of een of meer Premium-**streaming**-eindpunten, afhankelijk van hun behoeften. Standaard streaming endpoint is geschikt voor de meeste streaming workloads. 

Standard-streaming-eindpunten zijn geschikt voor de meeste streaming-workloads. Standaard streaming eindpunten bieden de flexibiliteit om uw inhoud te leveren aan vrijwel elk apparaat door middel van dynamische verpakking in HLS, MPEG-DASH, en Smooth Streaming, evenals dynamische encryptie voor Microsoft PlayReady, Google Widevine, Apple Fairplay, en AES128.  Ze schalen ook van zeer klein naar zeer groot publiek met duizenden gelijktijdige kijkers via Azure CDN-integratie. Als u een geavanceerde workload hebt of als uw vereisten voor streamingcapaciteit niet passen bij de standaard doorvoerdoelen voor streaming-eindpunten of als u de capaciteit van de StreamingEndpoint-service wilt beheren om aan de groeiende bandbreedtebehoeften te voldoen, wordt het aanbevolen om schaaleenheden toe te wijzen (ook wel premium streaming-eenheden genoemd).

Het wordt aanbevolen om dynamische verpakkingen en/of dynamische encryptie te gebruiken.

>[!NOTE]
>Wanneer uw AMS-account is gemaakt, wordt er een **standaardstreaming-eindpunt** met de status **Gestopt** toegevoegd aan uw account. Als u inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eindpunt van waar u inhoud wilt streamen, de status **Wordt uitgevoerd** hebben. 

Raadpleeg [dit](media-services-portal-manage-streaming-endpoints.md) onderwerp voor meer informatie.

Standaard kun je maximaal 2 streaming eindpunten in je Media Services-account hebben. Zie [Quota en beperkingen](media-services-quotas-and-limitations.md)als u een hogere limiet wilt aanvragen.

Er worden alleen kosten in rekening gebracht wanneer uw StreamingEndpoint in de werktoestand is.

### <a name="asset-delivery-policy"></a>Beleid voor het leveren van activa
Een van de stappen in de mediaservices-werkstroom voor het leveren van inhoud is het configureren van [leveringsbeleid voor assets](https://docs.microsoft.com/rest/api/media/operations/assetdeliverypolicy)die u wilt streamen. Het beleid voor de levering van activa vertelt Media Services hoe u wilt dat uw asset wordt geleverd: in welk streamingprotocol moet uw asset dynamisch worden verpakt (bijvoorbeeld MPEG DASH, HLS, Smooth Streaming of alles), ongeacht of u uw asset dynamisch wilt versleutelen en hoe (envelop of gemeenschappelijke versleuteling).

Als u een opslagversleuteld activum hebt, verwijdert de streamingserver voordat uw asset kan worden gestreamd, de opslagversleuteling en streamt de inhoud met behulp van het opgegeven leveringsbeleid. Als u bijvoorbeeld uw asset versleuteld wilt leveren met De Versleutelingscode (Advanced Encryption Standard) stelt u het beleidstype in op DynamicEnvelopeEncryption. Als u opslagversleuteling wilt verwijderen en het item wilt streamen in de foutvorm, stelt u het beleidstype in op NoDynamicEncryption.

### <a name="progressive-download"></a>Progressieve download
Met progressieve download u media afspelen voordat het hele bestand is gedownload. U slechts geleidelijk een MP4-bestand downloaden.

>[!NOTE]
>U moet versleutelde elementen decoderen als u wilt dat ze beschikbaar zijn voor progressieve download.

Als u gebruikers progressieve download-URL's wilt bieden, moet u eerst een OnDemandOrigin-locator maken. Het maken van de locator, geeft u de basis pad naar de asset. U moet dan de naam van het MP4-bestand toevoegen. Bijvoorbeeld:

http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

### <a name="streaming-urls"></a>URL's voor streaming
Uw inhoud streamen naar clients. Als u gebruikers streaming-URL's wilt bieden, moet u eerst een OnDemandOrigin-locator maken. Als u de locator maakt, krijgt u het basispad naar het element dat de inhoud bevat die u wilt streamen. Om deze inhoud te kunnen streamen, moet u dit pad echter verder wijzigen. Als u een volledige URL wilt maken voor het streamingmanifestbestand, moet u de padwaarde van de locator en de manifestnaam (filename.ism) toevoegen. Voeg vervolgens /Manifest en een geschikte indeling (indien nodig) toe aan het zoekpad.

U uw inhoud ook streamen via een TLS-verbinding. Zorg ervoor dat je streaming-URL's beginnen met HTTPS. Op dit moment ondersteunt AMS TLS niet met aangepaste domeinen.  

>[!NOTE]
>Je alleen via TLS streamen als het streaming-eindpunt van waaruit je je content levert, is gemaakt na 10 september 2014. Als uw streaming-URL's zijn gebaseerd op de streaming eindpunten die na 10 september zijn gemaakt, bevat de URL 'streaming.mediaservices.windows.net' (de nieuwe indeling). Streaming-URL's die 'origin.mediaservices.windows.net' (de oude indeling) bevatten, ondersteunen geen TLS. Als uw URL in de oude indeling is en u via TLS wilt kunnen streamen, maakt u een nieuw streamingeindpunt. Gebruik URL's die zijn gemaakt op basis van het nieuwe streaming-eindpunt om uw inhoud via TLS te streamen.

In de volgende lijst worden verschillende streamingformaten beschreven en worden voorbeelden gegeven:

* Smooth Streaming

{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

* MPEG DASH

{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest(format=mpd-time-csf)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

* Apple HTTP Live Streaming (HLS) V4

{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest(format=m3u8-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

* Apple HTTP Live Streaming (HLS) V3

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

