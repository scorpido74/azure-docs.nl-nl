---
title: Widevine Android offline streamen met Azure Media Services v3
description: In dit onderwerp wordt uitgelegd hoe u uw Azure Media Services-account configureert voor offline streaming van widevine-beveiligde inhoud.
services: media-services
keywords: DASH, DRM, Widevine Offline Mode, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: 64cd93acc78f4cb5b7ebc4266e7359aec662890c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295426"
---
# <a name="offline-widevine-streaming-for-android-with-media-services-v3"></a>Offline Widevine-streaming voor Android met Media Services v3

Naast het beschermen van content voor online streaming, bieden abonnementen op media-inhoud en verhuurdiensten downloadbare content die werkt wanneer u niet met internet bent verbonden. Mogelijk moet u inhoud downloaden op uw telefoon of tablet om af te spelen in vliegtuigmodus wanneer u vliegt, losgekoppeld van het netwerk. Aanvullende scenario's, waarin u inhoud zou willen downloaden:

- Sommige aanbieders van inhoud kunnen de levering van DRM-licenties buiten de grens van een land/regio weigeren. Als een gebruiker inhoud wil bekijken terwijl hij naar het buitenland reist, is offline downloaden nodig.
- In sommige landen/regio's is de beschikbaarheid en/of bandbreedte op internet beperkt. Gebruikers kunnen ervoor kiezen om inhoud te downloaden om te kunnen bekijken in een hoge resolutie voor een bevredigende kijkervaring.

In dit artikel wordt besproken hoe u offlinemodus weergave implementeert voor DASH-inhoud die wordt beschermd door Widevine op Android-apparaten. Offline DRM stelt u in staat om abonnements-, huur- en aankoopmodellen voor uw inhoud aan te bieden, zodat klanten van uw services eenvoudig inhoud mee kunnen nemen wanneer deze niet van internet zijn gekoppeld.

Voor het bouwen van de Android-speler-apps geven we een overzicht van drie opties:

> [!div class="checklist"]
> * Een speler bouwen met de Java API van ExoPlayer SDK
> * Een speler bouwen met Xamarin binding van ExoPlayer SDK
> * Een speler bouwen met Behulp van Encrypted Media Extension (EME) en Media Source Extension (MSE) in Chrome mobiele browser v62 of hoger

Het artikel beantwoordt ook een aantal veelgestelde vragen met betrekking tot offline streaming van widevine beveiligde inhoud.

> [!NOTE]
> Offline DRM wordt alleen gefactureerd voor het indienen van één aanvraag voor een licentie wanneer u de inhoud downloadt. Eventuele fouten worden niet gefactureerd.

## <a name="prerequisites"></a>Vereisten 

Voordat u offline DRM voor Widevine op Android-apparaten implementeert, moet u eerst het andere doen:

- Maak kennis met de concepten die zijn geïntroduceerd voor online contentbescherming met Widevine DRM. Dit wordt in detail behandeld in de volgende documenten/monsters:
    - [Ontwerp van een inhoudsbeveiligingssysteem van een multi-DRM met toegangsbeheer](design-multi-drm-system-with-access-control.md)
    - [De Digital Rights Management-service gebruiken voor dynamische versleuteling en licentielevering](protect-with-drm.md)
- Kloon. https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git

    U moet de code in [Versleutelen met DRM wijzigen met .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) om Widevine-configuraties toe te voegen.  
