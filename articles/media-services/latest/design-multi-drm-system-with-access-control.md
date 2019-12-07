---
title: Ontwerp van een multi-DRM-inhouds beschermings systeem met toegangs beheer-Azure Media Services | Microsoft Docs
description: In deze artikelen vindt u een gedetailleerde beschrijving van het ontwerpen van een multi-DRM-inhouds beschermings systeem met Azure Media Services.
services: media-services
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2018
ms.author: willzhan
ms.custom: seodec18
ms.openlocfilehash: 00ddedf135d13c07e8abe1094dd5366acb0f4ae5
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896173"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>Ontwerp van een multi-DRM-beveiliging van inhoud-systeem met toegangsbeheer 

Het ontwerpen en bouwen van een subsysteem Digital Rights Management (DRM) voor een over-the-top (OTT) of online streaming-oplossing is een complexe taak. Operators/online uitbesteden video-providers meestal van deze taak op gespecialiseerde DRM-serviceproviders. Het doel van dit document is een referentieontwerp en een referentie-implementatie van een end-to-end DRM-subsysteem in een OTT of online streamingoplossing.

De betreffende lezers voor dit document zijn engineers die werken in DRM subsystemen van OTT of online streaming/Graphic; oplossingen of lezers die geïnteresseerd in DRM subsystemen zijn. Verondersteld wordt dat lezers bekend met ten minste één van de DRM-technologieën op de markt, zoals PlayReady, Widevine, FairPlay of Adobe-toegang bent.

