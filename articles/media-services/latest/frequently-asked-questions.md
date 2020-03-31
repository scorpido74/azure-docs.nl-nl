---
title: Azure Media Services v3 veelgestelde vragen| Microsoft Documenten
description: In dit artikel vindt u antwoorden op veelgestelde vragen van Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/18/2020
ms.author: juliako
ms.openlocfilehash: 11123ee04dd02a60dff0b88e2e6e85fcd613a7d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067997"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Media Services v3 veelgestelde vragen

In dit artikel vindt u antwoorden op veelgestelde vragen van Azure Media Services (AMS).

## <a name="general"></a>Algemeen

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Welke Azure-rollen kunnen acties uitvoeren op Azure Media Services-bronnen? 

Zie [RBAC (Role-based access control) voor Media Services-accounts](rbac-overview.md).

### <a name="how-do-you-stream-to-apple-ios-devices"></a>Hoe stream je naar Apple iOS-apparaten?

Zorg ervoor dat je aan het einde van je pad (na het "/manifest"-gedeelte van de URL) hebt "(format=m3u8-aapl)" om de streaming origin-server te vertellen hls-inhoud terug te sturen voor consumptie op apple iOS-native apparaten (zie inhoud leveren voor meer [informatie).](dynamic-packaging-overview.md)

### <a name="how-do-i-configure-media-reserved-units"></a>Hoe configureer ik mediagereserveerde eenheden?

Voor de analysetaken van audio en video die worden geactiveerd door Media Services v3 of Video Indexer is het raadzaam om uw account in te richten met 10 S3 MRU’s. Als u meer dan 10 S3-MRU's nodig hebt, opent u een ondersteuningsticket via de [Azure-portal.](https://portal.azure.com/)

Zie [Mediaverwerking schalen met CLI](media-reserved-units-cli-how-to.md)voor meer informatie.

### <a name="what-is-the-recommended-method-to-process-videos"></a>Wat is de aanbevolen methode om video's te verwerken?

