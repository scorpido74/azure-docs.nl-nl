---
title: Bescherm uw inhoud met Media Services v3 dynamische versleuteling
titleSuffix: Azure Media Services
description: Meer informatie over inhoudsbescherming met dynamische versleuteling, streamingprotocollen en versleutelingstypen in Azure Media Services.
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
ms.date: 03/17/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: c1c9440f7ec70cea98f270f04c3030c800dd0fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461109"
---
# <a name="protect-your-content-with-media-services-dynamic-encryption"></a>Uw inhoud beveiligen met dynamische versleuteling van Media Services

Gebruik Azure Media Services om uw media te beveiligen vanaf het moment dat uw computer helemaal door opslag, verwerking en levering wordt verlaten. Met Media Services u uw live en on-demand content dynamisch versleuteld leveren met Advanced Encryption Standard (AES-128) of een van de drie belangrijkste DRM-systemen (Digital Rights Management): Microsoft PlayReady, Google Widevine en Apple FairPlay. Media Services biedt ook een service voor het leveren van AES-sleutels en DRM-licenties (PlayReady, Widevine en FairPlay) aan geautoriseerde clients. Als inhoud is versleuteld met een Duidelijke AES-sleutel en via HTTPS wordt verzonden, is deze pas duidelijk als deze de client bereikt. 

In Media Services v3 is een inhoudssleutel gekoppeld aan Streaming Locator (zie [dit voorbeeld).](protect-with-aes128.md) Als u de service voor het leveren van de sleutel van Media Services gebruikt, u Azure Media Services de inhoudssleutel voor u laten genereren. De inhoudssleutel moet zelf worden gegenereerd als u uw eigen belangrijke leveringsservice gebruikt, of als u een scenario met hoge beschikbaarheid moet verwerken waarbij u dezelfde inhoudssleutel in twee datacenters moet hebben.

Wanneer een stream door een speler wordt aangevraagd, gebruikt Media Services de opgegeven sleutel om uw inhoud dynamisch te versleutelen met behulp van AES clear key- of DRM-versleuteling. Om de stream te decoderen, vraagt de speler de sleutel aan bij de key delivery service van Media Services of de door u opgegeven belangrijke leveringsservice. Om te bepalen of de gebruiker gemachtigd is om de sleutel te krijgen, evalueert de service het inhoudssleutelbeleid dat u voor de sleutel hebt opgegeven.

U de REST-API of een Media Services-clientbibliotheek gebruiken om autorisatie- en verificatiebeleid voor uw licenties en sleutels te configureren.

De volgende afbeelding illustreert de workflow voor contentbescherming van Media Services:

![Werkstroom voor inhoudsbescherming mediaservices](./media/content-protection/content-protection.svg)
  
&#42; *Dynamic-versleuteling ondersteunt AES-128 clear key, CBCS en CENC. Zie voor meer informatie de [ondersteuningsmatrix](#streaming-protocols-and-encryption-types).*

In dit artikel worden concepten en terminologie uitgelegd die u helpen de inhoudsbescherming te begrijpen met Media Services.

## <a name="main-components-of-a-content-protection-system"></a>Belangrijkste onderdelen van een systeem voor inhoudsbescherming

Om uw contentbeveiligingssysteem succesvol te voltooien, moet u de omvang van de inspanning volledig begrijpen. De volgende secties geven een overzicht van drie onderdelen die u moet implementeren.

> [!NOTE]
> We raden u ten zeerste aan om elk onderdeel in de volgende secties volledig te concentreren en volledig te testen voordat u naar het volgende deel gaat. Als u uw inhoudsbeveiligingssysteem wilt testen, gebruikt u de gereedschappen die in de secties zijn opgegeven.

### <a name="media-services-code"></a>Media Services-code
  
Het [DRM-voorbeeld](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) laat u zien hoe u een multi-DRM-systeem implementeert met Media Services v3 met behulp van .NET. Het laat ook zien hoe u de Media Services-licentie/sleutelleveringsservice gebruiken.
  
