---
title: Veelgestelde vragen over Azure Media Services v3 | Microsoft Docs
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: a2619293bf3641cdca370ff528a87ae879460a3b
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086792"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Veelgestelde vragen over Media Services v3

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Media Services (AMS).

## <a name="general"></a>Algemeen

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Wat Azure-rollen kunnen acties uitvoeren op Azure Media Services resources? 

Zie [op rollen gebaseerd toegangs beheer (RBAC) voor Media Services accounts](rbac-overview.md).

### <a name="how-do-you-stream-to-apple-ios-devices"></a>Hoe streamt u naar Apple iOS-apparaten?

Zorg ervoor dat u ' (Format = M3U8-AAPL) ' aan het einde van het pad (na het gedeelte '/manifest ' van de URL) hebt om de bron van de streaming-oorsprong te laten terugkeren HLS-inhoud voor het gebruik van Apple iOS systeem eigen apparaten (Zie [inhoud leveren](dynamic-packaging-overview.md)) voor meer informatie.

### <a name="how-do-i-configure-media-reserved-units"></a>Hoe kan ik gereserveerde media-eenheden configureren?

Voor de analysetaken van audio en video die worden geactiveerd door Media Services v3 of Video Indexer is het raadzaam om uw account in te richten met 10 S3 MRU’s. Als u meer dan 10 S3 MRUs nodig hebt, kunt u een ondersteunings ticket openen met behulp van de [Azure Portal](https://portal.azure.com/).

Zie [Media verwerking schalen met CLI](media-reserved-units-cli-how-to.md)voor meer informatie.

### <a name="what-is-the-recommended-method-to-process-videos"></a>Wat is de aanbevolen methode om Video's te verwerken?

[Trans formaties](https://docs.microsoft.com/rest/api/media/transforms) gebruiken om algemene taken te configureren voor het coderen of analyseren van Video's. Elke **trans formatie** beschrijft een recept of een werk stroom van taken voor het verwerken van uw video-of audio bestanden. Een [taak](https://docs.microsoft.com/rest/api/media/jobs) is de daad werkelijke aanvraag om Media Services om de **trans formatie** toe te passen op een gegeven video-of audio-inhoud. Zodra de trans formatie is gemaakt, kunt u taken verzenden met behulp van Media Services Api's of een van de gepubliceerde Sdk's. Zie [Transformaties en taken](transforms-jobs-concept.md) voor meer informatie.

### <a name="i-uploaded-encoded-and-published-a-video-what-would-be-the-reason-the-video-does-not-play-when-i-try-to-stream-it"></a>Ik heb een video geüpload, gecodeerd en gepubliceerd. Wat is de reden waarom de video niet wordt afgespeeld wanneer ik deze probeer te streamen?

Een van de meest voorkomende redenen is dat u het streaming-eind punt niet hebt van waaruit u wilt afspelen in de actieve status.

### <a name="how-does-pagination-work"></a>Hoe werkt de paginering?

Wanneer u paginering gebruikt, moet u altijd de volgende koppeling gebruiken om de verzameling op te sommen en niet afhankelijk van een bepaalde pagina grootte. Zie [filteren, ordenen, paginering](entities-overview.md)voor meer informatie en voor beelden.

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Welke functies zijn nog niet beschikbaar in Azure Media Services v3?

Zie voor meer informatie [functie hiaten met betrekking tot v2-api's](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Wat is het proces van het verplaatsen van een Media Services account tussen abonnementen?  

Zie [een Media Services account verplaatsen tussen abonnementen](media-services-account-concept.md)voor meer informatie.

## <a name="live-streaming"></a>Live streamen 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Hoe kan ik pauzes/Video's en afbeeldings pastels invoegen tijdens Live Stream?

Media Services v3 Live encoding biedt nog geen ondersteuning voor het invoegen van video-of afbeeldings pastels tijdens live stream. 

U kunt een [Live on-premises encoder](recommended-on-premises-live-encoders.md) gebruiken om de bron video te scha kelen. Veel apps bieden de mogelijkheid om bronnen te wisselen, waaronder Telestream Wirecast, Switch Studio (op iOS), IB Studio (gratis app) en nog veel meer.

## <a name="content-protection"></a>Inhoudsbeveiliging

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>Moet ik een AES-128 Clear Key Encryption of een DRM-systeem gebruiken?

Klanten zich vaak afvragen of ze AES-versleuteling of een DRM-systeem gebruiken moeten. Het belangrijkste verschil tussen de twee systemen is dat met AES-versleuteling de inhouds sleutel via TLS naar de client wordt verzonden, zodat de sleutel tijdens de overdracht wordt versleuteld, maar zonder extra versleuteling (' in de Clear '). Als gevolg hiervan is de sleutel die wordt gebruikt om de inhoud te ontsleutelen toegankelijk voor de client speler en kan deze worden weer gegeven in een netwerk tracering op de client als tekst zonder opmaak. Een AES-128 Clear sleutel versleuteling is geschikt voor gebruik waarbij de viewer een vertrouwde partij is (bijvoorbeeld het versleutelen van bedrijfs Video's die binnen een bedrijf worden gedistribueerd om door werk nemers te worden weer gegeven).

DRM-systemen zoals PlayReady, Widevine en FairPlay bieden een extra versleutelings niveau voor de sleutel die wordt gebruikt om de inhoud te ontsleutelen vergeleken met een AES-128 Clear-sleutel. De inhouds sleutel wordt versleuteld met een sleutel die wordt beveiligd door de DRM-runtime, in aanvulling op transport niveau versleuteling van TLS. Bovendien wordt ontsleuteling verwerkt in een beveiligde omgeving op het niveau van het besturingssysteem, waar is het moeilijker voor een kwaadwillende gebruiker om aan te vallen. DRM wordt aanbevolen voor gebruik gevallen waarbij de viewer mogelijk niet een vertrouwde partij en u het hoogste niveau van beveiliging nodig hebt.

### <a name="how-to-show-a-video-only-to-users-who-have-a-specific-permission-without-using-azure-ad"></a>Hoe kan ik een video alleen weer geven voor gebruikers die een specifieke machtiging hebben, zonder Azure AD te gebruiken?

U hoeft geen specifieke token provider (zoals Azure AD) te gebruiken. U kunt uw eigen [JWT](https://jwt.io/) -provider (dat wil zeggen STS, Secure Token Service) maken met behulp van asymmetrische sleutel versleuteling. In uw aangepaste STS kunt u claims toevoegen op basis van uw bedrijfs logica.

Zorg ervoor dat de uitgever, de doel groep en de claims exact overeenkomen met wat is in de JWT en de ContentKeyPolicyRestriction die wordt gebruikt in ContentKeyPolicy.

Zie [uw inhoud beveiligen met Media Services Dynamic Encryption](content-protection-overview.md)(Engelstalig) voor meer informatie.

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Hoe en waar u kunt JWT-token ophalen om de aanvraag-licentie of sleutel u?

1. Voor productie moet u een STS (Secure token Services) (webservice) hebben die een JWT-token verleent op een HTTPS-aanvraag. Voor test kunt u de code gebruiken die wordt weer gegeven in de methode **GetTokenAsync** die is gedefinieerd in [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Player moet een aanvraag maken nadat een gebruiker is geverifieerd, naar de STS voor dergelijke een token en wijs deze toe aan de waarde van het token. U kunt de [Azure Media Player-API](https://amp.azure.net/libs/amp/latest/docs/)gebruiken.

* Zie [https://aka.ms/jwt](https://aka.ms/jwt)voor een voor beeld van het uitvoeren van een STS met behulp van symmetrische en asymmetrische sleutels. 
* Voor een voor beeld van een speler op basis van Azure Media Player met behulp van deze JWT-token raadpleegt u [https://aka.ms/amtest](https://aka.ms/amtest) (vouw de koppeling ' player_settings ' uit om de invoer van het token te bekijken).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Hoe autoriseert u aanvragen voor stream-video's met AES-versleuteling

De juiste aanpak is het gebruikmaken van de STS (Secure Token Service):

In STS, afhankelijk van het gebruikers profiel, kunt u verschillende claims toevoegen (zoals ' Premium User ', ' Basic User ', ' gebruiker met gratis proef versie '). Met andere claims in een JWT ziet de gebruiker andere inhoud. Voor andere inhoud/asset, wordt de ContentKeyPolicyRestriction beschikken over de bijbehorende RequiredClaims.

Gebruik Azure Media Services Api's voor het configureren van de levering van licenties en sleutels en het versleutelen van uw assets (zoals weer gegeven in [dit voor beeld](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

Ga voor meer informatie naar:

- [Overzicht van inhoudsbeveiliging](content-protection-overview.md)
- [Ontwerp van een inhoudsbeveiligingssysteem van een multi-DRM met toegangsbeheer](design-multi-drm-system-with-access-control.md)

### <a name="http-or-https"></a>HTTP of HTTPS?
De speler ASP.NET MVC-toepassing moet ondersteunen het volgende:

* Verificatie van de gebruiker via Azure AD, deze bevindt zich onder HTTPS.
* JWT-uitwisseling tussen de client en de Azure AD, deze bevindt zich onder HTTPS.
* DRM-licentie overname door de client, die onder HTTPS worden moet als licentielevering is opgegeven door Media Services. De PlayReady-productpakket verplichten niet HTTPS voor de licentielevering van. Als uw PlayReady-licentie-server buiten het Media Services is, kunt u via HTTP of HTTPS.

De ASP.NET-player-toepassing maakt gebruik van HTTPS als een best practice, zodat de Media Player is op een HTTPS-pagina. HTTP is echter verkozen voor streaming, zodat u moet rekening houden met de uitgifte van gemengde inhoud.

* Gemengde inhoud toegestaan niet in de browser. Maar plug-ins zoals Silverlight en de invoegtoepassing voor OSMF soepel te verwerken en STREEPJES worden toegestaan. Gemengde inhoud is een beveiligingsprobleem vanwege de dreiging van de mogelijkheid om te ' injecteren ' schadelijke JavaScript, waardoor de gegevens van de klant moet lopen. Browsers deze mogelijkheid standaard geblokkeerd. De enige manier om dit oplossen door het is aan de serverzijde (oorsprong) doordat alle domeinen (ongeacht HTTPS of HTTP). Dit is waarschijnlijk niet een goed idee een.
* Vermijd gemengde inhoud. De player-toepassing en de Media Player moet HTTP of HTTPS te gebruiken. Tijdens het afspelen van gemengde inhoud, is de tech silverlightSS vereist een waarschuwing gemengde inhoud uit te schakelen. De techniek flashSS verwerkt gemengde inhoud zonder een waarschuwing gemengde inhoud.
* Als uw streaming-eindpunt is gemaakt vóór augustus 2014, niet HTTPS wordt ondersteund. In dit geval maken en gebruiken van een nieuwe streaming-eindpunt voor HTTPS.

### <a name="what-about-live-streaming"></a>Hoe zit live streamen?

Kunt u exact het dezelfde ontwerpen en implementeren om te beveiligen met live streaming in Media Services van de asset die zijn gekoppeld aan een programma als een activum VOD te behandelen. Als u een multi-DRM-beveiliging van de live-inhoud wilt bieden, moet u dezelfde instelling/verwerking Toep assen op de Asset alsof het een VOD-Asset was voordat u de Asset koppelt aan de live uitvoer.

### <a name="what-about-license-servers-outside-media-services"></a>Hoe zit het met licentieservers buiten Media Services?

Vaak klanten in een serverfarm licentie geïnvesteerd in hun eigen Datacenter of een gehost door serviceproviders DRM. Met Media Services content protection, kunt u gebruiken in de hybride-modus. De inhoud kunnen worden gehost en dynamisch in Media Services, beveiligd terwijl DRM-licenties worden geleverd door servers buiten Media Services. In dit geval kunt u overwegen de volgende wijzigingen:

* STS moet uitgeven van tokens die worden geaccepteerd en kunnen worden gecontroleerd door de licentie voor server-farm. De Widevine-licentieservers geleverd door Axinom vereisen bijvoorbeeld een specifieke JWT die een bericht rechten bevat. Daarom moet u een STS om uit te geven die een JWT hebben. 
* U moet niet meer licentieleveringsservice configureren in Media Services. U moet de licentie overname URL's opgeven (voor PlayReady, Widevine en FairPlay) wanneer u ContentKeyPolicies configureert.

> [!NOTE]
> Widevine is een service van Google Inc. en is onderworpen aan de service voorwaarden en het privacybeleid van Google, Inc.

## <a name="media-services-v2-vs-v3"></a>Media Services v2 VS v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Kan ik de Azure Portal gebruiken om v3-resources te beheren?

Op dit moment kunt u het [Azure Portal](https://portal.azure.com/) gebruiken voor het volgende:

* Media Services v3 [Live-gebeurtenissen](live-events-outputs-concept.md)beheren, 
* V3- [assets](assets-concept.md)weer geven (niet beheren), 
* [krijg informatie over het openen van api's](access-api-portal.md). 

Gebruik de [rest API](https://aka.ms/ams-v3-rest-ref), [cli](https://aka.ms/ams-v3-cli-ref)of een van de ondersteunde [sdk's](media-services-apis-overview.md#sdks)voor alle andere beheer taken (bijvoorbeeld [trans formaties en taken](transforms-jobs-concept.md) en [inhouds beveiliging](content-protection-overview.md)).

### <a name="is-there-an-assetfile-concept-in-v3"></a>Is er een AssetFile-concept in v3?

De AssetFiles zijn verwijderd uit de AMS-API om Media Services te scheiden van de opslag-SDK-afhankelijkheid. Nu opslag, niet Media Services, houdt de informatie bij die bij de opslag hoort. 

Zie [Migrate to Media Services v3](media-services-v2-vs-v3.md)(Engelstalig) voor meer informatie.

### <a name="where-did-client-side-storage-encryption-go"></a>Waar is de opslag versleuteling aan de client zijde gebleven?

U wordt aangeraden de opslag versleuteling aan de server zijde te gebruiken (deze is standaard ingeschakeld). Zie [Azure Storage-service versleuteling voor Data-at-rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Media Services v3](media-services-overview.md)