- Maak kennis met de Google ExoPlayer SDK voor Android, een open-source videospeler SDK die offline Widevine DRM-weergave kan ondersteunen. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [ExoPlayer-ontwikkelaarsgids](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer Developer Blog](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>Inhoudsbeveiliging configureren in Azure Media Services

In de [getorcreatecontentkeybeleidsasync-methode](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) zijn de volgende noodzakelijke stappen aanwezig:

1. Geef op hoe de levering van inhoudssleutels is geautoriseerd in de licentieservice: 

    ```csharp
    ContentKeyPolicySymmetricTokenKey primaryKey = new ContentKeyPolicySymmetricTokenKey(tokenSigningKey);
    List<ContentKeyPolicyTokenClaim> requiredClaims = new List<ContentKeyPolicyTokenClaim>()
    {
        ContentKeyPolicyTokenClaim.ContentKeyIdentifierClaim
    };
    List<ContentKeyPolicyRestrictionTokenKey> alternateKeys = null;
    ContentKeyPolicyTokenRestriction restriction 
        = new ContentKeyPolicyTokenRestriction(Issuer, Audience, primaryKey, ContentKeyPolicyRestrictionTokenType.Jwt, alternateKeys, requiredClaims);
    ```
2. Widevine-licentiesjabloon configureren:  

    ```csharp
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ```

3. ContentKeyPolicyOptions maken:

    ```csharp
    options.Add(
        new ContentKeyPolicyOption()
        {
            Configuration = widevineConfig,
            Restriction = restriction
        });
    ```

## <a name="enable-offline-mode"></a>Offlinemodus inschakelen

Als u **de offlinemodus** voor Widevine-licenties wilt inschakelen, moet u [de widevine-licentiesjabloon](widevine-license-template-overview.md)configureren. Stel in het **object policy_overrides** de **eigenschap can_persist** in op **true** (standaard is false), zoals weergegeven in [ConfigureWidevineLicenseTempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563). 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>De Android-speler configureren voor offline afspelen

De eenvoudigste manier om een native player-app voor Android-apparaten te ontwikkelen, is door de [Google ExoPlayer SDK](https://github.com/google/ExoPlayer), een open-source videospeler SDK, te gebruiken. ExoPlayer ondersteunt functies die momenteel niet worden ondersteund door de native MediaPlayer API van Android, waaronder MPEG-DASH en Microsoft Smooth Streaming-leveringsprotocollen.

ExoPlayer versie 2.6 en hoger bevat veel klassen die offline Widevine DRM-weergave ondersteunen. In het bijzonder biedt de klasse OfflineLicenseHelper hulpprogrammafuncties om het gebruik van de DefaultDrmSessionManager voor het downloaden, vernieuwen en vrijgeven van offline licenties te vergemakkelijken. De lessen in de SDK-map "library/core/src/main/java/com/google/android/exoplayer2/offline/" ondersteunen offline videocontent downloaden.

De volgende lijst met klassen vergemakkelijkt de offline modus in de ExoPlayer SDK voor Android:

- library/core/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java 
- library/core/src/main/java/com/google/android/exoplayer2/offline/Downloader.java
- library/dash/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

Ontwikkelaars moeten verwijzen naar de [ExoPlayer Developer Guide](https://google.github.io/ExoPlayer/guide.html) en de bijbehorende [Developer Blog](https://medium.com/google-exoplayer) tijdens de ontwikkeling van een applicatie. Google heeft op dit moment nog geen volledig gedocumenteerde referentie-implementatie of voorbeeldcode uitgebracht voor de ExoPlayer-app die Widevine offline ondersteunt, dus de informatie is beperkt tot de handleiding en blog van de ontwikkelaars. 

### <a name="working-with-older-android-devices"></a>Werken met oudere Android-apparaten

Voor sommige oudere Android-apparaten moet u waarden instellen voor de volgende **policy_overrides** eigenschappen (gedefinieerd in [de licentiesjabloon Widevine:](widevine-license-template-overview.md) **rental_duration_seconds,** **playback_duration_seconds**en **license_duration_seconds**. U ze ook op nul instellen, wat oneindige/onbeperkte duur betekent.  

De waarden moeten worden ingesteld om een integer overflow bug te voorkomen. Voor meer uitleg over https://github.com/google/ExoPlayer/issues/3150 het https://github.com/google/ExoPlayer/issues/3112probleem, zie en . <br/>Als u de waarden niet expliciet instelt, worden zeer grote waarden voor **PlaybackDurationRemaining** en **LicenseDurationRemaining** toegewezen (bijvoorbeeld 9223372036854775807, de maximale positieve waarde voor een geheel getal van 64 bits). Als gevolg hiervan lijkt de Widevine-licentie verlopen en dus zal de decryptie niet gebeuren. 

Dit probleem doet zich niet voor op Android 5.0 Lollipop of hoger, aangezien Android 5.0 de eerste Android-versie is, die is ontworpen om ARMv8[(Advanced RISC Machine)](https://en.wikipedia.org/wiki/ARM_architecture)en 64-bits platforms volledig te ondersteunen, terwijl Android 4.4 KitKat oorspronkelijk is ontworpen om ARMv7- en 32-bits platforms te ondersteunen, net als bij andere oudere Android-versies.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Xamarin gebruiken om een Android-afspeel-app te bouwen

Xamarin bindingen voor ExoPlayer vind je via de volgende links:

- [Xamarin bindingen bibliotheek voor de Google ExoPlayer bibliotheek](https://github.com/martijn00/ExoPlayerXamarin)
- [Xamarin bindingen voor ExoPlayer NuGet](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Zie ook de volgende thread: [Xamarin binding](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Chrome-apps voor Android

Te beginnen met de release van [Chrome voor Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates), permanente licentie in EME wordt ondersteund. [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) wordt nu ook ondersteund in Chrome voor Android. Hiermee u offline afspeeltoepassingen maken in Chrome als uw eindgebruikers deze (of hogere) versie van Chrome hebben. 

Daarnaast heeft Google een Progressive Web App (PWA) sample en open-sourced het: 

- [Broncode](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Door Google gehoste versie](https://biograf-155113.appspot.com/ttt/episode-2/) (werkt alleen in Chrome v 62 en hoger op Android-apparaten)

Als u uw mobiele Chrome-browser upgradet naar v62 (of hoger) op een Android-telefoon en de hierboven gehoste voorbeeld-app test, ziet u dat zowel online streaming als offline afspelen werken.

De bovenstaande open-source PWA-app is geschreven in Node.js. Als u uw eigen versie op een Ubuntu-server wilt hosten, moet u rekening houden met de volgende veelvoorkomende problemen die afspelen kunnen voorkomen:

1. CORS-probleem: de voorbeeldvideo in de https://storage.googleapis.com/biograf-video-files/videos/voorbeeld-app wordt gehost in . Google heeft CORS ingesteld voor al hun testvoorbeelden die worden gehost in de bucket Van Google Cloud Storage. Ze worden geserveerd met CORS-headers, waarin expliciet `https://biograf-155113.appspot.com` de CORS-vermelding wordt opgegeven: (het domein waarin google hun voorbeeld host) waardoor de toegang door andere sites wordt voorkomen. Als u het probeert, ziet u de volgende HTTP-fout:`Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https:\//13.85.80.81:8080' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.`
2. Certificaatprobleem: Vanaf Chrome v 58 heeft EME voor Widevine HTTPS nodig. Daarom moet u de voorbeeld-app via HTTPS hosten met een X509-certificaat. Een gebruikelijk testcertificaat werkt niet vanwege de volgende vereisten: U moet een certificaat verkrijgen dat voldoet aan de volgende minimumvereisten:
    - Chrome en Firefox vereisen dat de instelling Alternatieve naam van SAN-onderwerp in het certificaat bestaat
    - Het certificaat moet een vertrouwde CA hebben en een zelfondertekend ontwikkelingscertificaat werkt niet
    - Het certificaat moet een CN hebben die overeenkomt met de DNS-naam van de webserver of gateway

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="question"></a>Vraag

Hoe kan ik permanente licenties (offline ingeschakeld) leveren voor sommige clients/gebruikers en niet-permanente licenties (offline uitgeschakeld) voor anderen? Moet ik de inhoud dupliceren en een aparte inhoudssleutel gebruiken?

### <a name="answer"></a>Antwoord
Sinds Media Services v3 kan een asset meerdere StreamingLocators hebben. Je.

1.    Eén ContentKeyPolicy met license_type = "persistent", ContentKeyPolicyRestriction met claim op "persistent" en de StreamingLocator;
2.    Een ander ContentKeyPolicy met license_type="niet-persistent", ContentKeyPolicyRestriction met claim op "niet-persistent", en de StreamingLocator.
3.    De twee StreamingLocators hebben verschillende ContentKey.

Afhankelijk van de bedrijfslogica van aangepaste STS worden verschillende claims uitgegeven in het JWT-token. Met het token kan alleen de bijbehorende licentie worden verkregen en kan alleen de bijbehorende URL worden afgespeeld.

### <a name="question"></a>Vraag

Voor Widevine-beveiligingsniveaus definieert het document 'Widevine DRM Architecture Overview' van Google drie verschillende beveiligingsniveaus. In [Azure Media Services-documentatie over widevine-licentiesjabloon](widevine-license-template-overview.md)worden echter vijf verschillende beveiligingsniveaus beschreven. Wat is de relatie of mapping tussen de twee verschillende sets van beveiligingsniveaus?

### <a name="answer"></a>Antwoord

In het document 'Widevine DRM Architecture Review' van Google worden de volgende drie beveiligingsniveaus gedefinieerd:

1.  Beveiligingsniveau 1: Alle inhoudsverwerking, cryptografie en beheer worden uitgevoerd binnen de Trusted Execution Environment (TEE). In sommige implementatiemodellen kan beveiligingsverwerking worden uitgevoerd in verschillende chips.
2.  Beveiligingsniveau 2: Voert cryptografie uit (maar geen videoverwerking) binnen de TEE: gedecodeerde buffers worden teruggestuurd naar het toepassingsdomein en verwerkt via afzonderlijke videohardware of -software. Op niveau 2 wordt cryptografische informatie echter nog steeds alleen binnen de TEE verwerkt.
3.  Beveiligingsniveau 3 Heeft geen TEE op het apparaat. Er kunnen passende maatregelen worden genomen om de cryptografische informatie en gedecodeerde inhoud op het hostbesturingssysteem te beschermen. Een Level 3-implementatie kan ook een cryptografische hardware-engine bevatten, maar dat verbetert alleen de prestaties, niet de beveiliging.

Tegelijkertijd kan in [Azure Media Services-documentatie over widevine-licentiesjabloon](widevine-license-template-overview.md)de security_level eigenschap van content_key_specs de volgende vijf verschillende waarden hebben (vereisten voor robuustheid van clients voor afspelen):

1.  Software-gebaseerde white-box crypto is vereist.
2.  Software crypto en een versluierde decoder is vereist.
3.  Het belangrijkste materiaal en crypto-bewerkingen moeten worden uitgevoerd binnen een tee met hardwareondersteund.
4.  De crypto en decodering van inhoud moeten worden uitgevoerd binnen een hardware ondersteund TEE.
5.  De crypto, decodering en alle verwerking van de media (gecomprimeerd en ongecomprimeerd) moeten worden afgehandeld binnen een hardware backed TEE.

Beide beveiligingsniveaus worden gedefinieerd door Google Widevine. Het verschil zit hem in het gebruiksniveau: architectuurniveau of API-niveau. De vijf beveiligingsniveaus worden gebruikt in de Widevine API. Het content_key_specs object, dat security_level bevat, wordt gedeserialiseerd en doorgegeven aan de widevine-wereldwijde leveringsservice door Azure Media Services Widevine-licentieservice. De onderstaande tabel toont de toewijzing tussen de twee sets beveiligingsniveaus.

| **Beveiligingsniveaus gedefinieerd in Widevine-architectuur** |**Beveiligingsniveaus die worden gebruikt in Widevine API**|
|---|---| 
| **Beveiligingsniveau 1:** Alle inhoudsverwerking, cryptografie en beheer worden uitgevoerd binnen de Trusted Execution Environment (TEE). In sommige implementatiemodellen kan beveiligingsverwerking worden uitgevoerd in verschillende chips.|**security_level=5**: De crypto, decodering en alle verwerking van de media (gecomprimeerd en ongecomprimeerd) moeten worden afgehandeld binnen een door hardware gesteunde TEE.<br/><br/>**security_level=4**: De crypto en decodering van inhoud moeten worden uitgevoerd binnen een tee met hardwareondersteund.|
**Beveiligingsniveau 2**: Voert cryptografie uit (maar geen videoverwerking) binnen de TEE: gedecodeerde buffers worden teruggestuurd naar het toepassingsdomein en verwerkt via afzonderlijke videohardware of -software. Op niveau 2 wordt cryptografische informatie echter nog steeds alleen binnen de TEE verwerkt.| **security_level=3**: Het belangrijkste materiaal en crypto-bewerkingen moeten worden uitgevoerd binnen een tee met hardwareondersteuning. |
| **Beveiligingsniveau 3**: Heeft geen TEE op het apparaat. Er kunnen passende maatregelen worden genomen om de cryptografische informatie en gedecodeerde inhoud op het hostbesturingssysteem te beschermen. Een Level 3-implementatie kan ook een cryptografische hardware-engine bevatten, maar dat verbetert alleen de prestaties, niet de beveiliging. | **security_level=2**: Software crypto en een versluierde decoder zijn vereist.<br/><br/>**security_level=1**: Softwaregebaseerde whitebox crypto is vereist.|

### <a name="question"></a>Vraag

Waarom duurt het downloaden van inhoud zo lang?

### <a name="answer"></a>Antwoord

Er zijn twee manieren om de downloadsnelheid te verbeteren:

1.  Schakel CDN in, zodat eindgebruikers eerder cdn raken in plaats van origin/streaming endpoint voor het downloaden van inhoud. Als de gebruiker streaming eindpunt raakt, wordt elk HLS-segment of DASH-fragment dynamisch verpakt en versleuteld. Hoewel deze latentie is in milliseconde schaal voor elk segment / fragment, wanneer u een uur lang video, de geaccumuleerde latentie kan groot zijn waardoor langere download.
2.  Geef eindgebruikers de mogelijkheid om lagen en audiotracks van videokwaliteit selectief te downloaden in plaats van alle inhoud. Voor de offline modus heeft het geen zin om alle kwaliteitslagen te downloaden. Er zijn twee manieren om dit te bereiken:
    1.  Client gecontroleerd: ofwel speler app automatisch selecteert of gebruiker selecteert video kwaliteit laag en audio tracks te downloaden;
    2.  Servicegestuurd: men kan de Dynamic Manifest-functie in Azure Media Services gebruiken om een (globaal) filter te maken, dat HLS-afspeellijst of DASH MPD beperkt tot één laag met videokwaliteit en geselecteerde audiotracks. Dan is de download URL gepresenteerd aan eindgebruikers zal dit filter bevatten.

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="summary"></a>Samenvatting

In dit artikel wordt besproken hoe u offline modus weergave implementeert voor DASH-inhoud die wordt beschermd door Widevine op Android-apparaten.  Het beantwoordde ook een aantal veelgestelde vragen met betrekking tot offline streaming van Widevine beschermde inhoud.
