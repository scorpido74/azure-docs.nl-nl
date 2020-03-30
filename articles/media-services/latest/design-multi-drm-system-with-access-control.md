---
title: Een multi-DRM-contentbeveiligingssysteem - Azure Media Services v3
description: In deze artikelen vindt u een gedetailleerde beschrijving van het ontwerpen van een multi-DRM-inhoudsbeveiligingssysteem met Azure Media Services.
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
ms.openlocfilehash: fbc6d6fa8f9a3b424eaec1f04a61b5ca24fe14fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161780"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>Ontwerp van een inhoudsbeveiligingssysteem van een multi-DRM met toegangsbeheer 

Het ontwerpen en bouwen van een DRM-subsysteem (Digital Rights Management) voor een over-the-top (OTT) of online streaming oplossing is een complexe taak. Operators/online videoproviders besteden deze taak doorgaans uit aan gespecialiseerde DRM-serviceproviders. Het doel van dit document is om een referentieontwerp en een referentie-implementatie van een end-to-end DRM-subsysteem te presenteren in een OTT- of online streaming-oplossing.

De beoogde lezers voor dit document zijn ingenieurs die werken in DRM subsystemen van OTT of online streaming / multiscreen oplossingen of lezers die geïnteresseerd zijn in DRM subsystemen. De veronderstelling is dat lezers bekend zijn met ten minste één van de DRM-technologieën op de markt, zoals PlayReady, Widevine, FairPlay of Adobe Access.

