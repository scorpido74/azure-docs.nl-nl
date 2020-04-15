---
title: Azure Media Services v3 veelgestelde vragen| Microsoft Documenten
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/07/2020
ms.author: juliako
ms.openlocfilehash: 713acbd098255af2869d7a462c9990f3d7e10bf1
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309192"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Media Services v3 veelgestelde vragen

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Media Services v3.

## <a name="general"></a>Algemeen

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Welke Azure-rollen kunnen acties uitvoeren op Azure Media Services-bronnen? 

Zie [RBAC (Role-based access control) voor Media Services-accounts](rbac-overview.md).

### <a name="how-do-i-stream-to-apple-ios-devices"></a>Hoe stream ik naar Apple iOS-apparaten?

Zorg ervoor dat je aan het einde van je pad (na het **/manifest** gedeelte van de URL) de streaming origin-server hebt **(format=m3u8-aapl)** om http live streaming (HLS)-content terug te sturen voor consumptie op Apple iOS-native apparaten. Zie Inhoud [leveren](dynamic-packaging-overview.md)voor meer informatie.

### <a name="how-do-i-configure-media-reserved-units"></a>Hoe configureer ik mediagereserveerde eenheden?

Voor de taken audioanalyse en videoanalyse die worden geactiveerd door Media Services v3 of Video Indexer, raden we u aan uw account in te richten bij 10 S3 Media Reserved Units (MRU's). Als u meer dan 10 S3-MRU's nodig hebt, opent u een ondersteuningsticket met behulp van de [Azure-portal.](https://portal.azure.com/)

Zie [Mediaverwerking schalen](media-reserved-units-cli-how-to.md)voor meer informatie .

### <a name="what-is-the-recommended-method-to-process-videos"></a>Wat is de aanbevolen methode om video's te verwerken?

Gebruik [Transformaties](https://docs.microsoft.com/rest/api/media/transforms) om veelvoorkomende taken te configureren voor het coderen of analyseren van video's. Elke transformatie beschrijft een recept of een werkstroom met taken voor het verwerken van uw video- of audiobestanden. Een [taak](https://docs.microsoft.com/rest/api/media/jobs) is het werkelijke verzoek aan Media Services om de transformatie toe te passen op een invoervideo- of audio-inhoud. Nadat de transformatie is gemaakt, u Vacatures indienen met behulp van Media Services API's of een van de gepubliceerde SDK's. Zie [Transformeren en taken voor](transforms-jobs-concept.md)meer informatie.

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>Ik heb een video geüpload, gecodeerd en gepubliceerd. Waarom wordt de video niet afgespeeld als ik deze probeer te streamen?

Een van de meest voorkomende redenen is dat je niet het streaming eindpunt hebt van waaruit je probeert terug te spelen in de status Running.

### <a name="how-does-pagination-work"></a>Hoe werkt pagination?

Wanneer u paginatie gebruikt, moet u altijd de volgende koppeling gebruiken om de verzameling op te sommen en niet afhankelijk zijn van een bepaald paginaformaat. Zie [Filteren, bestellen, paging](entities-overview.md)voor meer informatie en voorbeelden.

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Welke functies zijn nog niet beschikbaar in Azure Media Services v3?

Zie [Functiehiaten met betrekking tot v2-API's voor](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis)meer informatie.

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Wat is het proces van het verplaatsen van een Media Services-account tussen abonnementen?  

Zie Een [Media Services-account verplaatsen tussen abonnementen voor](media-services-account-concept.md)meer informatie .

## <a name="live-streaming"></a>Live streamen 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>Hoe stop ik de live stream nadat de uitzending is gedaan?

U het benaderen vanaf de clientkant of de serverkant.

#### <a name="client-side"></a>Clientzijde

Uw webtoepassing moet de gebruiker vragen of hij of zij de uitzending wil beëindigen terwijl hij de browser sluit. Dit is een browsergebeurtenis die uw webtoepassing aankan.

#### <a name="server-side"></a>Serverzijde

U live gebeurtenissen controleren door u te abonneren op Azure Event Grid-gebeurtenissen. Zie het [eventschema EventGrid](media-services-event-schemas.md#live-event-types)voor meer informatie .

U hebt de volgende mogelijkheden:

* [Abonneer u op](reacting-to-media-services-events.md) de [microsoft.media.liveeventencoders](media-services-event-schemas.md#liveeventencoderdisconnected) op streamniveau en houd er een tijdje op dat er een tijdje geen nieuwe verbindingen binnenkomen om uw live-evenement te stoppen en te verwijderen.
* [Abonneer u op](reacting-to-media-services-events.md) de [heartbeatgebeurtenissen](media-services-event-schemas.md#liveeventingestheartbeat) op trackniveau. Als alle tracks een inkomende bitrate hebben die naar 0 daalt of als de laatste keer dat de stempel niet meer toeneemt, u het live-evenement veilig afsluiten. De hartslag gebeurtenissen komen in op elke 20 seconden voor elke track, dus het kan een beetje verbose.

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>Hoe voeg ik pauzes/video's en afbeeldingen in tijdens een livestream?

Media Services v3 live-codering biedt nog geen ondersteuning voor het invoegen van video- of beeldleien tijdens de livestream. 

U een [live on-premises encoder](recommended-on-premises-live-encoders.md) gebruiken om de bronvideo te schakelen. Veel apps bieden de mogelijkheid om van bron te wisselen, waaronder Telestream Wirecast, Switcher Studio (op iOS) en OBS Studio (gratis app).

## <a name="content-protection"></a>Inhoudsbeveiliging

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>Moet ik AES-128 clear key encryptie of een DRM-systeem gebruiken?

Klanten vragen zich vaak af of ze AES-encryptie of een DRM-systeem moeten gebruiken. Het belangrijkste verschil tussen de twee systemen is dat met AES-encryptie, de inhoudssleutel wordt verzonden naar de client via TLS, zodat de sleutel wordt versleuteld tijdens het transport, maar zonder extra encryptie ("in de duidelijke"). Als gevolg hiervan is de sleutel die wordt gebruikt om de inhoud te decoderen toegankelijk voor de clientspeler en kan deze in een netwerktracering op de client in platte tekst worden bekeken. AES-128 clear key encryption is geschikt voor use cases waarbij de viewer een vertrouwde partij is (bijvoorbeeld het versleutelen van bedrijfsvideo's die binnen een bedrijf worden gedistribueerd om door werknemers te worden bekeken).

DRM-systemen zoals PlayReady, Widevine en FairPlay bieden allemaal een extra niveau van versleuteling op de sleutel die wordt gebruikt om de inhoud te decoderen, vergeleken met een Duidelijke AES-128-sleutel. De inhoudssleutel wordt versleuteld naar een sleutel die wordt beschermd door de DRM-runtime, naast eventuele versleuteling op transportniveau die door TLS wordt geleverd. Bovendien, decryptie wordt behandeld in een veilige omgeving op het niveau van het besturingssysteem, waar het moeilijker is voor een kwaadwillende gebruiker aan te vallen. We raden DRM aan voor use cases waarbij de viewer mogelijk geen vertrouwde partij is en u het hoogste beveiligingsniveau nodig hebt.

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>Hoe toon ik een video alleen aan gebruikers die een specifieke toestemming hebben, zonder Azure AD te gebruiken?

U hoeft geen specifieke tokenprovider te gebruiken, zoals Azure Active Directory (Azure AD). U uw eigen [JWT-provider](https://jwt.io/) (de zogenaamde Secure Token Service, of STS) maken met behulp van asymmetrische sleutelversleuteling. In uw aangepaste STS u claims toevoegen op basis van uw bedrijfslogica.

Zorg ervoor dat de uitgever, doelgroep en claims allemaal exact overeenkomen tussen `ContentKeyPolicyRestriction` wat `ContentKeyPolicy`er in JWT zit en de waarde die wordt gebruikt in .

Zie [Uw inhoud beveiligen met dynamische versleuteling van Media Services](content-protection-overview.md)voor meer informatie.

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>Hoe en waar heb ik een JWT-token voordat ik het gebruik om een licentie of sleutel aan te vragen?

Voor de productie moet u Secure Token Service (dat wil zeggen een webservice) hebben, die een JWT-token uitgeeft op een HTTPS-verzoek. Voor de test u de `GetTokenAsync` code gebruiken die wordt weergegeven in de methode die is gedefinieerd in [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).

De speler doet een verzoek, nadat een gebruiker is geverifieerd, aan STS voor een dergelijk token en wijst het toe als de waarde van het token. U de [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)gebruiken.

Zie het [gereedschap JWT](https://aka.ms/jwt)voor een voorbeeld van het uitvoeren van STS met een symmetrische toets of een asymmetrische toets. Zie het [Azure mediatesthulpprogramma](https://aka.ms/amtest)voor een voorbeeld van een speler op basis van Azure Media Player die een dergelijk JWT-token gebruikt. (Vouw de **player_settings** koppeling uit om de tokeninvoer te bekijken.)

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>Hoe geef ik toestemming voor het streamen van video's met AES-versleuteling?

De juiste aanpak is het gebruik van Secure Token Service. Voeg in STS, afhankelijk van het gebruikersprofiel, verschillende claims toe (zoals 'Premium-gebruiker', 'Basisgebruiker', 'Gratis proefgebruiker'). Met verschillende claims in een JWT kan de gebruiker verschillende inhoud zien. Voor verschillende inhoud `ContentKeyPolicyRestriction` of activa, `RequiredClaims` zal de bijbehorende waarde hebben.

Azure Media Services API's gebruiken voor het configureren van licentie/sleutellevering en het versleutelen van uw assets (zoals in [dit voorbeeld wordt weergegeven).](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)

Zie voor meer informatie:

- [Overzicht van inhoudsbeveiliging](content-protection-overview.md)
- [Ontwerp van een inhoudsbeveiligingssysteem van een multi-DRM met toegangsbeheer](design-multi-drm-system-with-access-control.md)

### <a name="should-i-use-http-or-https"></a>Moet ik HTTP of HTTPS gebruiken?
De ASP.NET MVC-spelertoepassing moet het volgende ondersteunen:

* Gebruikersverificatie via Azure AD, dat onder HTTPS staat.
* JWT-uitwisseling tussen de client en Azure AD, die onder HTTPS staat.
* DRM-licentieverwerving door de klant, die onder HTTPS moet vallen als de licentielevering wordt geleverd door Media Services. De PlayReady-productsuite verplicht geen HTTPS voor licentielevering. Als uw PlayReady-licentieserver zich buiten Media Services bevindt, u HTTP of HTTPS gebruiken.

De ASP.NET spelertoepassing gebruikt HTTPS als best practice, dus Media Player staat op een pagina onder HTTPS. HTTP heeft echter de voorkeur voor streaming, dus je moet deze problemen met gemengde inhoud overwegen:

* De browser staat geen gemengde inhoud toe. Maar plug-ins zoals Silverlight en de OSMF plug-in voor Smooth en DASH laten het wel toe. Gemengde inhoud is een beveiligingsprobleem vanwege de dreiging van de mogelijkheid om kwaadaardige JavaScript te injecteren, waardoor klantgegevens in gevaar kunnen komen. Browsers blokkeren deze mogelijkheid standaard. De enige manier om er omheen te werken is aan de server (oorsprong) kant door het toestaan van alle domeinen (ongeacht HTTPS of HTTP). Dit is waarschijnlijk ook geen goed idee.
* Vermijd gemengde inhoud. Zowel de player-applicatie als Media Player moeten HTTP of HTTPS gebruiken. Wanneer je gemengde inhoud afspeelt, moet de SilverlightSS-technologie een waarschuwing voor gemengde inhoud wissen. De FlashSS-technologie verwerkt gemengde inhoud zonder waarschuwing voor gemengde inhoud.
* Als je streaming-eindpunt vóór augustus 2014 is gemaakt, wordt er geen https ondersteund. Maak en gebruik in dit geval een nieuw streamingeindpunt voor HTTPS.

### <a name="what-about-live-streaming"></a>Hoe zit het met live streaming?

U precies hetzelfde ontwerp en dezelfde implementatie gebruiken om live streaming in Media Services te beschermen door de asset die aan een programma is gekoppeld, als een VOD-asset te behandelen. Als u een multi-DRM-bescherming van de live-inhoud wilt bieden, past u dezelfde instelling/verwerking toe op het actief alsof het een VOD-asset is voordat u het actief koppelt aan de live-uitvoer.

### <a name="what-about-license-servers-outside-media-services"></a>Hoe zit het met licentieservers buiten Media Services?

Vaak hebben klanten geïnvesteerd in een licentieserverfarm in hun eigen datacenter of in een farm die wordt gehost door DRM-serviceproviders. Met mediaservices-inhoudsbescherming u in hybride modus werken. Inhoud kan worden gehost en dynamisch worden beschermd in Media Services, terwijl DRM-licenties worden geleverd door servers buiten Media Services. Houd in dit geval rekening met de volgende wijzigingen:

* STS moet tokens uitgeven die acceptabel zijn en kunnen worden geverifieerd door de licentieserverfarm. De Widevine-licentieservers van Axinom vereisen bijvoorbeeld een specifieke JWT die een rechtenbericht bevat. Je moet een STS hebben om zo'n JWT uit te geven. 
* U hoeft geen licentieleveringsservice meer te configureren in Media Services. U moet de URL's voor licentieverwerving (voor PlayReady, Widevine `ContentKeyPolicy`en FairPlay) verstrekken wanneer u dit configureert.

> [!NOTE]
> Widevine is een service van Google en onderworpen aan de servicevoorwaarden en het privacybeleid van Google.

## <a name="media-services-v2-vs-v3"></a>Media Services v2 vs. v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Kan ik de Azure-portal gebruiken om v3-resources te beheren?

Momenteel u de [Azure-portal](https://portal.azure.com/) gebruiken om:

* [Live-evenementen beheren](live-events-outputs-concept.md) in Media Services v3. 
* V3-activa weergeven [assets](assets-concept.md)(niet beheren). 
* [Informatie over het openen van API's](access-api-portal.md). 

Voor alle andere beheertaken (bijvoorbeeld [Transformaties en Taken](transforms-jobs-concept.md) en [inhoudsbescherming)](content-protection-overview.md)gebruikt u de [REST API,](https://docs.microsoft.com/rest/api/media/)de [Azure CLI](https://aka.ms/ams-v3-cli-ref)of een van de ondersteunde [SDK's](media-services-apis-overview.md#sdks).

### <a name="is-there-an-assetfile-concept-in-v3"></a>Is er een AssetFile concept in v3?

Het `AssetFile` concept is verwijderd uit de Media Services API om Media Services te scheiden van Storage SDK-afhankelijkheid. Nu houdt Azure Storage, niet Media Services, de informatie die thuishoort in de Storage SDK. 

Zie [Migreren naar Media Services v3](media-services-v2-vs-v3.md)voor meer informatie.

### <a name="where-did-client-side-storage-encryption-go"></a>Waar is client-side storage encryptie gebleven?

We raden u nu aan opslagversleuteling aan de serverzijde te gebruiken (die standaard is ingeschakeld). Zie [Azure Storage Service Encryption voor gegevens in rust](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)voor meer informatie.

## <a name="offline-streaming"></a>Offline streamen

### <a name="fairplay-streaming-for-ios"></a>FairPlay Streaming voor iOS

De volgende veelgestelde vragen helpen bij het oplossen van problemen met offline FairPlay-streaming voor iOS.

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>Waarom wordt alleen audio afgespeeld, maar geen video tijdens de offline modus?

Dit gedrag lijkt te zijn door het ontwerp van de steekproef app. Wanneer een alternatieve audiotrack aanwezig is (wat het geval is voor HLS) tijdens de offline modus, worden zowel iOS 10 als iOS 11 standaard weergegeven op het alternatieve audiospoor. Als u dit gedrag wilt compenseren voor de offlinefps-modus, verwijdert u het alternatieve audionummer uit de stream. Om dit te doen op Media Services, voegt u het dynamische manifestfilter **audio-only=false toe.** Met andere woorden, een HLS-URL eindigt met **.ism/manifest(format=m3u8-aapl,audio-only=false)**. 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>Waarom speelt het nog steeds audio alleen af zonder video tijdens de offline modus nadat ik audio-only=false heb toegevoegd?

Afhankelijk van het ontwerp van de cachesleutel voor het contentdeliverynetwerk kan de inhoud in de cache worden opgeslagen. Verwijder de cache.

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>Wordt de FPS offline modus ondersteund op iOS 11 naast iOS 10?

Ja. Fps offline modus wordt ondersteund voor iOS 10 en iOS 11.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>Waarom kan ik het document 'Offline afspelen met FairPlay Streaming en HTTP Live Streaming' niet vinden in de FPS Server SDK?

Sinds FPS Server SDK versie 4 is dit document samengevoegd tot de 'FairPlay Streaming Programming Guide'.

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>Wat is de gedownloade/offline bestandsstructuur op iOS-apparaten?

De gedownloade bestandsstructuur op een iOS-apparaat ziet eruit als de volgende schermafbeelding. De `_keys` map slaat gedownloade FPS-licenties op, met één winkelbestand voor elke licentieservicehost. De `.movpkg` map slaat audio- en video-inhoud op. 

De eerste map met een naam die eindigt met een streepje gevolgd door een nummer bevat video-inhoud. De numerieke waarde is de piekbandbreedte van de videoweergaven. De tweede map met een naam die eindigt met een streepje gevolgd door 0 bevat audio-inhoud. De derde `Data` map met de naam bevat de hoofdafspeellijst van de FPS-inhoud. Ten slotte biedt boot.xml een `.movpkg` volledige beschrijving van de inhoud van de map. 

![Offline bestandsstructuur voor de FairPlay iOS-voorbeeld-app](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Hier is een voorbeeld boot.xml bestand:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

### <a name="widevine-streaming-for-android"></a>Widevine-streaming voor Android

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>Hoe kan ik permanente licenties (offline ingeschakeld) leveren voor sommige clients/gebruikers en niet-permanente licenties (offline uitgeschakeld) voor anderen? Moet ik de inhoud dupliceren en afzonderlijke inhoudssleutels gebruiken?

Omdat Media Services v3 een `StreamingLocator` asset toestaat om meerdere exemplaren te hebben, u het:

* Een `ContentKeyPolicy` instantie `license_type = "persistent"` `ContentKeyPolicyRestriction` met , `"persistent"`met `StreamingLocator`vordering op , en haar .
* Een `ContentKeyPolicy` ander `license_type="nonpersistent"` `ContentKeyPolicyRestriction` geval met `"nonpersistent`, `StreamingLocator`met vordering op ", en zijn .
* Twee `StreamingLocator` instanties met `ContentKey` verschillende waarden.

Afhankelijk van de bedrijfslogica van aangepaste STS worden verschillende claims uitgegeven in het JWT-token. Met het token kan alleen de bijbehorende licentie worden verkregen en kan alleen de bijbehorende URL worden afgespeeld.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Wat is de mapping tussen de WIDEvine en Media Services DRM beveiligingsniveaus?

Google's "Widevine DRM Architecture Overview" definieert drie beveiligingsniveaus. De [Azure Media Services-documentatie op de Widevine-licentiesjabloon](widevine-license-template-overview.md) geeft echter vijf beveiligingsniveaus weer (vereisten voor robuustheid van clients voor afspelen). In dit gedeelte wordt uitgelegd hoe de beveiligingsniveaus worden toegewezen.

Beide sets beveiligingsniveaus worden gedefinieerd door Google Widevine. Het verschil zit hem in gebruiksniveau: architectuur of API. De vijf beveiligingsniveaus worden gebruikt in de Widevine API. Het `content_key_specs` object, `security_level`dat bestaat , wordt gedeserialiseerd en doorgegeven aan de widevine-wereldwijde leveringsservice door de Azure Media Services Widevine-licentieservice. In de volgende tabel wordt de toewijzing tussen de twee sets beveiligingsniveaus weergegeven.

| **Beveiligingsniveaus gedefinieerd in Widevine-architectuur** |**Beveiligingsniveaus die worden gebruikt in Widevine API**|
|---|---| 
| **Beveiligingsniveau 1:** Alle inhoudsverwerking, cryptografie en beheer worden uitgevoerd binnen de Trusted Execution Environment (TEE). In sommige implementatiemodellen kan beveiligingsverwerking worden uitgevoerd in verschillende chips.|**security_level=5**: De crypto, decodering en alle verwerking van de media (gecomprimeerd en ongecomprimeerd) moeten worden afgehandeld binnen een tee met hardwareondersteuning.<br/><br/>**security_level=4**: De crypto en decodering van inhoud moeten worden uitgevoerd binnen een door hardware gesteunde TEE.|
**Beveiligingsniveau 2**: Cryptografie (maar geen videoverwerking) wordt uitgevoerd binnen de TEE. Gedecodeerde buffers worden teruggestuurd naar het toepassingsdomein en verwerkt via afzonderlijke videohardware of -software. Op niveau 2 wordt cryptografische informatie echter nog steeds alleen binnen de TEE verwerkt.| **security_level=3**: Het belangrijkste materiaal en crypto-bewerkingen moeten worden uitgevoerd binnen een tee met hardwareondersteuning. |
| **Beveiligingsniveau 3**: Er is geen TEE op het apparaat. Er kunnen passende maatregelen worden genomen om de cryptografische informatie en gedecodeerde inhoud op het hostbesturingssysteem te beschermen. Een level 3-implementatie kan ook een cryptografische hardware-engine bevatten, maar dat verbetert alleen de prestaties, niet de beveiliging. | **security_level=2**: Software crypto en een versluierde decoder zijn vereist.<br/><br/>**security_level=1**: Software-based white-box crypto is vereist.|

#### <a name="why-does-content-download-take-so-long"></a>Waarom duurt het downloaden van inhoud zo lang?

Er zijn twee manieren om de downloadsnelheid te verbeteren:

* Schakel een netwerk voor contentlevering in, zodat gebruikers eerder dat raken in plaats van het origin/streaming-eindpunt voor het downloaden van inhoud. Als een gebruiker een streaming-eindpunt raakt, wordt elk HLS-segment of DASH-fragment dynamisch verpakt en versleuteld. Hoewel deze latentie is in milliseconde schaal voor elk segment of fragment, wanneer u een uur durende video, de geaccumuleerde latentie kan groot zijn en leiden tot een langere download.
* Geef gebruikers de mogelijkheid om selectief lagen en audiotracks van videokwaliteit te downloaden in plaats van alle inhoud. Voor offline modus heeft het geen zin om alle kwaliteitslagen te downloaden. Er zijn twee manieren om dit te bereiken:

  * Clientgecontroleerd: de speler-app selecteert automatisch, of de gebruiker selecteert, de videokwaliteit laag en de audio tracks te downloaden.
  * Servicegestuurd: U de functie Dynamisch manifest in Azure Media Services gebruiken om een (globaal) filter te maken, dat HLS-afspeellijst of DASH MPD beperkt tot één laag met videokwaliteit en geselecteerde audiotracks. Dan is de download URL gepresenteerd aan gebruikers zal dit filter bevatten.

## <a name="next-steps"></a>Volgende stappen

[Media Services v3 overzicht](media-services-overview.md)