Gebruik [Transformaties](https://docs.microsoft.com/rest/api/media/transforms) om veelvoorkomende taken te configureren voor het coderen of analyseren van video's. Elke **transformatie** beschrijft een recept of een werkstroom met taken voor het verwerken van uw video- of audiobestanden. Een [taak](https://docs.microsoft.com/rest/api/media/jobs) is het feitelijke verzoek aan Media Services om de **transformatie** toe te passen op een bepaalde invoervideo of audio-inhoud. Zodra de transformatie is gemaakt, u vacatures indienen met behulp van Media Services API's of een van de gepubliceerde SDK's. Zie [Transformeren en taken voor](transforms-jobs-concept.md)meer informatie.

### <a name="i-uploaded-encoded-and-published-a-video-what-would-be-the-reason-the-video-does-not-play-when-i-try-to-stream-it"></a>Ik heb een video geüpload, gecodeerd en gepubliceerd. Wat zou de reden zijn dat de video niet wordt afgespeeld wanneer ik het probeer te streamen?

Een van de meest voorkomende redenen is dat u niet beschikt over de streaming eindpunt van waaruit u probeert terug te spelen in de status Running.

### <a name="how-does-pagination-work"></a>Hoe werkt pagination?

Wanneer u paginatie gebruikt, moet u altijd de volgende koppeling gebruiken om de verzameling op te sommen en niet afhankelijk zijn van een bepaald paginaformaat. Zie [Filteren, bestellen, paging](entities-overview.md)voor meer informatie en voorbeelden.

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Welke functies zijn nog niet beschikbaar in Azure Media Services v3?

Zie voor meer informatie [functiehiaten met betrekking tot v2-API's](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Wat is het proces van het verplaatsen van een Media Services-account tussen abonnementen?  

Zie Een [Media Services-account verplaatsen tussen abonnementen voor](media-services-account-concept.md)meer informatie .

## <a name="live-streaming"></a>Live streamen 

### <a name="how-to-stop-the-live-stream-after-the-broadcast-is-done"></a>Hoe de live stream te stoppen na de uitzending is gedaan?

U het benaderen vanaf een clientkant of een serverkant.

#### <a name="client-side"></a>Clientzijde

Uw webtoepassing moet de gebruiker vragen of hij de uitzending wil beëindigen als deze de browser sluit. Dit is een browsergebeurtenis die uw webtoepassing aankan.

#### <a name="server-side"></a>Serverzijde

Je live-evenementen volgen door je te abonneren op Event Grid-evenementen. Zie het [eventgrid-gebeurtenisschema voor](media-services-event-schemas.md#live-event-types)meer informatie .

* U [zich abonneren op](reacting-to-media-services-events.md) het streamniveau [Microsoft.Media.LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) en controleren of er een tijdje geen nieuwe verbindingen binnenkomen om uw live-evenement te stoppen en te verwijderen.
* U [zich ook abonneren op](reacting-to-media-services-events.md) [heartbeatgebeurtenissen](media-services-event-schemas.md#liveeventingestheartbeat) op het niveau van het trackniveau. Als alle tracks hebben inkomende bitrate daalt tot 0; of de laatste tijdstempel neemt niet meer toe, dan u ook veilig afsluiten van de live-evenement. De hartslag gebeurtenissen komen in op elke 20 seconden voor elke track dus het kan een beetje verbose.

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Hoe invoegen pauzes / video's en beeldleien tijdens live stream?

Media Services v3 live-codering biedt nog geen ondersteuning voor het invoegen van video- of beeldleien tijdens de livestream. 

U een [live on-premises encoder](recommended-on-premises-live-encoders.md) gebruiken om de bronvideo te schakelen. Veel apps bieden de mogelijkheid om van bron te wisselen, waaronder Telestream Wirecast, Switcher Studio (op iOS), OBS Studio (gratis app) en nog veel meer.

## <a name="content-protection"></a>Inhoudsbeveiliging

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>Moet ik een AES-128 clear key encryptie of een DRM-systeem gebruiken?

Klanten vragen zich vaak af of ze AES-encryptie of een DRM-systeem moeten gebruiken. Het belangrijkste verschil tussen de twee systemen is dat met AES-encryptie de inhoudssleutel wordt verzonden naar de client via TLS, zodat de sleutel wordt versleuteld tijdens het transport, maar zonder extra encryptie ("in de duidelijke"). Als gevolg hiervan is de sleutel die wordt gebruikt om de inhoud te decoderen toegankelijk voor de clientspeler en kan deze in een netwerktracering op de client in platte tekst worden bekeken. Een AES-128 clear key encryptie is geschikt voor use cases waarbij de viewer een vertrouwde partij is (bijvoorbeeld het versleutelen van bedrijfsvideo's die binnen een bedrijf worden gedistribueerd om door werknemers te worden bekeken).

DRM-systemen zoals PlayReady, Widevine en FairPlay bieden allemaal een extra niveau van versleuteling op de sleutel die wordt gebruikt om de inhoud te decoderen in vergelijking met een duidelijke aes-128-sleutel. De inhoudssleutel wordt versleuteld naar een sleutel die wordt beschermd door de DRM-runtime, naast eventuele versleuteling op transportniveau die door TLS wordt geleverd. Bovendien, decryptie wordt behandeld in een veilige omgeving op het niveau van het besturingssysteem, waar het moeilijker is voor een kwaadwillende gebruiker aan te vallen. DRM wordt aanbevolen voor use cases waarbij de viewer mogelijk geen vertrouwde partij is en u het hoogste beveiligingsniveau nodig hebt.

### <a name="how-to-show-a-video-only-to-users-who-have-a-specific-permission-without-using-azure-ad"></a>Hoe u een video alleen weergeven aan gebruikers die een specifieke toestemming hebben, zonder Azure AD te gebruiken?

U hoeft geen specifieke tokenprovider (zoals Azure AD) te gebruiken. U uw eigen [JWT-provider](https://jwt.io/) (de zogenaamde STS, Secure Token Service) maken met behulp van asymmetrische sleutelversleuteling. In uw aangepaste STS u claims toevoegen op basis van uw bedrijfslogica.

Zorg ervoor dat de uitgever, het publiek en de claims allemaal exact overeenkomen tussen wat er in JWT staat en de ContentKeyPolicyRestriction die in ContentKeyPolicy wordt gebruikt.

Zie [Uw inhoud beveiligen met dynamische versleuteling van Media Services](content-protection-overview.md)voor meer informatie.

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Hoe en waar jwt-token te krijgen voordat u het gebruikt om een licentie of sleutel aan te vragen?

1. Voor de productie moet u een Secure Token Services (STS) (webservice) hebben die JWT-token uitgeeft op een HTTPS-verzoek. Voor de test u de code gebruiken die wordt weergegeven in de **GetTokenAsync-methode** die is gedefinieerd in [Program.cs.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)
2. Speler moet een verzoek indienen, nadat een gebruiker is geverifieerd, aan de STS voor een dergelijk token en het toewijzen als de waarde van het token. U de [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)gebruiken.

* Voor een voorbeeld van het uitvoeren van STS, met [https://aka.ms/jwt](https://aka.ms/jwt)ofwel symmetrische en asymmetrische sleutel, zie . 
* Zie [https://aka.ms/amtest](https://aka.ms/amtest) (player_settings" koppeling uitvouwen om de tokeninvoer te zien voor een voorbeeld van een speler op basis van Azure Media Player met behulp van een dergelijk JWT-token.

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Hoe autoriseer je verzoeken om video's te streamen met AES-encryptie?

De juiste aanpak is om sts (Secure Token Service) te gebruiken:

Voeg in STS, afhankelijk van het gebruikersprofiel, verschillende claims toe (zoals 'Premium-gebruiker', 'Basisgebruiker', 'Gratis proefgebruiker'). Met verschillende claims in een JWT kan de gebruiker verschillende inhoud zien. Natuurlijk, voor verschillende inhoud / asset, de ContentKeyPolicyRestriction zal de bijbehorende RequiredClaims.

Azure Media Services API's gebruiken voor het configureren van licentie/sleutellevering en het versleutelen van uw assets (zoals in [dit voorbeeld wordt weergegeven).](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)

Zie voor meer informatie:

- [Overzicht van inhoudsbeveiliging](content-protection-overview.md)
- [Ontwerp van een inhoudsbeveiligingssysteem van een multi-DRM met toegangsbeheer](design-multi-drm-system-with-access-control.md)

### <a name="http-or-https"></a>HTTP of HTTPS?
De ASP.NET MVC-spelertoepassing moet het volgende ondersteunen:

* Gebruikersverificatie via Azure AD, dat onder HTTPS staat.
* JWT-uitwisseling tussen de client en Azure AD, die onder HTTPS staat.
* DRM-licentieverwerving door de klant, die onder HTTPS moet vallen als de licentielevering wordt geleverd door Media Services. De PlayReady-productsuite verplicht geen HTTPS voor licentielevering. Als uw PlayReady-licentieserver zich buiten Media Services bevindt, u HTTP of HTTPS gebruiken.

De ASP.NET spelertoepassing gebruikt HTTPS als best practice, dus Media Player staat op een pagina onder HTTPS. Http heeft echter de voorkeur voor streaming, dus je moet rekening houden met het probleem van gemengde inhoud.

* De browser staat geen gemengde inhoud toe. Maar plug-ins zoals Silverlight en de OSMF plug-in voor smooth en DASH laten het toe. Gemengde inhoud is een beveiligingsprobleem vanwege de dreiging van de mogelijkheid om kwaadaardige JavaScript te injecteren, waardoor klantgegevens in gevaar kunnen komen. Browsers blokkeren deze mogelijkheid standaard. De enige manier om er omheen te werken is aan de server (oorsprong) kant door het toestaan van alle domeinen (ongeacht HTTPS of HTTP). Dit is waarschijnlijk ook geen goed idee.
* Vermijd gemengde inhoud. Zowel de player-applicatie als Media Player moeten HTTP of HTTPS gebruiken. Bij het afspelen van gemengde inhoud, de silverlightSS tech vereist clearing een mixed-content waarschuwing. De flashSS-technologie verwerkt gemengde inhoud zonder waarschuwing voor gemengde inhoud.
* Als je streaming-eindpunt vóór augustus 2014 is gemaakt, wordt er geen https ondersteund. Maak en gebruik in dit geval een nieuw streamingeindpunt voor HTTPS.

### <a name="what-about-live-streaming"></a>Hoe zit het met live streaming?

U precies hetzelfde ontwerp en dezelfde implementatie gebruiken om live streaming in Media Services te beschermen door het item dat aan een programma is gekoppeld, als een VOD-asset te behandelen. Als u een multi-DRM-bescherming van de live-inhoud wilt bieden, past u dezelfde instelling/verwerking toe op het actief alsof het een VOD-asset is voordat u het actief koppelt aan de live-uitvoer.

### <a name="what-about-license-servers-outside-media-services"></a>Hoe zit het met licentieservers buiten Media Services?

Vaak hebben klanten geïnvesteerd in een licentieserverfarm in hun eigen datacenter of een bedrijf dat wordt gehost door DRM-serviceproviders. Met mediaservices-inhoudsbescherming u in hybride modus werken. Inhoud kan worden gehost en dynamisch worden beveiligd in Media Services, terwijl DRM-licenties worden geleverd door servers buiten Media Services. Houd in dit geval rekening met de volgende wijzigingen:

* STS moet tokens uitgeven die acceptabel zijn en kunnen worden geverifieerd door de licentieserverfarm. De Widevine-licentieservers van Axinom vereisen bijvoorbeeld een specifieke JWT die een rechtenbericht bevat. Daarom moet je een STS hebben om zo'n JWT uit te geven. 
* U hoeft geen licentieleveringsservice meer te configureren in Media Services. U moet de URL's voor licentieverwerving (voor PlayReady, Widevine en FairPlay) verstrekken wanneer u ContentKeyPolicies configureert.

> [!NOTE]
> Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="media-services-v2-vs-v3"></a>Media Services v2 vs v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Kan ik de Azure-portal gebruiken om v3-resources te beheren?

Momenteel u de [Azure-portal](https://portal.azure.com/) gebruiken om:

* Media Services v3 [Live Events](live-events-outputs-concept.md)beheren, 
* bekijken (niet beheren) v3 [Activa](assets-concept.md), 
* [informatie krijgen over toegang tot API's](access-api-portal.md). 

Voor alle andere beheertaken (bijvoorbeeld [Transformaties en Taken](transforms-jobs-concept.md) en [Contentbescherming),](content-protection-overview.md)gebruikt u de [REST API](https://docs.microsoft.com/rest/api/media/), [CLI](https://aka.ms/ams-v3-cli-ref)of een van de ondersteunde [SDK's](media-services-apis-overview.md#sdks).

### <a name="is-there-an-assetfile-concept-in-v3"></a>Is er een AssetFile concept in v3?

De AssetFiles zijn uit de AMS API verwijderd om Media Services te scheiden van storage SDK-afhankelijkheid. Opslag, niet Media Services, bewaart nu de informatie die thuishoort in Storage. 

Zie [Migreren naar Media Services v3](media-services-v2-vs-v3.md)voor meer informatie.

### <a name="where-did-client-side-storage-encryption-go"></a>Waar is client-side storage encryptie gebleven?

Het wordt nu aanbevolen om de server-side storage encryptie te gebruiken (die standaard is ingeschakeld). Zie [Azure Storage Service Encryption for Data at Rest voor](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)meer informatie.

## <a name="next-steps"></a>Volgende stappen

[Media Services v3 overzicht](media-services-overview.md)