In deze discussie, door multi-DRM omvatten we de 3 DRM's ondersteund door Azure Media Services: Common Encryption (CENC) voor PlayReady en Widevine, FairPlay evenals AES-128 clear key encryptie. Een belangrijke trend in online streaming en de OTT-industrie is het gebruik van native DRM's op verschillende clientplatforms. Deze trend is een verschuiving ten opzichte van de vorige die een enkele DRM en zijn client SDK gebruikt voor verschillende clientplatforms. Wanneer u CENC gebruikt met multi-native DRM, worden zowel PlayReady als Widevine versleuteld volgens de [Common Encryption (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) specificatie.

De voordelen van het gebruik van native multi-DRM voor content bescherming zijn dat het:

* Verlaagt de coderingskosten omdat één versleutelingsproces wordt gebruikt om verschillende platforms met zijn eigen DRM's te targeten.
* Vermindert de kosten van het beheer van activa omdat slechts één exemplaar van het actief nodig is in de opslag.
* Elimineert drm-clientlicentiekosten omdat de native DRM-client meestal gratis is op zijn eigen platform.

### <a name="goals-of-the-article"></a>Doelstellingen van het artikel

De doelstellingen van dit artikel zijn:

* Zorg voor een referentieontwerp van een DRM-subsysteem dat alle 3 DRM's (CENC voor DASH, FairPlay voor HLS en PlayReady gebruikt voor vloeiende streaming).
* Geef een referentie-implementatie op azure- en Azure Media Services-platform.
* Bespreek een aantal ontwerp- en implementatieonderwerpen.

In de volgende tabel worden de native DRM-ondersteuning op verschillende platforms en EME-ondersteuning in verschillende browsers samengevat.

| **Client platform** | **Native DRM** | **Eme** |
| --- | --- | --- |
| **Smart TV's, SB's** | PlayReady, Widevine en/of andere | Ingesloten browser/EME voor PlayReady en/of Widevine|
| **Windows 10** | PlayReady | Microsoft Edge/IE11 voor PlayReady|
| **Android-apparaten (telefoon, tablet, tv)** |Widevine |Chrome voor Widevine |
| **iOS** | FairPlay | Safari voor FairPlay (sinds iOS 11.2) |
| **Macos** | FairPlay | Safari voor FairPlay (sinds Safari 9+ op Mac OS X 10.11+ El Capitan)|
| **tvOS (tvOS)** | FairPlay | |

Gezien de huidige status van implementatie voor elke DRM, wil een service doorgaans twee of drie DRM's implementeren om ervoor te zorgen dat u alle typen eindpunten op de beste manier aanpakt.

Er is een afweging tussen de complexiteit van de servicelogica en de complexiteit aan de clientzijde om een bepaald niveau van gebruikerservaring op de verschillende klanten te bereiken.

Houd er rekening mee dat u wilt dat uw selectie wordt gemaakt:

* PlayReady is native geïmplementeerd in elk Windows-apparaat, op sommige Android-apparaten, en beschikbaar via software SDKs op vrijwel elk platform.
* Widevine is native geïmplementeerd in elk Android-apparaat, in Chrome en in sommige andere apparaten. Widevine wordt ook ondersteund in Firefox en Opera browsers via DASH.
* FairPlay is beschikbaar op iOS, macOS en tvOS.


## <a name="a-reference-design"></a>Een referentieontwerp
Deze sectie presenteert een referentieontwerp dat agnost is naar de technologieën die worden gebruikt om het te implementeren.

Een DRM-subsysteem kan de volgende componenten bevatten:

* Sleutelbeheer
* DRM-versleutelingsverpakking
* Levering van DRM-licentie
* Controle/toegangscontrole van rechten
* Gebruikersverificatie/autorisatie
* Player-app
* Origin/content delivery network (CDN)

Het volgende diagram illustreert de interactie op hoog niveau tussen de componenten in een DRM-subsysteem:

![DRM-subsysteem met CENC](./media/design-multi-drm-system-with-access-control/media-services-generic-drm-subsystem-with-cenc.png)

Het ontwerp heeft drie basislagen:

* Een back-officelaag (zwart) wordt niet extern belicht.
* Een DMZ-laag (donkerblauw) bevat alle eindpunten die het publiek onder ogen zien.
* Een openbare internetlaag (lichtblauw) bevat het CDN en spelers met verkeer over het openbare internet.

Er moet ook een content management tool om DRM bescherming te controleren, ongeacht of het statische of dynamische encryptie. De ingangen voor DRM-versleuteling zijn:

* MBR-video-inhoud
* Inhoudstoets
* URL's voor licentieverwerving

Hier is de stroom op hoog niveau tijdens de afspeeltijd:

* De gebruiker is geverifieerd.
* Er wordt een autorisatietoken gemaakt voor de gebruiker.
* DRM beschermde inhoud (manifest) wordt gedownload naar de speler.
* De speler dient een aanvraag voor licentieverwerving in om servers in licentie te geven, samen met een sleutel-ID en een autorisatietoken.

In het volgende gedeelte wordt het ontwerp van sleutelbeheer besproken.

| **ContentKey-to-asset** | **Scenario** |
| --- | --- |
| 1-op-1 |Het eenvoudigste geval. Het biedt de beste controle. Maar deze regeling resulteert over het algemeen in de hoogste licentieleveringskosten. Ten minste is één licentieaanvraag vereist voor elk beschermd actief. |
| 1-op-veel |U dezelfde inhoudssleutel gebruiken voor meerdere elementen. Voor alle elementen in een logische groep, zoals een genre of de subset van een genre (of filmgen), u bijvoorbeeld één inhoudssleutel gebruiken. |
| Veel-op-1 |Voor elk item zijn meerdere inhoudssleutels nodig. <br/><br/>Als u bijvoorbeeld dynamische CENC-beveiliging moet toepassen met multi-DRM voor MPEG-DASH en dynamische AES-128-versleuteling voor HLS, hebt u twee afzonderlijke inhoudssleutels nodig. Elke inhoudssleutel heeft een eigen ContentKeyType nodig. (Voor de inhoudssleutel die wordt gebruikt voor dynamische CENC-beveiliging, gebruikt u ContentKeyType.CommonEncryption. Gebruik ContentKeyType.EnvelopeEncryption voor de inhoudssleutel die wordt gebruikt voor dynamische AES-128-versleuteling.)<br/><br/>Als ander voorbeeld u in CENC-bescherming van DASH-inhoud in theorie één inhoudssleutel gebruiken om de videostream en een andere inhoudssleutel te beschermen om de audiostream te beschermen. |
| Veel-op-veel |Combinatie van de vorige twee scenario's. Voor elk van de meerdere elementen in dezelfde activagroep wordt één set inhoudssleutels gebruikt. |

Een andere belangrijke factor om te overwegen is het gebruik van persistente en niet-persistente licenties.

Waarom zijn deze overwegingen belangrijk?

Als u een openbare cloud gebruikt voor licentielevering, hebben persistente en niet-persistente licenties een directe invloed op de kosten voor licentielevering. De volgende twee verschillende ontwerpcases illustreren:

* Maandelijks abonnement: gebruik een permanente licentie en 1-op-veel content key-to-asset mapping. Voor alle kinderfilms gebruiken we bijvoorbeeld één contentsleutel voor versleuteling. In dat geval:

    Totaal aantal aangevraagde licenties voor films/apparaten voor alle kinderen = 1

* Maandelijks abonnement: gebruik een niet-permanente licentie en 1-op-1-toewijzing tussen inhoudssleutel en asset. In dat geval:

    Totaal aantal aangevraagde licenties voor films/apparaten voor alle kinderen = [aantal films bekeken] x [aantal sessies]

De twee verschillende ontwerpen resulteren in zeer verschillende licentie aanvraag patronen. De verschillende patronen leiden tot verschillende licentieleveringskosten als licentieleveringsservice wordt geleverd door een openbare cloud zoals Media Services.

## <a name="map-design-to-technology-for-implementation"></a>Kaartontwerp naar technologie voor implementatie
Vervolgens wordt het generieke ontwerp toegewezen aan technologieën op het Azure/Media Services-platform door aan te geven welke technologie voor elke bouwsteen moet worden gebruikt.

In de volgende tabel ziet u de toewijzing.

| **Bouwsteen** | **Technologie** |
| --- | --- |
| **Speler** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **Identity Provider (IDP)** |Azure Active Directory (Azure AD) |
| **Secure Token Service (STS)** |Azure AD |
| **DRM-beveiligingswerkstroom** |Dynamische beveiliging van Azure Media Services |
| **Levering van DRM-licentie** |* Media Services licentie levering (PlayReady, Widevine, FairPlay) <br/>* Axinom licentie server <br/>* Aangepaste PlayReady licentieserver |
| **Oorsprong** |Eindpunt voor het streamen van Azure Media Services |
| **Sleutelbeheer** |Niet nodig voor referentie-implementatie |
| **Inhoudsbeheer** |Een C#-consoletoepassing |

Met andere woorden, zowel IDP als STS worden geleverd door Azure AD. De [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/) wordt gebruikt voor de speler. Zowel Azure Media Services als Azure Media Player ondersteunen CENC via DASH, FairPlay over HLS, PlayReady via vloeiende streaming en AES-128-versleuteling voor DASH, HLS en soepel.

In het volgende diagram ziet u de algemene structuur en stroom met de vorige technologietoewijzing:

![CENC op Media Services](./media/design-multi-drm-system-with-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Om DRM-inhoudsbescherming in te stellen, gebruikt het hulpprogramma voor inhoudsbeheer de volgende ingangen:

* Inhoud openen
* Inhoudssleutel van sleutelbeheer
* URL's voor licentieverwerving
* Een lijst met informatie van Azure AD, zoals doelgroep-, emittent- en tokenclaims

Hier is de uitvoer van de content management tool:

* ContentKeyPolicy beschrijft DRM-licentiesjabloon voor elk type DRM dat wordt gebruikt;
* ContentKeyPolicyRestriction beschrijft het toegangscontrole voordat een DRM-licentie wordt uitgegeven
* Streamingpolicy beschrijft de verschillende combinaties van DRM - encryptiemodus - streaming protocol - containerformaat, voor streaming
* StreamingLocator beschrijft inhoudssleutel/IV die wordt gebruikt voor versleuteling en streaming-URL's 

Hier is de stroom tijdens runtime:

* Bij gebruikersverificatie wordt een JWT gegenereerd.
* Een van de claims in de JWT is een groepsclaim die de groepsobject-id GetiteldUserGroup bevat. Deze claim wordt gebruikt om de rechtencontrole te doorstaan.
* De speler downloadt het clientmanifest van door CENC beveiligde inhoud en identificeert het volgende:
   * Sleutel-ID.
   * De inhoud is DRM-beveiligd.
   * URL's voor licentieverwerving.
* De speler maakt een licentie verwerving verzoek op basis van de browser / DRM ondersteund. In de aanvraag voor het verkrijgen van licenties worden ook de sleutel-ID en de JWT ingediend. De licentieleveringsservice verifieert de JWT en de claims voordat deze de benodigde licentie uitgeeft.

## <a name="implementation"></a>Implementatie
### <a name="implementation-procedures"></a>Uitvoeringsprocedures
Implementatie omvat de volgende stappen:

1. Testassets voorbereiden. Encode/package a test video to multi-bitrate fragmented MP4 in Media Services. Dit actief is *niet* DRM-beschermd. DRM bescherming wordt gedaan door dynamische bescherming later.

2. Maak een sleutel-id en een inhoudssleutel (optioneel uit een sleutelzaad). In dit geval is het sleutelbeheersysteem niet nodig omdat er slechts één sleutel-id en inhoudssleutel nodig zijn voor een paar testelementen.

3. Gebruik de Media Services API om multi-DRM-licentieleveringsservices voor het testitem te configureren. Als u aangepaste licentieservers gebruikt van uw bedrijf of de leveranciers van uw bedrijf in plaats van licentieservices in Media Services, u deze stap overslaan. U url's voor licentieverwerving opgeven in de stap wanneer u licentielevering configureert. De Media Services API is nodig om een aantal gedetailleerde configuraties op te geven, zoals autorisatiebeleidsbeperking en licentieresponssjablonen voor verschillende DRM-licentieservices. Op dit moment biedt de Azure-portal niet de benodigde gebruikersinterface voor deze configuratie. Zie [PlayReady en/of Widevine dynamic common encryption gebruiken](protect-with-drm.md)voor informatie op API-niveau en voorbeeldcode.

4. Gebruik de Media Services API om het beleid voor het leveren van activa voor het testitem te configureren. Zie [PlayReady en/of Widevine dynamic common encryption gebruiken](protect-with-drm.md)voor informatie op API-niveau en voorbeeldcode.

5. Een Azure AD-tenant maken en configureren in Azure.

6. Maak een paar gebruikersaccounts en -groepen in uw Azure AD-tenant. Maak ten minste een groep 'Gebruiker met recht' en voeg een gebruiker toe aan deze groep. Gebruikers in deze groep slagen voor het verkrijgen van een licentie. Gebruikers die niet in deze groep zitten, slagen niet voor de verificatiecontrole en kunnen geen licentie verkrijgen. Lidmaatschap van deze groep 'Gebruiker' is een vereiste groepsclaim in de JWT die is uitgegeven door Azure AD. U geeft deze claimvereiste op in de stap wanneer u services voor multi-DRM-licentieservices configureert.

7. Maak een ASP.NET MVC-app om je videospeler te hosten. Deze ASP.NET-app is beveiligd met gebruikersverificatie tegen de Azure AD-tenant. De juiste claims zijn opgenomen in de toegangstokens die zijn verkregen na gebruikersverificatie. We raden OpenID Connect API aan voor deze stap. Installeer de volgende NuGet-pakketten:

   * Microsoft.Azure.ActiveDirectory.GraphClient installeren
   * Installatiepakket Microsoft.Owin.Security.OpenIdConnect
   * Installatiepakket Microsoft.Owin.Security.Cookies
   * Installatiepakket Microsoft.Owin.Host.SystemWeb
   * Installatiepakket Microsoft.IdentityModel.Clients.ActiveDirectory

8. Maak een speler met behulp van de [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/). Gebruik de [Azure Media Player ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) om aan te geven welke DRM-technologie op verschillende DRM-platforms moet worden gebruikt.

9. In de volgende tabel ziet u de testmatrix.

    | **Drm** | **Browser** | **Resultaat voor de rechthebbende gebruiker** | **Resultaat voor gebruiker zonder recht** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge of Internet Explorer 11 op Windows 10 |Slagen |Mislukt |
    | **Widevine** |Chrome, Firefox, Opera |Slagen |Mislukt |
    | **FairPlay** |Safari op macOS      |Slagen |Mislukt |
    | **AES-128** |De meeste moderne browsers  |Slagen |Mislukt |

Zie [Een Azure Media Services OWIN MVC-app integreren met Azure Active Directory en beperking van de weergave van inhoudssleutels op basis van JWT-claims](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)voor informatie over het instellen van Azure AD voor een ASP.NET MVC-speler- app.

Zie [JWT-tokenverificatie in Azure Media Services en dynamische versleuteling](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)voor meer informatie.  

Voor informatie over Azure AD:

* U informatie over ontwikkelaars vinden in de [handleiding van Azure Active Directory-ontwikkelaars.](../../active-directory/develop/v2-overview.md)
* U beheerdersgegevens vinden in [Uw Azure AD-tenantmap beheren.](../../active-directory/fundamentals/active-directory-administer.md)

### <a name="some-issues-in-implementation"></a>Enkele kwesties bij de uitvoering

Gebruik de volgende informatie over probleemoplossing voor hulp bij implementatieproblemen.

* De URL van de uitgever moet eindigen met "/". Het publiek moet de client-id van de spelertoepassing zijn. Voeg ook "/" toe aan het einde van de URL van de uitgever.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    In de [JWT Decoder](http://jwt.calebb.net/), zie je **aud** en **iss,** zoals weergegeven in de JWT:

    ![JWT JWT](./media/design-multi-drm-system-with-access-control/media-services-1st-gotcha.png)

* Machtigingen toevoegen aan de toepassing in Azure AD op het tabblad **Configureren** van de toepassing. Machtigingen zijn vereist voor elke toepassing, zowel lokale als geïmplementeerde versies.

    ![Machtigingen](./media/design-multi-drm-system-with-access-control/media-services-perms-to-other-apps.png)

* Gebruik de juiste uitgever wanneer u dynamische CENC-beveiliging instelt.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    Het volgende werkt niet:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    De GUID is de Azure AD-tenant-id. De GUID is te vinden in het pop-upmenu **Eindpunten** in de Azure-portal.

* Subsidie groep lidmaatschap claims privileges. Controleer of dit het volgende in het manifestbestand van azure AD-toepassingen staat: 

    "groupMembershipClaims": "Alles" (de standaardwaarde is null)

* Stel het juiste TokenType in wanneer u beperkingen maakt.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Omdat u naast SWT (ACS) ondersteuning voor JWT (Azure AD) toevoegt, is de standaard TokenType TokenType.JWT. Als u SWT/ACS gebruikt, moet u het token instellen op TokenType.SWT.

## <a name="the-completed-system-and-test"></a>Het voltooide systeem en de test

In deze sectie vindt u de volgende scenario's in het voltooide end-to-end-systeem, zodat u een basisbeeld van het gedrag hebben voordat u een aanmeldingsaccount krijgt:

* Als u een niet-geïntegreerd scenario nodig hebt:

    * Voor video-assets die worden gehost in Media Services die onbeschermd of DRM-beveiligd zijn, maar zonder tokenverificatie (het uitgeven van een licentie aan degene die daarom heeft gevraagd), u deze testen zonder u aan te melden. Schakel over naar HTTP als uw videostreaming via HTTP is.

* Als u een end-to-end geïntegreerd scenario nodig hebt:

    * Voor videoassets onder dynamische DRM-beveiliging in Media Services, waarbij de tokenverificatie en JWT zijn gegenereerd door Azure AD, moet u zich aanmelden.

Voor de speler webapplicatie en de aanmelding, zie [deze website](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Gebruikersaanmelding
Om het end-to-end geïntegreerde DRM-systeem te testen, moet u een account hebben gemaakt of toegevoegd.

Welke rekening?

Hoewel Azure oorspronkelijk alleen toegang toestond voor Gebruikers van Microsoft-accountgebruikers, is toegang nu toegestaan door gebruikers van beide systemen. Alle Azure-eigenschappen vertrouwen nu op Azure AD voor verificatie en Azure AD verifieert organisatiegebruikers. Er is een federatierelatie gemaakt waarin Azure AD het consumentenidentiteitssysteem van het Microsoft-account vertrouwt om gebruikers van consumenten te verifiëren. Als gevolg hiervan kan Azure AD microsoft-accounts en native Azure AD-accounts verifiëren.

Omdat Azure AD het Microsoft-accountdomein vertrouwt, u alle accounts van een van de volgende domeinen toevoegen aan de aangepaste Azure AD-tenant en het account gebruiken om u aan te melden:

| **Domeinnaam** | **Domain** |
| --- | --- |
| **Aangepast Azure AD-tenantdomein** |somename.onmicrosoft.com |
| **Bedrijfsdomein** |microsoft.com |
| **Microsoft-accountdomein** |outlook.com, live.com, hotmail.com |

U contact opnemen met een van de auteurs om een account voor u te laten maken of toevoegen.

De volgende schermafbeeldingen tonen verschillende aanmeldingspagina's die door verschillende domeinaccounts worden gebruikt:

**Aangepast Azure AD-tenantdomeinaccount:** de aangepaste aanmeldingspagina van het aangepaste Azure AD-tenantdomein.

![Aangepaste Azure AD-tenantdomeinaccount](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain1.png)

**Microsoft-domeinaccount met smartcard:** de aanmeldingspagina die is aangepast door Microsoft corporate IT met tweestapsverificatie.

![Aangepast Azure AD-tenantdomeinaccount twee](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain2.png)

**Microsoft-account**: de aanmeldingspagina van het Microsoft-account voor consumenten.

![Aangepast Azure AD-tenantdomeinaccount drie](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Versleutelde media-extensies gebruiken voor PlayReady

In een moderne browser met EME-ondersteuning (Encrypted Media Extensions) voor PlayReady, zoals Internet Explorer 11 op Windows 8.1 of hoger en Microsoft Edge-browser op Windows 10, is PlayReady de onderliggende DRM voor EME.

![EME gebruiken voor PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready1.png)

Het gebied voor donkere spelers is omdat PlayReady-beveiliging voorkomt dat u een schermopname van beveiligde video maakt.

De volgende screenshot toont de plug-ins voor spelers en Microsoft Security Essentials (MSE)/EME-ondersteuning:

![Plug-ins voor spelers voor PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready2.png)

Met EME in Microsoft Edge en Internet Explorer 11 op Windows 10 kan [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) worden aangeroepen op Windows 10-apparaten die het ondersteunen. PlayReady SL3000 ontgrendelt de stroom van verbeterde premium content (4K, HDR) en nieuwe content delivery modellen (voor verbeterde content).

Om zich te concentreren op de Windows-apparaten, PlayReady is de enige DRM in de hardware beschikbaar op Windows-apparaten (PlayReady SL3000). Een streamingdienst kan PlayReady via EME of via een Universal Windows Platform-toepassing gebruiken en een hogere videokwaliteit bieden door PlayReady SL3000 te gebruiken dan een andere DRM. Inhoud tot 2K stroomt meestal via Chrome of Firefox en inhoud tot 4K loopt via Microsoft Edge/Internet Explorer 11 of een Universele Windows Platform-toepassing op hetzelfde apparaat. Het bedrag is afhankelijk van service-instellingen en implementatie.

#### <a name="use-eme-for-widevine"></a>EME gebruiken voor Widevine

In een moderne browser met EME/Widevine-ondersteuning, zoals Chrome 41+ op Windows 10, Windows 8.1, Mac OSX Yosemite en Chrome op Android 4.4.4, is Google Widevine de DRM achter EME.

![EME gebruiken voor Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine1.png)

Widevine weerhoudt u er niet van om een schermopname van beveiligde video te maken.

![Plug-ins voor spelers voor Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>EME gebruiken voor FairPlay

Op dezelfde manier kun je FairPlay-beveiligde content testen in deze testspeler in Safari op macOS of iOS 11.2 en hoger.

Zorg ervoor dat je "FairPlay" als protectionInfo.type plaatst en plaats de juiste URL voor je toepassingscertificaat in FPS AC Path (FairPlay Streaming Application Certificate Path).

### <a name="unentitled-users"></a>Gebruikers zonder recht

Als een gebruiker geen lid is van de groep 'Gebruikers recht', voldoet de gebruiker niet aan de controle van het recht. De multi-DRM licentie service weigert vervolgens om de gevraagde licentie uit te geven zoals getoond. De gedetailleerde beschrijving is "License acquire failed", wat is ontworpen.

![Gebruikers zonder recht](./media/design-multi-drm-system-with-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Een aangepaste beveiligingstokenservice uitvoeren

Als u een aangepaste STS uitvoert, wordt de JWT uitgegeven door de aangepaste STS met behulp van een symmetrische of een asymmetrische toets.

In de volgende schermafbeelding wordt een scenario weergegeven waarin een symmetrische sleutel wordt gebruikt (met Chrome):

![Aangepaste STS met een symmetrische toets](./media/design-multi-drm-system-with-access-control/media-services-running-sts1.png)

De volgende schermafbeelding toont een scenario dat een asymmetrische sleutel gebruikt via een X509-certificaat (met behulp van een moderne Microsoft-browser):

![Aangepaste STS met een asymmetrische toets](./media/design-multi-drm-system-with-access-control/media-services-running-sts2.png)

In beide voorgaande gevallen blijft gebruikersverificatie hetzelfde. Het gebeurt via Azure AD. Het enige verschil is dat JWT's worden uitgegeven door de aangepaste STS in plaats van Azure AD. Wanneer u dynamische CENC-beveiliging configureert, geeft de licentieservicebeperking het type JWT aan, een symmetrische of een asymmetrische sleutel.

## <a name="next-steps"></a>Volgende stappen

* [Veelgestelde vragen](frequently-asked-questions.md)
* [Overzicht van inhoudsbeveiliging](content-protection-overview.md)
* [Uw inhoud beveiligen met DRM](protect-with-drm.md)
