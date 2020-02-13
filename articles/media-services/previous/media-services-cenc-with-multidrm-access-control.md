---
title: Ontwerp van een systeem van de beveiliging van inhoud met toegangsbeheer met Azure Media Services | Microsoft Docs
description: Meer informatie over licenties voor Microsoft Smooth Streaming Client Porting Kit.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: kilroyh;yanmf;juliako
ms.openlocfilehash: 68f42aa13288c2416257f3ba6c0b6072c1572977
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162987"
---
# <a name="design-of-a-content-protection-system-with-access-control-using-azure-media-services"></a>Ontwerp van een systeem van de beveiliging van inhoud met toegangsbeheer met Azure Media Services 

## <a name="overview"></a>Overzicht

Het ontwerpen en bouwen van een subsysteem digital rights management (DRM) voor een over-the-top (OTT) of online streaming-oplossing is een complexe taak. Operators/online uitbesteden video-providers meestal van deze taak op gespecialiseerde DRM-serviceproviders. Het doel van dit document is een referentieontwerp en -implementatie van een end-to-end DRM-subsysteem in een OTT of online streamingoplossing presenteren.

De betreffende lezers voor dit document zijn engineers die werken in DRM subsystemen van OTT of online streaming/Graphic; oplossingen of lezers die geïnteresseerd in DRM subsystemen zijn. Verondersteld wordt dat lezers bekend met ten minste één van de DRM-technologieën op de markt, zoals PlayReady, Widevine, FairPlay of Adobe-toegang bent.