In deze discussie door multi-DRM nemen we de 3 DRM's ondersteund door Azure Media Services: algemene versleuteling (CENC) voor PlayReady en Widevine, FairPlay, evenals de AES-128 clear key-versleuteling. Een belangrijke trend in online streaming en uit de branche OTT is het gebruik van systeemeigen DRM's op verschillende-clientplatforms. Deze trend is een overstap van de voorgaande build is die een enkele DRM en de client-SDK voor verschillende-clientplatforms gebruikt. Wanneer u CENC met multi systeemeigen DRM, zowel PlayReady als Widevine zijn versleuteld volgens de [Common Encryption (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) specificatie.

De voordelen van het gebruik van systeemeigen multi-DRM voor inhoudsbeveiliging zijn dat deze:

* Vermindert de kosten voor codering omdat een enkel versleutelingsproces wordt gebruikt om u te richten op verschillende platforms met de systeemeigen DRM's.
* Reduceert de kosten van het beheer van activa omdat slechts één exemplaar van actief in de opslag nodig is.
* DRM-client licentiekosten omdat de systeemeigen DRM-client meestal vrije ruimte op het eigen platform is elimineert.

### <a name="goals-of-the-article"></a>Doelstellingen van het artikel

De doelstellingen van dit artikel zijn:

* Geef een referentieontwerp van een DRM-subsysteem die gebruikmaakt van alle 3 DRM's (CENC voor DASH), FairPlay voor HLS en PlayReady voor smooth streaming.
* Geef een referentie-implementatie op Azure en Azure Media Services-platform.
* Sommige onderwerpen ontwerp en de implementatie besproken.

De volgende tabel geeft een overzicht van de systeemeigen DRM-ondersteuning op verschillende platforms en EME ondersteuning in verschillende browsers.

| **Clientplatform** | **Systeemeigen DRM** | **EME** |
| --- | --- | --- |
| **Smart-tv's, STB 's** | PlayReady, Widevine en/of andere | Ingesloten browser/EME voor PlayReady en/of Widevine|
| **Windows 10** | PlayReady | Micro soft Edge/IE11 voor PlayReady|
| **Android-apparaten (telefoon, tablet, tv-programma's)** |Widevine |Chrome for Widevine |
| **iOS** | FairPlay | Safari voor FairPlay (sinds het iOS 11.2) |
| **MacOS** | FairPlay | Safari voor FairPlay (sinds het Safari 9 + op Mac OS X 10.11 + El Capitan)|
| **tvOS** | FairPlay | |

Rekening houdend met de huidige status van de implementatie voor elke DRM, een service doorgaans wil implementeren twee of drie DRM's om ervoor te zorgen dat u alle typen van eindpunten in de beste manier kunt oplossen.

Er is een verschil tussen de complexiteit van de logica van de service en de complexiteit op de client tot een bepaalde mate van ervaring van de gebruiker op de verschillende clients.

Houd er rekening mee als u wilt een selectie hebt gemaakt:

* PlayReady is systeemeigen geïmplementeerd in elke Windows-apparaat, op bepaalde Android-apparaten en beschikbaar gesteld in software-SDK's op vrijwel elk platform.
* Widevine is systeemeigen geïmplementeerd in elke Android-apparaat, Chrome en sommige andere apparaten. Widevine wordt ook ondersteund in Firefox en Opera browsers via DASH.
* FairPlay is beschikbaar op iOS-, Mac OS- en tvOS.


## <a name="a-reference-design"></a>Een referentieontwerp
In deze sectie geeft een referentieontwerp die is agnostisch ten opzichte van de technologieën die worden gebruikt om dit te implementeren.

Een DRM-subsysteem kan de volgende onderdelen bevatten:

* Sleutelbeheer
* DRM-codering verpakking
* Levering van DRM-licentie
* Rechten van een selectievakje/toegangsbeheer
* Verificatie/autorisatie van gebruiker
* Player-app
* Oorsprong/content delivery network (CDN)

Het volgende diagram illustreert de op hoog niveau interactie tussen de onderdelen van een DRM-subsysteem:

![DRM-subsysteem met CENC](./media/design-multi-drm-system-with-access-control/media-services-generic-drm-subsystem-with-cenc.png)

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

| **ContentKey-naar-asset** | **Scenario** |
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

| **Bouwstenen** | **Technologie** |
| --- | --- |
| **Player** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **ID-Provider (IDP)** |Azure Active Directory (Azure AD) |
| **Secure Token Service (STS)** |Azure AD |
| **Werkstroom voor DRM-beveiliging** |Azure Media Services dynamische beveiliging |
| **Levering van DRM-licentie** |* Media Services-licentie leveringsmethode (PlayReady, Widevine, FairPlay) <br/>* Axinom licentieserver <br/>* Aangepaste PlayReady-licentieserver |
| **Oorsprong** |Azure Media Services streaming-eindpunt |
| **Sleutelbeheer** |Niet nodig voor de referentie-implementatie |
| **Inhoudsbeheer** |Een C#-consoletoepassing |

Met andere woorden, worden zowel de id-provider en de STS geleverd door Azure AD. De [API van Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/) wordt gebruikt voor de speler. Zowel Azure Media Services als Azure Media Player ondersteunen CENC via DASH, FairPlay via HLS, PlayReady over smooth streaming en AES-128-versleuteling voor DASH, HLS en Smooth.

Het volgende diagram toont de algemene structuur en de stroom met de vorige technologie-toewijzing:

![CENC op mediaservices](./media/design-multi-drm-system-with-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Als u inhoud DRM-beveiliging instelt, wordt het content management-hulpprogramma gebruikt de volgende invoer:

* Open inhoud
* Inhoudssleutel uit Sleutelbeheer
* Licentie-URL's voor aanschaf
* Een lijst met gegevens uit Azure AD, zoals de doelgroep, uitgever en token aanvragen

Hier volgt de uitvoer van het hulpprogramma voor inhoudsbeheer:

* ContentKeyPolicy beschrijft DRM-licentiesjabloon voor elk soort DRM gebruikt.
* ContentKeyPolicyRestriction beschrijving van het toegangsbeheer voordat een DRM-licentie is verleend
* Streamingpolicy beschrijft de verschillende combinaties van DRM - versleutelingsmodus - streaming-protocol - containerindeling voor streaming
* StreamingLocator beschrijft inhoud sleutel/IV gebruikt voor versleuteling en streaming-URL 's 

Dit is de stroom tijdens runtime:

* Bij verificatie van de gebruiker wordt een JWT gegenereerd.
* Een van de claims in de JWT is een claim van groepen die het groepsobject-ID EntitledUserGroup bevat. Deze claim wordt gebruikt om door te geven van de rechten van een selectievakje.
* De speler downloadt de client het manifest van CENC beschermde inhoud en identificeert het volgende:
   * Sleutel-ID.
   * De inhoud is beveiligd door DRM.
   * URL's aanschaf van licentie.
* De speler wordt een licentieaanvraag ophalen op basis van de browser/DRM ondersteund. In de licentie overname aanvraag, de sleutel-ID en de JWT worden ook verzonden. De service voor het leveren van licenties controleert of de JWT- en de claims voordat de benodigde licentie.

## <a name="implementation"></a>Implementatie
### <a name="implementation-procedures"></a>Procedures voor implementatie
Implementatie omvat de volgende stappen uit:

1. Bereid test activa. Een video naar multi-bitrate test coderen/pakket gefragmenteerd MP4 in Media Services. Deze asset is *niet* DRM beveiligd. DRM-bescherming wordt geboden door dynamische beveiliging later opnieuw.

2. Maak een sleutel-ID en een inhoudssleutel (eventueel van een sleutel seed). In dit geval de sleutelbeheersysteem is niet nodig omdat alleen één sleutel-ID en sleutel zijn vereist voor een aantal test activa.

3. Gebruik de API van Media Services om te configureren van multi-DRM-licentie levering van services voor de test-asset. Als u aangepaste licentieservers door uw bedrijf of van uw bedrijf leveranciers in plaats van licentie-services in Media Services gebruikt, kunt u deze stap overslaan. Wanneer u licentielevering configureert, kunt u licentie overname URL's in de stap. De API van Media Services is nodig om op te geven van sommige. gedetailleerde configuraties, zoals autorisatie-beleidsbeperking en licentie antwoord sjablonen voor verschillende services van DRM-licentie. Op dit moment biedt de Azure-portal niet de benodigde gebruikersinterface voor deze configuratie. Zie voor informatie van de API-niveau en voorbeeldcode [met PlayReady en/of Widevine dynamic common encryption](protect-with-drm.md).

4. De API van Media Services gebruiken om te configureren van het leveringsbeleid voor Assets voor de test-asset. Zie voor informatie van de API-niveau en voorbeeldcode [met PlayReady en/of Widevine dynamic common encryption](protect-with-drm.md).

5. Maken en configureren van een Azure AD-tenant in Azure.

6. Maak een paar gebruikersaccounts en groepen in uw Azure AD-tenant. Ten minste een 'Gebruiker het recht'-groep maken en een gebruiker toevoegen aan deze groep. De controle van de rechten van doorgeven gebruikers in deze groep in verwerving van licenties. Gebruikers die niet in deze groep om door te geven van de controle mislukt en kunnen niet een licentie. Lidmaatschap van deze groep 'Gebruiker het recht' is een claim van de vereiste groepen in de JWT dat is uitgegeven door Azure AD. U geeft deze vereiste claim in de stap bij het configureren van multi-DRM-services voor het leveren van licenties.

7. Maak een ASP.NET MVC-app voor het hosten van uw video speler. Deze ASP.NET-app is beveiligd met verificatie op basis van de Azure AD-tenant. Juiste claims zijn opgenomen in de toegangstokens verkregen na verificatie van de gebruiker. We raden OpenID Connect-API voor deze stap. Installeer de volgende NuGet-pakketten:

   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

8. Een speler maken met behulp van de [API van Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/). Gebruik de [API van Azure Media Player ProtectionInfo](https://amp.azure.net/libs/amp/latest/docs/) om op te geven welke DRM-technologie kunt gebruiken voor verschillende DRM-platforms.

9. De volgende tabel ziet u de testmatrix.

    | **DRM** | **Browser** | **Resultaat van de gebruiker heeft het recht** | **Resultaat van unentitled gebruiker** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge of Internet Explorer 11 op Windows 10 |Voltooid |Mislukt |
    | **Widevine** |Chrome, Firefox, Opera |Voltooid |Mislukt |
    | **FairPlay** |Safari op Mac OS      |Voltooid |Mislukt |
    | **AES-128** |De meeste moderne browsers  |Voltooid |Mislukt |

Zie voor meer informatie over het instellen van Azure AD voor een ASP.NET MVC-app-speler [een op basis van een Azure Media Services OWIN MVC-app integreren met Azure Active Directory en beperken de levering van inhoud sleutel op basis van claims JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Zie voor meer informatie, [JWT-tokenverificatie in Azure Media Services en dynamische versleuteling](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

Voor informatie over Azure AD:

* U vindt informatie voor ontwikkelaars in de [ontwikkelaarsgids van Azure Active Directory](../../active-directory/develop/v1-overview.md).
* U vindt informatie voor beheerders in [uw Azure AD-tenant-directory beheren](../../active-directory/fundamentals/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Sommige problemen in uitvoering

Gebruik de volgende informatie voor probleemoplossing voor hulp bij problemen met implementatie.

* De verlener URL met eindigen moet '/'. De doelgroep moet de speler toepassing client-ID. Ook toe te voegen '/' aan het einde van de URL-verlener.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    In de [JWT Decoder](http://jwt.calebb.net/), ziet u **aud** en **iss**, zoals weergegeven in de JWT:

    ![JWT](./media/design-multi-drm-system-with-access-control/media-services-1st-gotcha.png)

* Machtigingen voor de toepassing in Azure AD toevoegen aan de **configureren** tabblad van de toepassing. Machtigingen zijn vereist voor elke toepassing, lokale en geïmplementeerde versies.

    ![Machtigingen](./media/design-multi-drm-system-with-access-control/media-services-perms-to-other-apps.png)

* Gebruik de juiste verlener bij het instellen van dynamische CENC beveiliging.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    De volgende werkt niet:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    De GUID is de Azure AD-tenant-ID. De GUID kan worden gevonden de **eindpunten** pop-upmenu in Azure portal.

* Lidmaatschap van verlenen claims bevoegdheden. Zorg ervoor dat het volgende in het manifestbestand van de Azure AD-toepassing: 

    "groupMembershipClaims": "Alle" (de standaardwaarde is null)

* Stel de juiste TokenType bij het maken van beperking vereisten.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Omdat u ondersteuning voor JWT (Azure AD) naast SWT (ACS) toevoegt, is de standaardwaarde TokenType TokenType.JWT. Als u SWT/ACS gebruikt, moet u het token instellen op TokenType.SWT.

## <a name="the-completed-system-and-test"></a>Het voltooide systeem en de test

In dit gedeelte leidt u door de volgende scenario's in het voltooide end-to-end-systeem zodat u een eenvoudige beeld van het gedrag hebben kunt voordat u een account aanmelden:

* Als u een niet-geïntegreerd scenario moet:

    * Voor video activa die worden gehost in Media Services die een onbeveiligd of DRM beveiligd maar zonder tokenverificatie (een licentie verlenen aan iedereen die deze aangevraagd), kunt u deze testen zonder dat u aangemeld. Schakel over naar HTTP als uw video-streaming via HTTP.

* Als u een geïntegreerde end-to-end-scenario moet:

    * Voor videomedia onder de dynamische DRM-beveiliging in Media Services met tokenverificatie en JWT die worden gegenereerd door Azure AD, moet u zich aanmeldt.

Zie voor de speler web-App en de aanmelding [deze website](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Gebruikersaanmelding
Als u wilt testen van het end-to-end geïntegreerde DRM-systeem, moet u hebt een account te maken of toegevoegd.

Welk account?

Hoewel Azure oorspronkelijk alleen toegang Microsoft-accountgebruikers, wordt toegang is nu toegestaan door gebruikers van beide systemen. Alle Azure-eigenschappen vertrouwt nu Azure AD voor verificatie en Azure AD verifieert de organisatie-gebruikers. Een federatieve relatie is gemaakt waarin Azure AD vertrouwt voor het Microsoft-account klantidentiteitssysteem om klantgebruikers te verifiëren. Azure AD kan als gevolg hiervan gastaccounts Microsoft ook als systeemeigen Azure AD-accounts verifiëren.

Omdat Azure AD het domein van Microsoft-account wordt vertrouwd, kunt u alle accounts uit een van de volgende domeinen naar de aangepaste Azure AD-tenant en het account gebruiken voor aanmelding bij toevoegen:

| **Domeinnaam** | **Domein** |
| --- | --- |
| **Aangepast Azure AD-tenantdomein** |somename.onmicrosoft.com |
| **Bedrijfsdomein** |Microsoft.com |
| **Domein van Microsoft-account** |Outlook.com, live.com, hotmail.com |

U kunt contact opnemen met een van de auteurs hebben een account te maken of toegevoegd voor u.

De volgende schermafbeeldingen tonen verschillende aanmelden pagina's die worden gebruikt door verschillende domeinaccounts:

**Aangepaste Azure AD-tenant domeinaccount**: de aangepaste aanmeldingspagina van de aangepaste Azure AD tenant-domein.

![Aangepast Azure AD-tenant domeinaccount een](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain1.png)

**Microsoft-domeinaccount met smartcard**: aangepast door Microsoft zakelijke pagina IT met tweeledige verificatie.

![Aangepast Azure AD-tenant-domeinaccount twee](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain2.png)

**Microsoft-account**: de aanmeldingspagina van het Microsoft-account voor consumenten.

![Aangepast Azure AD-tenant domeinaccount drie](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Versleutelde Media-extensies gebruiken voor PlayReady

PlayReady is op een moderne browser met Encrypted Media Extensions (EME) voor PlayReady-ondersteuning, zoals Internet Explorer 11 op Windows 8.1 of hoger en Microsoft Edge-browser op Windows 10, de onderliggende DRM voor EME.

![Gebruik EME voor PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready1.png)

Het donkere player-gebied is omdat de PlayReady-bescherming wordt voorkomen dat u een schermopname van beveiligde video maken.

De volgende schermafbeelding ziet u de player-invoegtoepassingen en Microsoft Security Essentials (MSE) / EME ondersteuning voor:

![Player-invoegtoepassingen voor PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready2.png)

EME in Microsoft Edge en Internet Explorer 11 op Windows 10 kunt [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) moet worden aangeroepen op Windows 10-apparaten die dit ondersteunen. PlayReady SL3000 Hiermee ontgrendelt u de stroom van verbeterde premium-inhoud (4K, Kopregel) en nieuwe inhoud leveringsmodel (voor verbeterde inhoud).

PlayReady is om u te richten op de Windows-apparaten, de enige DRM in de hardware die beschikbaar zijn op Windows-apparaten (PlayReady SL3000). Een streamingservice kunt PlayReady via EME of via een Universal Windows Platform-toepassing gebruiken en bieden een hogere kwaliteit van de video met behulp van PlayReady SL3000 dan een andere DRM. Normaal gesproken inhoud tot maximaal 2 K stroomt het via Chrome of Firefox en inhoud omhoog naar 4 K-stromen via Microsoft Edge/Internet Explorer 11 of een Universal Windows Platform-toepassing op hetzelfde apparaat. Het bedrag is afhankelijk van instellingen en -implementatie.

#### <a name="use-eme-for-widevine"></a>EME voor Widevine gebruiken

Google Widevine is op een moderne browser met EME/Widevine-ondersteuning, zoals Chrome 41 + op Windows 10, Windows 8.1-, Mac OS x Yosemite en Chrome op Android 4.4.4 de DRM achter EME.

![EME voor Widevine gebruiken](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine1.png)

Widevine die niet voorkomen dat u een schermopname van beveiligde video maken.

![Player-invoegtoepassingen voor Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>Gebruik EME voor FairPlay

Op deze manier kunt u FairPlay beveiligde inhoud testen in de test-speler in Safari op Mac OS- of iOS 11.2 en hoger.

Zorg ervoor dat u plaatst 'FairPlay' als protectionInfo.type en in de juiste URL voor het certificaat voor uw toepassing in FPS AC pad (FairPlay Streaming toepassing certificaat).

### <a name="unentitled-users"></a>Unentitled gebruikers

Gebruikers die geen lid is van de groep 'Gebruikers het recht', doorgeven niet de gebruiker de controle recht. De service van de multi-DRM-licenties is vervolgens weigert deze voor de aangevraagde licentie uitgeven, zoals wordt weergegeven. De gedetailleerde beschrijving is ' verkrijgen van licentie is mislukt, ' die is ontworpen.

![Unentitled gebruikers](./media/design-multi-drm-system-with-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Een aangepaste service voor beveiligingstokens uitvoeren

Als u een aangepaste STS uitvoert, wordt door de aangepaste STS de JWT verleend met behulp van een symmetrische of een asymmetrische sleutel.

De volgende Schermafbeelding toont een scenario die gebruikmaakt van een symmetrische sleutel (met behulp van Chrome):

![Aangepaste STS met een symmetrische sleutel](./media/design-multi-drm-system-with-access-control/media-services-running-sts1.png)

De volgende schermafbeelding ziet u een scenario waarin een asymmetrische sleutel via een X509 maakt gebruik van het certificaat (met behulp van een moderne browser van Microsoft):

![Aangepaste STS met een asymmetrische sleutel](./media/design-multi-drm-system-with-access-control/media-services-running-sts2.png)

In beide van de vorige gevallen wordt blijft verificatie van de gebruiker hetzelfde. Dit vindt plaats via Azure AD. Het enige verschil is dat JWTs zijn uitgegeven door de aangepaste STS in plaats van Azure AD. Wanneer u dynamische CENC beveiliging configureert, is de beperking license delivery service Hiermee geeft u het type JWT, een symmetrische of een asymmetrische sleutel.

## <a name="next-steps"></a>Volgende stappen

* [Veelgestelde vragen](frequently-asked-questions.md)
* [Overzicht van inhoudsbeveiliging](content-protection-overview.md)
* [Beveiligen van uw inhoud met DRM](protect-with-drm.md)