U kunt elke asset met meerdere versleutelingstypen versleutelen (AES-128, PlayReady, Widevine, FairPlay). Zie [Streamingprotocollen en versleutelingstypen](#streaming-protocols-and-encryption-types)om te zien wat zinvol is om te combineren.

In het voorbeeld wordt uitgelegd hoe u:

1. Een [inhoudssleutelbeleid](content-key-policy-concept.md)maken en configureren .

   U maakt een beleid voor inhoudssleutel om te configureren hoe de inhoudssleutel (die veilige toegang tot uw assets biedt) wordt geleverd aan eindclients:  

   * Definieer licentieleveringsautorisatie. Geef de logica op van de autorisatiecontrole op basis van claims in JSON Web Token (JWT).
   * Configureer [PlayReady-,](playready-license-template-overview.md) [Widevine-](widevine-license-template-overview.md)en/of [FairPlay-licenties.](fairplay-license-overview.md) Met de sjablonen u rechten en machtigingen configureren voor elk van de DRM's.

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```

2. Maak een [streaminglocator](streaming-locators-concept.md) die is geconfigureerd om het versleutelde item te streamen.
  
   De streaming locator moet worden gekoppeld aan een [streaming beleid](streaming-policy-concept.md). In het voorbeeld `StreamingLocator.StreamingPolicyName` stellen we het beleid "Predefined_MultiDrmCencStreaming" in.

   De PlayReady- en Widevine-versleutelingen worden toegepast en de sleutel wordt geleverd aan de afspeelclient op basis van de geconfigureerde DRM-licenties. Als u uw stream ook wilt versleutelen met CBCS (FairPlay), gebruikt u het beleid "Predefined_MultiDrmStreaming".

   De streaminglocator is ook gekoppeld aan het beleid voor inhoudssleutel dat u hebt gedefinieerd.

3. Maak een testtoken.

   De `GetTokenAsync` methode laat zien hoe u een testtoken maakt.
4. Bouw de streaming URL.

   De `GetDASHStreamingUrlAsync` methode laat zien hoe u de streaming-URL maken. In dit geval streamt de URL de DASH-inhoud.

### <a name="player-with-an-aes-or-drm-client"></a>Speler met een AES- of DRM-client

Een videospeler-app op basis van een SDK voor spelers (native of browsergebaseerd) moet aan de volgende vereisten voldoen:

* De speler SDK ondersteunt de benodigde DRM clients.
* De speler SDK ondersteunt de vereiste streaming protocollen: Smooth, DASH, en / of HTTP Live Streaming (HLS).
* De speler SDK kan omgaan met het doorgeven van een JWT-token in een aanvraag voor licentieverwerving.

U een speler maken met behulp van de [Azure Media Player API.](https://amp.azure.net/libs/amp/latest/docs/) Gebruik de [Azure Media Player ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) om aan te geven welke DRM-technologie op verschillende DRM-platforms moet worden gebruikt.

Voor het testen van versleutelde inhoud van AES of CENC (Widevine en/of PlayReady) u [Azure Media Player](https://aka.ms/azuremediaplayer)gebruiken. Zorg ervoor dat u **geavanceerde opties** selecteert en controleer uw versleutelingsopties.

Als je FairPlay-versleutelde content wilt testen, gebruik [dan deze testspeler.](https://aka.ms/amtest) De speler ondersteunt Widevine, PlayReady en FairPlay DRMs, samen met AES-128 clear key encryptie.

Kies de juiste browser om verschillende DRM's te testen:

* Chrome, Opera of Firefox voor Widevine.
* Microsoft Edge of Internet Explorer 11 voor PlayReady.
* Safari op macOS voor FairPlay.

### <a name="security-token-service"></a>Service voor beveiligingstoken

Een beveiligingstokenservice (STS) geeft JWT uit als toegangstoken voor back-endresourcetoegang. U de licentie-/sleutelleveringsservice van Azure Media Services gebruiken als back-endbron. Een STS moet de volgende dingen definiëren:

* Uitgever en publiek (of bereik).
* Claims, die afhankelijk zijn van zakelijke vereisten op het gebied van contentbescherming.
* Symmetrische of asymmetrische verificatie voor verificatie van handtekeningen.
* Key rollover support (indien nodig).

U [deze STS-tool](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) gebruiken om de STS te testen. Het ondersteunt alle drie de typen verificatiesleutels: symmetrisch, asymmetrisch of Azure Active Directory (Azure AD) met sleutelrollover.

## <a name="streaming-protocols-and-encryption-types"></a>Protocollen voor streaming en versleutelingstypen

U Media Services gebruiken om uw inhoud dynamisch versleuteld te leveren met AES clear key- of DRM-versleuteling met PlayReady, Widevine of FairPlay. Momenteel u de INDELINGEN HLS, MPEG DASH en Smooth Streaming versleutelen. Elk protocol ondersteunt de volgende versleutelingsmethoden.

### <a name="hls"></a>HLS

Het HLS-protocol ondersteunt de volgende containerindelingen en versleutelingsschema's:

|Containerindeling|Versleutelingsschema|VOORBEELD URL|
|---|---|---|
|Alle|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (Fairplay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (Fairplay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS/CMAF + FairPlay (inclusief HEVC/H.265) wordt ondersteund op de volgende apparaten:

* iOS 11 of hoger.
* iPhone 8 of hoger.
* MacOS High Sierra met Intel 7e generatie CPU.

### <a name="mpeg-dash"></a>MPEG-DASH

Het MPEG-DASH-protocol ondersteunt de volgende containerindelingen en versleutelingsschema's:

|Containerindeling|Versleutelingsschema|URL-voorbeelden
|---|---|---|
|Alle|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Smooth Streaming

Het Smooth Streaming-protocol ondersteunt de volgende containerindelingen en versleutelingsschema's.

|Protocol|Containerindeling|Versleutelingsschema|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>Browsers

Veelvoorkomende browsers ondersteunen de volgende DRM-clients:

|Browser|Versleuteling|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>Inhoudstoegang beheren

U bepalen wie toegang heeft tot uw inhoud door het beleid voor inhoudssleutel te configureren. Media Services ondersteunt meerdere manieren om gebruikers te autoriseren die sleutels aanvragen. De klant (speler) moet aan het beleid voldoen voordat de sleutel aan de klant kan worden geleverd. Het beleid voor inhoudssleutel kan *open* of *tokenbeperking* hebben.

Een beleid met open inhoudssleutel kan worden gebruikt wanneer u zonder toestemming een licentie aan iemand wilt verlenen. Als uw inkomsten bijvoorbeeld op basis van advertenties zijn en niet op een abonnement zijn gebaseerd.  

Met een beleid voor inhoudssleutel met tokenwordt de inhoudssleutel alleen verzonden naar een client die een geldig JWT-token of een eenvoudig webtoken (SWT) in de licentie/sleutelaanvraag presenteert. Dit token moet worden uitgegeven door een STS.

U Azure AD als een STS gebruiken of een [aangepaste STS](#using-a-custom-sts)implementeren. De STS moet zijn geconfigureerd om een token te maken dat is ondertekend met de opgegeven sleutel- en uitgifteclaims die u hebt opgegeven in de configuratie van tokenbeperkingen. De Media Services-licentie/sleutelleveringsservice retourneert de gevraagde licentie of sleutel naar de client als beide voorwaarden bestaan:

* Het token is geldig.
* De claims in het token komen overeen met die welke zijn geconfigureerd voor de licentie of sleutel.

Wanneer u het beleid met een tokenbeperking configureert, moet u de primaire verificatiesleutel, de uitgever en de doelgroepparameters opgeven. De primaire verificatiesleutel bevat de sleutel waarmee het token is ondertekend. De uitgever is de STS die het token uitgeeft. De doelgroep, ook wel scope genoemd, beschrijft de intentie van het token of de bron waartoe het token toegang tot geeft. De mediaservices-licentie/key delivery-service valideert dat deze waarden in het token overeenkomen met de waarden in de sjabloon.

### <a name="token-replay-prevention"></a>Token replay preventie

Met de functie *Token Replay Prevention* kunnen mediaservices-klanten een limiet instellen voor het aantal keren dat hetzelfde token kan worden gebruikt om een sleutel of een licentie aan te vragen. De klant kan een `urn:microsoft:azure:mediaservices:maxuses` claim van het type toevoegen in het token, waarbij de waarde het aantal keren is dat het token kan worden gebruikt om een licentie of sleutel te verkrijgen. Alle volgende verzoeken met hetzelfde token aan Key Delivery sturen een ongeautoriseerde reactie terug. Zie hoe u de claim toevoegt in het [DRM-voorbeeld](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Overwegingen

* Klanten moeten controle hebben over het genereren van token. De claim moet in het token zelf worden geplaatst.
* Bij het gebruik van deze functie worden aanvragen met tokens waarvan de vervaldatum meer dan een uur verwijderd is van het moment dat het verzoek wordt ontvangen, geweigerd met een ongeautoriseerd antwoord.
* Tokens worden uniek geïdentificeerd door hun handtekening. Elke wijziging in de payload (bijvoorbeeld bijwerken naar de vervaldatum of de claim) verandert de handtekening van het token en het zal tellen als een nieuw token dat Key Delivery nog niet eerder is tegengekomen.
* Afspelen mislukt als het token `maxuses` de door de klant ingestelde waarde heeft overschreden.
* Deze functie kan worden gebruikt voor alle bestaande beveiligde inhoud (alleen het token dat is uitgegeven, moet worden gewijzigd).
* Deze functie werkt met zowel JWT als SWT.

## <a name="using-a-custom-sts"></a>Een aangepaste STS gebruiken

Een klant kan ervoor kiezen om een aangepaste STS te gebruiken om tokens te verstrekken. Redenen zijn onder meer:

* De identity provider (IDP) die door de klant wordt gebruikt, ondersteunt STS niet. In dit geval kan een aangepaste STS een optie zijn.
* De klant heeft mogelijk een flexibelere of strengere controle nodig om STS te integreren met het factureringssysteem voor abonnees van de klant.

   Een [OTT-serviceprovider](https://en.wikipedia.org/wiki/Over-the-top_media_services) kan bijvoorbeeld meerdere abonneepakketten aanbieden, zoals premium, basic en sport. De operator kan de claims in een token willen koppelen aan het pakket van een abonnee, zodat alleen de inhoud in een specifiek pakket beschikbaar wordt gesteld. In dit geval biedt een aangepaste STS de nodige flexibiliteit en controle.

* Aangepaste claims opnemen in het token om te kiezen tussen verschillende ContentKeyPolicyOptions met verschillende DRM-licentieparameters (een abonnementslicentie versus een huurlicentie).
* Een claim opnemen die de inhoudssleutel-id van de sleutel vertegenwoordigt waartoe het token toegang verleent.

Wanneer u een aangepaste STS gebruikt, moeten twee wijzigingen worden aangebracht:

* Wanneer u de licentieleveringsservice voor een asset configureert, moet u de beveiligingssleutel opgeven die wordt gebruikt voor verificatie door de aangepaste STS in plaats van de huidige sleutel van Azure AD.
* Wanneer een JTW-token wordt gegenereerd, wordt een beveiligingssleutel opgegeven in plaats van de privésleutel van het huidige X509-certificaat in Azure AD.

Er zijn twee soorten beveiligingssleutels:

* Symmetrische sleutel: Dezelfde sleutel wordt gebruikt om een JWT te genereren en te verifiëren.
* Asymmetrische sleutel: Een publiek-private sleutelpaar in een X509-certificaat wordt gebruikt met een privésleutel om een JWT te versleutelen/genereren en met de openbare sleutel om het token te verifiëren.

Als u .NET Framework/C# als ontwikkelingsplatform gebruikt, moet het X509-certificaat dat wordt gebruikt voor een asymmetrische beveiligingssleutel ten minste 2048 een sleutellengte hebben. Deze sleutellengte is een vereiste van het klasse System.IdentityModel.Tokens.X509AsymmetricSecurityKey in .NET Framework. Anders wordt de volgende uitzondering gegooid: IDX10630: Het 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' voor ondertekening kan niet kleiner zijn dan '2048' bits.

## <a name="custom-key-and-license-acquisition-url"></a>URL voor aangepaste sleutel en licentieverwerving

Gebruik de volgende sjablonen als u een andere licentie/sleutelleveringsservice wilt opgeven (niet Media Services). De twee vervangbare velden in de sjablonen zijn aanwezig, zodat u uw streamingbeleid delen over veel elementen in plaats van een streamingbeleid per actief te maken. 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`: Sjabloon voor de URL van de aangepaste service die sleutels levert aan spelers van eindgebruikers. Dit is niet vereist wanneer u Azure Media Services gebruikt voor het uitgeven van sleutels. 

   De sjabloon ondersteunt vervangbare tokens die de service tijdens runtime zal bijwerken met de waarde die specifiek is voor de aanvraag.  De momenteel ondersteunde tokenwaarden zijn:
   * `{AlternativeMediaId}`, die wordt vervangen door de waarde van StreamingLocatorId.AlternativeMediaId.
   * `{ContentKeyId}`, die wordt vervangen door de waarde van de id van de gevraagde sleutel.
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`: Sjabloon voor de URL van de aangepaste service die licenties levert aan eindgebruikers. Dit is niet vereist wanneer u Azure Media Services gebruikt voor het uitgeven van licenties.

   De sjabloon ondersteunt vervangbare tokens die de service tijdens runtime zal bijwerken met de waarde die specifiek is voor de aanvraag. De momenteel ondersteunde tokenwaarden zijn:  
   * `{AlternativeMediaId}`, die wordt vervangen door de waarde van StreamingLocatorId.AlternativeMediaId.
   * `{ContentKeyId}`, die wordt vervangen door de waarde van de id van de gevraagde sleutel. 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`: Hetzelfde als de vorige sjabloon, alleen voor Widevine. 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`: Hetzelfde als de vorige sjabloon, alleen voor FairPlay.  

Bijvoorbeeld:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId`heeft een waarde van de gevraagde sleutel. U kunt `AlternativeMediaId` het verzoek gebruiken als u het verzoek wilt toewijzen aan een entiteit aan uw zijde. U kunt `AlternativeMediaId` bijvoorbeeld machtigingen opzoeken.

Zie [Streamingbeleid voor](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)REST-voorbeelden die aangepaste URL's voor licentie-/sleutelacquisitie gebruiken .

> [!NOTE]
> Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="troubleshoot"></a>Problemen oplossen

Als u `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` de fout opneemt, moet u ervoor zorgen dat u het juiste streamingbeleid opgeeft.

Als u fouten krijgt `_NOT_SPECIFIED_IN_URL`die eindigen met, moet u ervoor zorgen dat u de versleutelingsindeling in de URL opgeeft. Een voorbeeld is `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Zie [Streamingprotocollen en versleutelingstypen](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Stel vragen, geef feedback, ontvang updates

Bekijk het communityartikel [van Azure Media Services](media-services-community.md) om verschillende manieren te zien waarop u vragen stellen, feedback geven en updates ontvangen over Media Services.

## <a name="next-steps"></a>Volgende stappen

* [Beveiligen met AES-versleuteling](protect-with-aes128.md)
* [Beschermen met DRM](protect-with-drm.md)
* [Ontwerp multi-DRM content protection system met toegangscontrole](design-multi-drm-system-with-access-control.md)
* [Versleuteling aan de opslagzijde](storage-account-concept.md#storage-side-encryption)
* [Veelgestelde vragen](frequently-asked-questions.md)
* [JSON-webtokenhandler](https://docs.microsoft.com/dotnet/framework/security/json-web-token-handler)