In deze discussie van DRM bevatten we ook algemene versleuteling (CENC) met multi-DRM. Een belangrijke trend in online streaming en uit de branche OTT is het gebruik van CENC met meerdere systeemeigen DRM op verschillende-clientplatforms. Deze trend is een overstap van de voorgaande build is die een enkele DRM en de client-SDK voor verschillende-clientplatforms gebruikt. Wanneer u CENC gebruikt met multi-native DRM, worden zowel PlayReady als Widevine versleuteld volgens de specificatie van de [common Encryption (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) .

De voordelen van CENC met multi-DRM zijn dat deze:

* Vermindert de kosten voor codering omdat een enkel versleutelingsproces wordt gebruikt om u te richten op verschillende platforms met de systeemeigen DRM's.
* Reduceert de kosten van het beheer van versleutelde activa omdat slechts één exemplaar van de versleutelde activa nodig is.
* DRM-client licentiekosten omdat de systeemeigen DRM-client meestal vrije ruimte op het eigen platform is elimineert.

Microsoft is een actieve promoter DASH en CENC samen met enkele belangrijke branche-spelers. Azure Media Services biedt ondersteuning voor DASH en CENC. Zie de volgende blogs voor recente aankondigingen:

*  [Google Widevine-services voor het leveren van licenties in Azure Media Services aankondigen](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Azure Media Services voegt Google Widevine-verpakking toe voor het leveren van een multi-DRM-stroom](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="goals-of-the-article"></a>Doelstellingen van het artikel

De doelstellingen van dit artikel zijn:

* Geef een referentieontwerp van een DRM-subsysteem dat gebruikmaakt van CENC met multi-DRM.
* Geef een referentie-implementatie op een Azure/Media Services-platform.
* Sommige onderwerpen ontwerp en de implementatie besproken.

De term 'multi-DRM' in het artikel bevat informatie over de volgende producten:

* Microsoft PlayReady
* Google Widevine
* Apple FairPlay 

De volgende tabel geeft een overzicht van de systeemeigen platform/native app en browsers die worden ondersteund door elke DRM.

| **Client platform** | **Systeem eigen DRM-ondersteuning** | **Browser/app** | **Streaming-indelingen** |
| --- | --- | --- | --- |
| **Smart Tv's, operator STBs, OTT STBs** |PlayReady voornamelijk, en/of Widevine, en/of andere |Linux, Opera, via WebKit, overige |Verschillende indelingen |
| **Windows 10-apparaten (Windows-PC, Windows-tablets, Windows Phone, Xbox)** |PlayReady |Microsoft Edge/IE11/EME<br/><br/><br/>Universeel Windows-platform |STREEPJE (voor HLS, PlayReady wordt niet ondersteund)<br/><br/>DASH, Smooth Streaming (voor HLS, PlayReady wordt niet ondersteund) |
| **Android-apparaten (telefoon, Tablet, TV)** |Widevine |Chrome/EME |DASH, HLS |
| **iOS (iPhone, iPad), OS X-clients en Apple TV** |FairPlay |Safari 8 +/ EME |HLS |

Rekening houdend met de huidige status van de implementatie voor elke DRM, een service doorgaans wil implementeren twee of drie DRM's om ervoor te zorgen dat u alle typen van eindpunten in de beste manier kunt oplossen.

Er is een verschil tussen de complexiteit van de logica van de service en de complexiteit op de client tot een bepaalde mate van ervaring van de gebruiker op de verschillende clients.

Houd er rekening mee als u wilt een selectie hebt gemaakt:

* PlayReady is systeemeigen geïmplementeerd in elke Windows-apparaat, op bepaalde Android-apparaten en beschikbaar gesteld in software-SDK's op vrijwel elk platform.
* Widevine is systeemeigen geïmplementeerd in elke Android-apparaat, Chrome en sommige andere apparaten.
* FairPlay is beschikbaar op iOS en Mac OS-clients of via iTunes.

Er zijn twee opties voor een typische multi-DRM:

* PlayReady en Widevine
* PlayReady, Widevine en FairPlay

## <a name="a-reference-design"></a>Een referentieontwerp
In deze sectie geeft een referentieontwerp die is agnostisch ten opzichte van de technologieën die worden gebruikt om dit te implementeren.

Een DRM-subsysteem kan de volgende onderdelen bevatten:

* Sleutelbeheer
* DRM-pakketten
* Levering van DRM-licentie
* Recht controleren
* Verificatie/autorisatie
* Speler
* Oorsprong/content delivery network (CDN)

Het volgende diagram illustreert de op hoog niveau interactie tussen de onderdelen van een DRM-subsysteem:

![DRM-subsysteem met CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

Het ontwerp heeft drie fundamentele lagen:

* Een back-office-laag (zwart) is niet extern worden weergegeven.
* Een DMZ-laag (Donkerblauw) bevat alle eindpunten die toegankelijk zijn vanaf het publiek.
* Een openbare internetlaag (Lichtblauw) bevat de CDN en spelers met verkeer via het openbare internet.

Ook moet er een content management-hulpprogramma voor het besturingselement DRM-beveiliging, ongeacht of het statische of dynamische versleuteling. De invoer voor DRM-codering zijn onder andere:

* MBR video-inhoud
* Inhoudssleutel
* Licentie-URL's voor aanschaf

Dit is de op hoog niveau stroom tijdens het afspelen van tijd:

* De gebruiker wordt geverifieerd.
* Een verificatietoken is voor de gebruiker gemaakt.
* DRM beveiligde inhoud (manifest) wordt gedownload naar de speler.
* De speler verzendt een verzoek voor het ophalen van licentie met licentieservers samen met een sleutel-ID en een verificatietoken.

In de volgende sectie wordt beschreven voor het ontwerp van Sleutelbeheer.

| **ContentKey-to-Asset** | **Scenario** |
| --- | --- |
| 1-op-1 |Het meest eenvoudige geval. Het biedt de beste controle. Maar resulteert deze benadering in het algemeen in de hoogste kosten voor de levering van licentie. Ten minste is één licentie vereist voor alle beveiligde activa. |
| 1-op-veel |U kunt dezelfde inhoud sleutel gebruiken voor meerdere assets. Bijvoorbeeld, voor alle activa in een logische groep, kunt zoals een genre of de subset met een genre (of film gen), u een enkele inhoudssleutel. |
| Veel-op-1 |Meerdere inhoudssleutels nodig zijn voor elk actief. <br/><br/>Als u nodig hebt om toe te passen van de beveiliging van het dynamische CENC met multi-DRM voor MPEG-DASH en dynamische AES-128-versleuteling voor HLS, moet u bijvoorbeeld twee afzonderlijke inhoudssleutels. Elke sleutel moet een eigen ContentKeyType. (Gebruik voor de inhoudssleutel wordt gebruikt voor dynamische CENC beveiliging, ContentKeyType.CommonEncryption. Voor de inhoudssleutel wordt gebruikt voor dynamische AES-128-versleuteling, gebruikt u ContentKeyType.EnvelopeEncryption.)<br/><br/>Als een ander voorbeeld kunt in CENC beveiliging van DASH-inhoud, in theorie, u een inhoudssleutel ter bescherming van de video-stream en een andere inhoud sleutel voor het beveiligen van de audio-stream. |
| Veel-op-veel |De combinatie van de vorige twee scenario's. Een set met inhoud sleutels wordt gebruikt voor elk van de meerdere assets in de groep met dezelfde activa. |

Een andere belangrijke factoren om rekening houden is het gebruik van permanente en niet-persistente-licenties.

Waarom deze overwegingen belangrijk zijn?

Als u een openbare cloud voor de licentielevering van de, hebben permanente en niet-persistente licenties een directe invloed op kosten voor de levering van licentie. De volgende twee verschillende ontwerp-gevallen hebben om te illustreren:

* Maandabonnement: gebruik een permanente licentie en 1-op-veel inhoud sleutel-asset-toewijzing. Bijvoorbeeld, voor alle kinderen films gebruiken we een enkele inhoudssleutel voor versleuteling. In dit geval:

    Totaal aantal licenties die zijn aangevraagd voor alle kinderen films/apparaat = 1

* Maandabonnement: gebruik een niet-persistente licentie en 1-op-1-toewijzing tussen inhoudssleutel en asset. In dit geval:

    Totaal aantal licenties die zijn aangevraagd voor alle kinderen films/apparaat = [aantal films bekeken] x [aantal sessies]

De twee verschillende modellen resulteren in zeer verschillende licentie aanvraag patronen. De verschillende patronen leiden tot verschillende licentielevering kosten als service voor het leveren van licentie wordt geleverd door een openbare cloud, zoals Media Services.

## <a name="map-design-to-technology-for-implementation"></a>Ontwerp van de kaart voor de technologie voor implementatie
Het ontwerp van de algemene wordt vervolgens door op te geven welke technologie gebruiken voor elke bouwsteen toegewezen aan technologieën op het Azure/Media Services-platform.

De volgende tabel ziet u de toewijzing.

| **Bouw steen** | **Technologie** |
| --- | --- |
| **Treden** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **ID-provider (IDP)** |Azure Active Directory (Azure AD) |
| **Security Token Service (STS)** |Azure AD |
| **DRM-beveiligings werk stroom** |Media Services dynamische beveiliging |
| **Levering van DRM-licentie** |* Media Services-licentie leveringsmethode (PlayReady, Widevine, FairPlay) <br/>* Axinom licentieserver <br/>* Aangepaste PlayReady-licentieserver |
| **Oorsprong** |Media Services streaming-eindpunt |
| **Sleutelbeheer** |Niet nodig voor de referentie-implementatie |
| **Inhoudsbeheer** |Een C#-consoletoepassing |

Met andere woorden, worden zowel de id-provider en de STS gebruikt met Azure AD. De [Azure Media Player-API](https://amp.azure.net/libs/amp/latest/docs/) wordt gebruikt voor de speler. Ondersteunen zowel Media Services en Media Player DASH en CENC met multi-DRM.

Het volgende diagram toont de algemene structuur en de stroom met de vorige technologie-toewijzing:

![CENC op mediaservices](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Als u dynamische versleuteling voor CENC instelt, wordt het content management-hulpprogramma gebruikt de volgende invoer:

* Open inhoud
* Inhoudssleutel uit de sleutel genereren/beheer
* Licentie-URL's voor aanschaf
* Een lijst met gegevens uit Azure AD

Hier volgt de uitvoer van het hulpprogramma voor inhoudsbeheer:

* ContentKeyAuthorizationPolicy bevat de specificatie op hoe licentielevering controleert of een JSON Web Token (JWT) en de specificaties van DRM-licentie.
* AssetDeliveryPolicy bevat de specificaties voor streaming-indeling, DRM-beveiliging en URL's aanschaf van licentie.

Dit is de stroom tijdens runtime:

* Bij verificatie van de gebruiker wordt een JWT gegenereerd.
* Een van de claims in de JWT is een claim van groepen die het groepsobject-ID EntitledUserGroup bevat. Deze claim wordt gebruikt om door te geven van de rechten van een selectievakje.
* De speler downloadt de client het manifest van CENC beschermde inhoud en identificeert het volgende:
   * Sleutel-ID.
   * De inhoud is CENC beveiligd.
   * URL's aanschaf van licentie.
* De speler wordt een licentieaanvraag ophalen op basis van de browser/DRM ondersteund. In de licentie overname aanvraag, de sleutel-ID en de JWT worden ook verzonden. De service voor het leveren van licenties controleert of de JWT- en de claims voordat de benodigde licentie.

## <a name="implementation"></a>Implementatie
### <a name="implementation-procedures"></a>Procedures voor implementatie
Implementatie omvat de volgende stappen uit:

1. Bereid test activa. Een video naar multi-bitrate test coderen/pakket gefragmenteerd MP4 in Media Services. Deze asset is *niet* beveiligd met DRM. DRM-bescherming wordt geboden door dynamische beveiliging later opnieuw.

2. Maak een sleutel-ID en een inhoudssleutel (eventueel van een sleutel seed). In dit geval de sleutelbeheersysteem is niet nodig omdat alleen één sleutel-ID en sleutel zijn vereist voor een aantal test activa.

3. Gebruik de API van Media Services om te configureren van multi-DRM-licentie levering van services voor de test-asset. Als u aangepaste licentieservers door uw bedrijf of van uw bedrijf leveranciers in plaats van licentie-services in Media Services gebruikt, kunt u deze stap overslaan. Wanneer u licentielevering configureert, kunt u licentie overname URL's in de stap. De API van Media Services is nodig om op te geven van sommige. gedetailleerde configuraties, zoals autorisatie-beleidsbeperking en licentie antwoord sjablonen voor verschillende services van DRM-licentie. Op dit moment biedt de Azure-portal niet de benodigde gebruikersinterface voor deze configuratie. Zie voor informatie over het API-niveau en voorbeeld code [PlayReady en/of Widevine Dynamic common Encryption gebruiken](media-services-protect-with-playready-widevine.md).

4. De API van Media Services gebruiken om te configureren van het leveringsbeleid voor Assets voor de test-asset. Zie voor informatie over het API-niveau en voorbeeld code [PlayReady en/of Widevine Dynamic common Encryption gebruiken](media-services-protect-with-playready-widevine.md).

5. Maken en configureren van een Azure AD-tenant in Azure.

6. Maak een paar gebruikersaccounts en groepen in uw Azure AD-tenant. Ten minste een 'Gebruiker het recht'-groep maken en een gebruiker toevoegen aan deze groep. De controle van de rechten van doorgeven gebruikers in deze groep in verwerving van licenties. Gebruikers die niet in deze groep om door te geven van de controle mislukt en kunnen niet een licentie. Lidmaatschap van deze groep 'Gebruiker het recht' is een claim van de vereiste groepen in de JWT dat is uitgegeven door Azure AD. U geeft deze vereiste claim in de stap bij het configureren van multi-DRM-services voor het leveren van licenties.

7. Maak een ASP.NET MVC-app voor het hosten van uw video speler. Deze ASP.NET-app is beveiligd met verificatie op basis van de Azure AD-tenant. Juiste claims zijn opgenomen in de toegangstokens verkregen na verificatie van de gebruiker. We raden OpenID Connect-API voor deze stap. Installeer de volgende NuGet-pakketten:

   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

8. Een speler maken met behulp van de [Azure Media Player-API](https://amp.azure.net/libs/amp/latest/docs/). Gebruik de [Azure Media Player PROTECTIONINFO API](https://amp.azure.net/libs/amp/latest/docs/) om op te geven welke DRM-technologie op verschillende DRM-platforms moet worden gebruikt.

9. De volgende tabel ziet u de testmatrix.

    | **Digital** | **Browser** | **Resultaat van gebruiker met recht** | **Resultaat voor onbevoegd gebruiker** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge of Internet Explorer 11 op Windows 10 |Voltooid |Mislukt |
    | **Widevine** |Chrome, Firefox, Opera |Voltooid |Mislukt |
    | **FairPlay** |Safari op Mac OS      |Voltooid |Mislukt |
    | **AES-128** |De meeste moderne browsers  |Voltooid |Mislukt |

Voor informatie over het instellen van Azure AD voor een ASP.NET MVC Player-app raadpleegt u [een Azure Media Services op OWIN MVC gebaseerde app integreren met Azure Active Directory en de levering van de inhouds sleutel beperken op basis van JWT-claims](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Zie [JWT-token verificatie in azure Media Services en dynamische versleuteling](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)voor meer informatie.  

Voor informatie over Azure AD:

* In de [hand leiding voor ontwikkel aars van Azure Active Directory](../../active-directory/azuread-dev/v1-overview.md)vindt u informatie voor ontwikkel aars.
* U kunt beheerders informatie vinden in [uw Azure AD-Tenant Directory beheren](../../active-directory/fundamentals/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Sommige problemen in uitvoering
Gebruik de volgende informatie voor probleemoplossing voor hulp bij problemen met implementatie.

* De verlener URL met eindigen moet '/'. De doelgroep moet de speler toepassing client-ID. Ook toe te voegen '/' aan het einde van de URL-verlener.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    In de [JWT-decoder](http://jwt.calebb.net/)ziet u **AUD** en **ISS**, zoals wordt weer gegeven in de JWT:

    ![JWT](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Voeg machtigingen toe aan de toepassing in azure AD op het tabblad **configureren** van de toepassing. Machtigingen zijn vereist voor elke toepassing, lokale en geïmplementeerde versies.

    ![Machtigingen](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

* Gebruik de juiste verlener bij het instellen van dynamische CENC beveiliging.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    De volgende werkt niet:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    De GUID is de Azure AD-tenant-ID. De GUID vindt u in het pop-upmenu **endpoints** in het Azure Portal.

* Lidmaatschap van verlenen claims bevoegdheden. Zorg ervoor dat het volgende in het manifestbestand van de Azure AD-toepassing: 

    "groupMembershipClaims": "Alle" (de standaardwaarde is null)

* Stel de juiste TokenType bij het maken van beperking vereisten.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Omdat u ondersteuning voor JWT (Azure AD) naast SWT (ACS) toevoegt, is de standaardwaarde TokenType TokenType.JWT. Als u SWT/ACS gebruikt, moet u het token instellen op TokenType.SWT.

## <a name="additional-topics-for-implementation"></a>Aanvullende onderwerpen voor implementatie
Deze sectie wordt besproken enkele aanvullende onderwerpen in het ontwerp en implementatie.

### <a name="http-or-https"></a>HTTP of HTTPS?
De speler ASP.NET MVC-toepassing moet ondersteunen het volgende:

* Verificatie van de gebruiker via Azure AD, deze bevindt zich onder HTTPS.
* JWT-uitwisseling tussen de client en de Azure AD, deze bevindt zich onder HTTPS.
* DRM-licentie overname door de client, die onder HTTPS worden moet als licentielevering is opgegeven door Media Services. De PlayReady-productpakket verplichten niet HTTPS voor de licentielevering van. Als uw PlayReady-licentie-server buiten het Media Services is, kunt u via HTTP of HTTPS.

De ASP.NET-player-toepassing maakt gebruik van HTTPS als een best practice, zodat de Media Player is op een HTTPS-pagina. HTTP is echter verkozen voor streaming, zodat u moet rekening houden met de uitgifte van gemengde inhoud.

* Gemengde inhoud toegestaan niet in de browser. Maar plug-ins zoals Silverlight en de invoegtoepassing voor OSMF soepel te verwerken en STREEPJES worden toegestaan. Gemengde inhoud is een beveiligingsprobleem vanwege de dreiging van de mogelijkheid om te ' injecteren ' schadelijke JavaScript, waardoor de gegevens van de klant moet lopen. Browsers deze mogelijkheid standaard geblokkeerd. De enige manier om dit oplossen door het is aan de serverzijde (oorsprong) doordat alle domeinen (ongeacht HTTPS of HTTP). Dit is waarschijnlijk niet een goed idee een.
* Vermijd gemengde inhoud. De player-toepassing en de Media Player moet HTTP of HTTPS te gebruiken. Tijdens het afspelen van gemengde inhoud, is de tech silverlightSS vereist een waarschuwing gemengde inhoud uit te schakelen. De techniek flashSS verwerkt gemengde inhoud zonder een waarschuwing gemengde inhoud.
* Als uw streaming-eindpunt is gemaakt vóór augustus 2014, niet HTTPS wordt ondersteund. In dit geval maken en gebruiken van een nieuwe streaming-eindpunt voor HTTPS.

In de referentie-implementatie voor DRM beveiligde inhoud, de toepassing en de streaming zijn onder HTTPS. Niet voor inhoud openen moet de speler verificatie of een licentie, zodat u via HTTP of HTTPS gebruiken kunt.

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Active Directory rollover van ondertekeningssleutel gebruiken
Rollover van ondertekeningssleutel gebruiken is een belangrijk punt om rekening mee te houden in uw implementatie. Als u deze negeren, het voltooide systeem uiteindelijk werkt niet volledig binnen zes weken op het meest.

Azure AD maakt gebruik van industriële standaarden vertrouwensrelatie tussen zelf en toepassingen die gebruikmaken van Azure AD. Azure AD gebruikt met name een ondertekeningssleutel die uit een openbare en persoonlijke sleutelpaar bestaat. Als u Azure AD maakt een beveiligingstoken dat informatie over de gebruiker bevat, het is ondertekend door Azure AD met een persoonlijke sleutel voordat deze wordt verzonden naar de toepassing. Om te controleren dat het token geldig en oorsprong van Azure AD is, moet de toepassing van het token handtekening valideren. De toepassing maakt gebruik van de openbare sleutel beschikbaar is gemaakt door Azure AD die is opgenomen in het document met federatieve metagegevens van de tenant. Deze openbare sleutel en de handtekeningsleutel waarvan deze is afgeleid, is hetzelfde account gebruikt voor alle tenants in Azure AD.

Zie [belang rijke informatie over de rollover van de handtekening sleutel in azure AD](../../active-directory/active-directory-signing-key-rollover.md)voor meer informatie over de sleutel rollover van Azure AD.

Tussen het [open bare-persoonlijke sleutel paar](https://login.microsoftonline.com/common/discovery/keys/):

* De persoonlijke sleutel wordt gebruikt door Azure AD voor het genereren van een JWT.
* De openbare sleutel wordt gebruikt door een toepassing zoals DRM-licentie levering van services in Media Services om te controleren of de JWT.

Azure AD draait uit veiligheidsoverwegingen regelmatig het certificaat (elke zes weken). In het geval van inbreuk op de beveiliging, kan de rollover van ondertekeningssleutel elk gewenst moment optreden. Daarom moeten de services voor het leveren van licenties in Media Services bijwerken van de openbare sleutel als Azure AD het sleutelpaar draait gebruikt. Anders tokenverificatie in Media Services is mislukt en er is geen licentie wordt verleend.

Als u deze service instelt, stelt u TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument bij het configureren van DRM-services voor het leveren van licenties.

Hier volgt de JWT-stroom:

* Azure AD geeft de JWT met de huidige persoonlijke sleutel voor een geverifieerde gebruiker.
* Wanneer een speler een CENC met multi-DRM beveiligde inhoud, zoekt deze eerst de JWT dat is uitgegeven door Azure AD.
* De speler verzendt een aanvraag voor het ophalen van licentie met de JWT naar services voor het leveren van licenties in Media Services.
* De huidige/geldige openbare sleutel van Azure AD de services voor het leveren van licenties in Media Services gebruiken om te controleren of de JWT alvorens licenties.

DRM-services voor het leveren van licentie wordt altijd controleren voor de huidige/geldige openbare sleutel van Azure AD. De openbare sleutel aangeboden door Azure AD is de sleutel die wordt gebruikt om te controleren of een JWT dat is uitgegeven door Azure AD.

Wat gebeurt er als de rollover van ondertekeningssleutel gebeurt nadat genereert een JWT voor Azure AD, maar voordat de JWT wordt verzonden door spelers met DRM-licentie levering van services in Media Services voor verificatie?

Omdat een sleutel kan op elk moment worden vernieuwd, is meer dan één geldige openbare sleutel altijd beschikbaar zijn in het document met federatieve metagegevens. Levering van Media Services-licentie kunt u elk van de sleutels die zijn opgegeven in het document. Omdat één sleutel kan snel worden geïmplementeerd, mogelijk een andere de vervanging ervan en enzovoorts bedekt.

### <a name="where-is-the-access-token"></a>Waar is het toegangstoken?
Als u kijkt hoe een web-app een API-app aanroept onder [toepassings identiteit met OAuth 2,0-client referenties toewijzen](../../active-directory/azuread-dev/web-api.md), is de verificatie stroom als volgt:

* Een gebruiker zich aanmeldt bij Azure AD in de web-App. Zie webbrowser [to Web Application](../../active-directory/azuread-dev/web-app.md)(Engelstalig) voor meer informatie.
* Het Azure AD-autorisatie-eindpunt leidt de gebruikersagent terug naar de clienttoepassing met een autorisatiecode. De gebruikersagent retourneert de autorisatiecode op de clienttoepassing omleidings-URI.
* De web-App moet een toegangstoken verkrijgen, zodat het kan worden geverifieerd bij de web-API en opvragen van de gewenste resource. Het maakt een aanvraag voor het eindpunt van de Azure AD-token en voorziet in de referentie, client-ID en de web-API-toepassing-ID-URI. Deze geeft de autorisatiecode om te bewijzen dat de gebruiker heeft ingestemd.
* Azure AD verifieert de toepassing en retourneert een JWT-toegangstoken dat wordt gebruikt voor de web-API aanroepen.
* Via HTTPS gebruikt de web-App het geretourneerde JWT-toegangstoken om toe te voegen van de JWT-tekenreeks met een aanduiding 'Bearer' in de header 'Autorisatie' van de aanvraag naar de web-API. De web-API controleert vervolgens de JWT. Als de validatie is geslaagd, wordt de gewenste resource.

In deze stroom van de identiteit toepassing vertrouwt de web-API die de web-App de gebruiker geverifieerd. Dit patroon wordt een subsysteem van het vertrouwde genoemd om deze reden. In het [diagram autorisatie stroom](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) wordt beschreven hoe autorisatie-granting flow werkt.

Licentie ophalen met de tokenbeperking volgt hetzelfde patroon vertrouwde subsysteem. De service voor het leveren van licenties in Media Services is de web-API-resource, of het 'back-end resource' die een webtoepassing moet toegang hebben tot. Dus waar het toegangstoken is?

Het toegangstoken is verkregen via Azure AD. Na een geslaagde authenticatie maakt wordt een autorisatiecode geretourneerd. De autorisatiecode wordt vervolgens gebruikt, samen met de client-ID en de app-sleutel voor het uitwisselen van voor het toegangstoken. Het toegangstoken wordt gebruikt voor toegang tot een "pointer"-toepassing die verwijst naar of Hiermee geeft u de Media Services-service voor het leveren van licenties.

Als u wilt registreren en configureren van de wijzer-app in Azure AD, moet u de volgende stappen uitvoeren:

1. In de Azure AD-tenant:

   * Een toepassing (resource) met de aanmeldings-URL https://[resource_name].azurewebsites.net/ toevoegen. 
   * Toevoegen van een app-ID met de URL https://[aad_tenant_name].onmicrosoft.com/[resource_name].

2. Voeg een nieuwe sleutel voor de resource-app.

3. Het manifestbestand van de app bijwerken zodat de eigenschap groupMembershipClaims de waarde 'groupMembershipClaims heeft': 'Alle'.

4. Voeg in de Azure AD-app die verwijst naar de Web-App van de speler, in de sectie **machtigingen voor andere toepassingen**, de resource-app toe die is toegevoegd in stap 1. Onder **gedelegeerde machtiging**selecteert u **toegang [RESOURCE_NAME]** . Met deze optie geeft de web-app-machtiging voor het maken van toegangstokens die toegang hebben tot de resource-app. Als u met Visual Studio en de Azure-web-app ontwikkelt, moet u dit doen voor de lokale en geïmplementeerde versie van de web-app.

De JWT dat is uitgegeven door Azure AD is het toegangstoken dat wordt gebruikt voor toegang tot de resource van de wijzer.

### <a name="what-about-live-streaming"></a>Hoe zit live streamen?
De vorige discussie gericht op middelen op aanvraag. Hoe zit live streamen?

Kunt u exact het dezelfde ontwerpen en implementeren om te beveiligen met live streaming in Media Services van de asset die zijn gekoppeld aan een programma als een activum VOD te behandelen.

Met name om live te streamen in Media Services, moet u een kanaal maken en maak vervolgens een programma onder het kanaal. Voor het maken van het programma dat u wilt maken van een asset die het livearchief voor het programma bevat. Voor CENC met multi-DRM-beveiliging van de live-inhoud van de dezelfde setup/verwerking naar de asset toepassing alsof het een activum VOD voordat u begint met het programma.

### <a name="what-about-license-servers-outside-media-services"></a>Hoe zit het met licentieservers buiten Media Services?
Vaak klanten in een serverfarm licentie geïnvesteerd in hun eigen Datacenter of een gehost door serviceproviders DRM. Met Media Services Content Protection, kunt u gebruiken in de hybride-modus. De inhoud kunnen worden gehost en dynamisch in Media Services, beveiligd terwijl DRM-licenties worden geleverd door servers buiten Media Services. In dit geval kunt u overwegen de volgende wijzigingen:

* STS moet uitgeven van tokens die worden geaccepteerd en kunnen worden gecontroleerd door de licentie voor server-farm. De Widevine-licentieservers geleverd door Axinom vereisen bijvoorbeeld een specifieke JWT die een bericht rechten bevat. Daarom moet u een STS om uit te geven die een JWT hebben. Voor informatie over dit type implementatie gaat u naar het [Azure-documentatie centrum](https://azure.microsoft.com/documentation/) en raadpleegt u [Axinom gebruiken om Widevine-licenties te leveren aan Azure Media Services](media-services-axinom-integration.md).
* U moet niet meer licentieleveringsservice (ContentKeyAuthorizationPolicy) configureren in Media Services. U moet de licentie overname URL's opgeven (voor PlayReady, Widevine en FairPlay) wanneer u AssetDeliveryPolicy het instellen van CENC met multi-DRM configureert.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Wat gebeurt er als ik wil een aangepaste STS gebruiken?
Een klant kan kiezen voor een aangepaste STS JWTs opgeven. Redenen zijn:

* STS biedt geen ondersteuning voor de id-provider die wordt gebruikt door de klant. In dit geval een aangepaste STS mogelijk een optie.
* De klant mogelijk meer flexibele of betere controle STS integreren met de klant abonnee factureringssysteem. Bijvoorbeeld, een operator MVPD biedt mogelijk meerdere OTT-abonnee pakketten, zoals basic, premium en sport. De operator wilt overeenkomen met de claims in een token met de abonnee pakket zodat alleen de inhoud in een specifiek pakket beschikbaar worden gesteld. In dit geval een aangepaste STS biedt de benodigde flexibiliteit en controle.

Wanneer u een aangepaste STS, moet twee worden gewijzigd:

* Wanneer u een service voor het leveren van licenties voor een asset configureert, moet u opgeven de beveiligingssleutel gebruikt voor verificatie door de aangepaste STS in plaats van de huidige sleutel van Azure AD. (Meer informatie volgt.) 
* Wanneer een JTW-token wordt gegenereerd, een beveiligingssleutel is opgegeven in plaats van de persoonlijke sleutel van de huidige X509 certificaat in Azure AD.

Er zijn twee soorten sleutels:

* Symmetrische sleutel: dezelfde sleutel wordt gebruikt voor het genereren en te controleren of een JWT.
* Asymmetrische sleutel: een openbaar / persoonlijk sleutelpaar in een X509 certificaat wordt gebruikt met een persoonlijke sleutel voor het versleutelen/genereren van een JWT en met de openbare sleutel om te controleren of het token.

> [!NOTE]
> Als u .NET Framework / C# als uw ontwikkelplatform, de X509 certificaat dat wordt gebruikt voor een asymmetrische beveiligingssleutel moet een sleutellengte van ten minste 2048 hebben. Dit is een vereiste van de klasse System.IdentityModel.Tokens.X509AsymmetricSecurityKey in .NET Framework. Anders is de volgende uitzondering opgetreden:
> 
> IDX10630: System.IdentityModel.Tokens.X509AsymmetricSecurityKey voor het ondertekenen van mag niet kleiner dan '2048-bits.

## <a name="the-completed-system-and-test"></a>Het voltooide systeem en de test
In dit gedeelte leidt u door de volgende scenario's in het voltooide end-to-end-systeem zodat u een eenvoudige beeld van het gedrag hebben kunt voordat u een account aanmelden:

* Als u een niet-geïntegreerd scenario moet:

    * Voor video activa die worden gehost in Media Services die een onbeveiligd of DRM beveiligd maar zonder tokenverificatie (een licentie verlenen aan iedereen die deze aangevraagd), kunt u deze testen zonder dat u aangemeld. Schakel over naar HTTP als uw video-streaming via HTTP.

* Als u een geïntegreerde end-to-end-scenario moet:

    * Voor videomedia onder de dynamische DRM-beveiliging in Media Services met tokenverificatie en JWT die worden gegenereerd door Azure AD, moet u zich aanmeldt.

Zie [deze website](https://openidconnectweb.azurewebsites.net/)voor de webtoepassing van de speler en de aanmelding.

### <a name="user-sign-in"></a>Gebruikersaanmelding
Als u wilt testen van het end-to-end geïntegreerde DRM-systeem, moet u hebt een account te maken of toegevoegd.

Welk account?

Hoewel Azure oorspronkelijk alleen toegang Microsoft-accountgebruikers, wordt toegang is nu toegestaan door gebruikers van beide systemen. Alle Azure-eigenschappen vertrouwt nu Azure AD voor verificatie en Azure AD verifieert de organisatie-gebruikers. Een federatieve relatie is gemaakt waarin Azure AD vertrouwt voor het Microsoft-account klantidentiteitssysteem om klantgebruikers te verifiëren. Azure AD kan als gevolg hiervan gastaccounts Microsoft ook als systeemeigen Azure AD-accounts verifiëren.

Omdat Azure AD het domein van Microsoft-account wordt vertrouwd, kunt u alle accounts uit een van de volgende domeinen naar de aangepaste Azure AD-tenant en het account gebruiken voor aanmelding bij toevoegen:

| **Domein naam** | **Domeinen** |
| --- | --- |
| **Aangepast Azure AD-Tenant domein** |somename.onmicrosoft.com |
| **Bedrijfs domein** |Microsoft.com |
| **Microsoft-account domein** |Outlook.com, live.com, hotmail.com |

U kunt contact opnemen met een van de auteurs hebben een account te maken of toegevoegd voor u.

De volgende schermafbeeldingen tonen verschillende aanmelden pagina's die worden gebruikt door verschillende domeinaccounts:

**Aangepast domein account voor Azure AD-Tenant**: de aangepaste aanmeldings pagina van het aangepaste domein van de Azure AD-Tenant.

![Domeinaccount voor aangepast Azure AD-tenant](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Micro soft-domein account met Smart Card**: de aanmeldings pagina die door micro soft wordt aangepast met twee ledige verificatie.

![Domeinaccount voor aangepast Azure AD-tenant](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft-account**: de aanmeldings pagina van de Microsoft-account voor consumenten.

![Domeinaccount voor aangepast Azure AD-tenant](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Versleutelde Media-extensies gebruiken voor PlayReady
PlayReady is op een moderne browser met Encrypted Media Extensions (EME) voor PlayReady-ondersteuning, zoals Internet Explorer 11 op Windows 8.1 of hoger en Microsoft Edge-browser op Windows 10, de onderliggende DRM voor EME.

![Gebruik EME voor PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

Het donkere player-gebied is omdat de PlayReady-bescherming wordt voorkomen dat u een schermopname van beveiligde video maken.

De volgende schermafbeelding ziet u de player-invoegtoepassingen en Microsoft Security Essentials (MSE) / EME ondersteuning voor:

![Player-invoegtoepassingen voor PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME in micro soft Edge en Internet Explorer 11 op Windows 10 toestaan [PLAYREADY SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) te worden aangeroepen op Windows 10-apparaten die dit ondersteunen. PlayReady SL3000 Hiermee ontgrendelt u de stroom van verbeterde premium-inhoud (4K, Kopregel) en nieuwe inhoud leveringsmodel (voor verbeterde inhoud).

PlayReady is om u te richten op de Windows-apparaten, de enige DRM in de hardware die beschikbaar zijn op Windows-apparaten (PlayReady SL3000). Een streamingservice kunt PlayReady via EME of via een Universal Windows Platform-toepassing gebruiken en bieden een hogere kwaliteit van de video met behulp van PlayReady SL3000 dan een andere DRM. Normaal gesproken inhoud tot maximaal 2 K stroomt het via Chrome of Firefox en inhoud omhoog naar 4 K-stromen via Microsoft Edge/Internet Explorer 11 of een Universal Windows Platform-toepassing op hetzelfde apparaat. Het bedrag is afhankelijk van instellingen en -implementatie.

#### <a name="use-eme-for-widevine"></a>EME voor Widevine gebruiken
Google Widevine is op een moderne browser met EME/Widevine-ondersteuning, zoals Chrome 41 + op Windows 10, Windows 8.1-, Mac OS x Yosemite en Chrome op Android 4.4.4 de DRM achter EME.

![EME voor Widevine gebruiken](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine die niet voorkomen dat u een schermopname van beveiligde video maken.

![Player-invoegtoepassingen voor Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Unentitled gebruikers
Gebruikers die geen lid is van de groep 'Gebruikers het recht', doorgeven niet de gebruiker de controle recht. De service van de multi-DRM-licenties is vervolgens weigert deze voor de aangevraagde licentie uitgeven, zoals wordt weergegeven. De gedetailleerde beschrijving is ' verkrijgen van licentie is mislukt, ' die is ontworpen.

![Unentitled gebruikers](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Een aangepaste service voor beveiligingstokens uitvoeren
Als u een aangepaste STS uitvoert, wordt door de aangepaste STS de JWT verleend met behulp van een symmetrische of een asymmetrische sleutel.

De volgende Schermafbeelding toont een scenario die gebruikmaakt van een symmetrische sleutel (met behulp van Chrome):

![Aangepaste STS met een symmetrische sleutel](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

De volgende schermafbeelding ziet u een scenario waarin een asymmetrische sleutel via een X509 maakt gebruik van het certificaat (met behulp van een moderne browser van Microsoft):

![Aangepaste STS met een asymmetrische sleutel](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

In beide van de vorige gevallen wordt blijft verificatie van de gebruiker hetzelfde. Dit vindt plaats via Azure AD. Het enige verschil is dat JWTs zijn uitgegeven door de aangepaste STS in plaats van Azure AD. Wanneer u dynamische CENC beveiliging configureert, is de beperking license delivery service Hiermee geeft u het type JWT, een symmetrische of een asymmetrische sleutel.

## <a name="summary"></a>Samenvatting

Dit document besproken CENC met multi systeemeigen DRM en toegangsbeheer via tokenverificatie, het ontwerp en de implementatie met behulp van Azure Media Services en Media Player.

* Een referentieontwerp werd weergegeven met alle vereiste onderdelen in een subsysteem DRM/CENC.
* Een referentie-implementatie is die wordt weergegeven op het Azure Media Services en Media Player.
* Sommige onderwerpen die rechtstreeks betrokken is bij het ontwerpen en implementeren, zijn ook besproken.

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de service voorwaarden en het privacybeleid van Google, Inc.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
 
