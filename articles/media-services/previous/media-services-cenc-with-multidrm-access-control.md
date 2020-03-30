---
title: Ontwerp van een contentbeveiligingssysteem met toegangscontrole met Azure Media Services | Microsoft Documenten
description: Meer informatie over het in licentie geven van de Microsoft Smooth Streaming Client Porting Kit.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162987"
---
# <a name="design-of-a-content-protection-system-with-access-control-using-azure-media-services"></a>Ontwerp van een contentbeveiligingssysteem met toegangsbeheer met Azure Media Services 

## <a name="overview"></a>Overzicht

Het ontwerpen en bouwen van een drm-subsysteem (Digital Rights Management) voor een over-the-top (OTT) of online streaming oplossing is een complexe taak. Operators/online videoproviders besteden deze taak doorgaans uit aan gespecialiseerde DRM-serviceproviders. Het doel van dit document is om een referentieontwerp en implementatie van een end-to-end DRM-subsysteem te presenteren in een OTT- of online streaming-oplossing.

De beoogde lezers voor dit document zijn ingenieurs die werken in DRM subsystemen van OTT of online streaming / multiscreen oplossingen of lezers die geïnteresseerd zijn in DRM subsystemen. De veronderstelling is dat lezers bekend zijn met ten minste één van de DRM-technologieën op de markt, zoals PlayReady, Widevine, FairPlay of Adobe Access.

In deze discussie over DRM, we ook gemeenschappelijke encryptie (CENC) met multi-DRM. Een belangrijke trend in online streaming en de OTT-industrie is het gebruik van CENC met multi-native DRM op verschillende clientplatforms. Deze trend is een verschuiving ten opzichte van de vorige die een enkele DRM en zijn client SDK gebruikt voor verschillende clientplatforms. Wanneer u CENC gebruikt met multi-native DRM, worden zowel PlayReady als Widevine versleuteld volgens de [Common Encryption (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) specificatie.

De voordelen van CENC met multi-DRM zijn dat het:

* Verlaagt de coderingskosten omdat één versleutelingsproces wordt gebruikt om verschillende platforms met zijn eigen DRM's te targeten.
* Vermindert de kosten van het beheer van versleutelde assets omdat slechts één exemplaar van versleutelde assets nodig is.
* Elimineert drm-clientlicentiekosten omdat de native DRM-client meestal gratis is op zijn eigen platform.

Microsoft is een actieve promotor van DASH en CENC samen met een aantal grote spelers in de industrie. Azure Media Services biedt ondersteuning voor DASH en CENC. Zie voor recente aankondigingen de volgende blogs:

*  [Google Widevine-services voor het leveren van licenties in Azure Media Services aankondigen](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Azure Media Services voegt Google Widevine-verpakkingen toe voor het leveren van een multi-DRM-stream](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="goals-of-the-article"></a>Doelstellingen van het artikel

De doelstellingen van dit artikel zijn:

* Geef een referentieontwerp van een DRM-subsysteem dat CENC met multi-DRM gebruikt.
* Geef een referentie-implementatie op een Azure/Media Services-platform.
* Bespreek een aantal ontwerp- en implementatieonderwerpen.

In het artikel heeft de term "multi-DRM" betrekking op de volgende producten:

* Microsoft PlayReady
* Google Widevine
* Apple FairPlay 

In de volgende tabel wordt een overzicht van de native platform/native app en browsers die door elke DRM worden ondersteund.

| **Client platform** | **Ondersteuning voor Native DRM** | **Browser/app** | **Streaming-indelingen** |
| --- | --- | --- | --- |
| **Smart TV's, operator STB's, OTT STB's** |PlayReady voornamelijk, en/of Widevine, en/of andere |Linux, Opera, WebKit, andere |Verschillende formaten |
| **Windows 10-apparaten (Windows-pc, Windows-tablets, Windows Phone, Xbox)** |PlayReady |Microsoft Edge/IE11/EME<br/><br/><br/>Universeel Windows-platform |DASH (voor HLS wordt PlayReady niet ondersteund)<br/><br/>DASH, Smooth Streaming (voor HLS wordt PlayReady niet ondersteund) |
| **Android-apparaten (telefoon, tablet, tv)** |Widevine |Chrome/EME |DASH, HLS |
| **iOS (iPhone, iPad), OS X-clients en Apple TV** |FairPlay |Safari 8+/EME |HLS |

Gezien de huidige status van implementatie voor elke DRM, wil een service doorgaans twee of drie DRM's implementeren om ervoor te zorgen dat u alle typen eindpunten op de beste manier aanpakt.

Er is een afweging tussen de complexiteit van de servicelogica en de complexiteit aan de clientzijde om een bepaald niveau van gebruikerservaring op de verschillende klanten te bereiken.

Houd er rekening mee dat u wilt dat uw selectie wordt gemaakt:

* PlayReady is native geïmplementeerd in elk Windows-apparaat, op sommige Android-apparaten, en beschikbaar via software SDKs op vrijwel elk platform.
* Widevine is native geïmplementeerd in elk Android-apparaat, in Chrome en in sommige andere apparaten.
* FairPlay is alleen beschikbaar op iOS- en Mac OS-clients of via iTunes.

Er zijn twee opties voor een typische multi-DRM:

* PlayReady en Widevine
* PlayReady, Widevine en FairPlay

## <a name="a-reference-design"></a>Een referentieontwerp
Deze sectie presenteert een referentieontwerp dat agnost is naar de technologieën die worden gebruikt om het te implementeren.

Een DRM-subsysteem kan de volgende componenten bevatten:

* Sleutelbeheer
* DRM-verpakking
* Levering van DRM-licentie
* Entitlement check
* Verificatie/autorisatie
* Speler
* Origin/content delivery network (CDN)

Het volgende diagram illustreert de interactie op hoog niveau tussen de componenten in een DRM-subsysteem:

![DRM-subsysteem met CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

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
| **Identiteitsprovider (IDP)** |Azure Active Directory (Azure AD) |
| **Beveiligingstokenservice (STS)** |Azure AD |
| **DRM-beveiligingswerkstroom** |Dynamische bescherming van Media Services |
| **Levering van DRM-licentie** |* Media Services licentie levering (PlayReady, Widevine, FairPlay) <br/>* Axinom licentie server <br/>* Aangepaste PlayReady licentieserver |
| **Oorsprong** |Streamingeindpunt voor Media Services |
| **Sleutelbeheer** |Niet nodig voor referentie-implementatie |
| **Inhoudsbeheer** |Een C#-consoletoepassing |

Met andere woorden, zowel IDP als STS worden gebruikt met Azure AD. De [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/) wordt gebruikt voor de speler. Zowel Media Services als Media Player ondersteunen DASH en CENC met multi-DRM.

In het volgende diagram ziet u de algemene structuur en stroom met de vorige technologietoewijzing:

![CENC op Media Services](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Om dynamische CENC-versleuteling in te stellen, gebruikt de tool voor inhoudsbeheer de volgende ingangen:

* Inhoud openen
* Inhoudssleutel van sleutelgeneratie/beheer
* URL's voor licentieverwerving
* Een lijst met gegevens uit Azure AD

Hier is de uitvoer van de content management tool:

* ContentKeyAuthorizationPolicy bevat de specificatie over hoe licentielevering een JSON Web Token (JWT) en DRM-licentiespecificaties verifieert.
* AssetDeliveryPolicy bevat specificaties voor streamingformaat, DRM-bescherming en URL's voor licentieverwerving.

Hier is de stroom tijdens runtime:

* Bij gebruikersverificatie wordt een JWT gegenereerd.
* Een van de claims in de JWT is een groepsclaim die de groepsobject-id GetiteldUserGroup bevat. Deze claim wordt gebruikt om de rechtencontrole te doorstaan.
* De speler downloadt het clientmanifest van door CENC beveiligde inhoud en identificeert het volgende:
   * Sleutel-ID.
   * De inhoud is CENC-beveiligd.
   * URL's voor licentieverwerving.
* De speler maakt een licentie verwerving verzoek op basis van de browser / DRM ondersteund. In de aanvraag voor het verkrijgen van licenties worden ook de sleutel-ID en de JWT ingediend. De licentieleveringsservice verifieert de JWT en de claims voordat deze de benodigde licentie uitgeeft.

## <a name="implementation"></a>Implementatie
### <a name="implementation-procedures"></a>Uitvoeringsprocedures
Implementatie omvat de volgende stappen:

1. Testassets voorbereiden. Encode/package a test video to multi-bitrate fragmented MP4 in Media Services. Dit actief is *niet* DRM-beschermd. DRM bescherming wordt gedaan door dynamische bescherming later.

2. Maak een sleutel-id en een inhoudssleutel (optioneel uit een sleutelzaad). In dit geval is het sleutelbeheersysteem niet nodig omdat er slechts één sleutel-id en inhoudssleutel nodig zijn voor een paar testelementen.

3. Gebruik de Media Services API om multi-DRM-licentieleveringsservices voor het testitem te configureren. Als u aangepaste licentieservers gebruikt van uw bedrijf of de leveranciers van uw bedrijf in plaats van licentieservices in Media Services, u deze stap overslaan. U url's voor licentieverwerving opgeven in de stap wanneer u licentielevering configureert. De Media Services API is nodig om een aantal gedetailleerde configuraties op te geven, zoals autorisatiebeleidsbeperking en licentieresponssjablonen voor verschillende DRM-licentieservices. Op dit moment biedt de Azure-portal niet de benodigde gebruikersinterface voor deze configuratie. Zie [PlayReady en/of Widevine dynamic common encryption gebruiken](media-services-protect-with-playready-widevine.md)voor informatie op API-niveau en voorbeeldcode.

4. Gebruik de Media Services API om het beleid voor het leveren van activa voor het testitem te configureren. Zie [PlayReady en/of Widevine dynamic common encryption gebruiken](media-services-protect-with-playready-widevine.md)voor informatie op API-niveau en voorbeeldcode.

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

* U informatie over ontwikkelaars vinden in de [handleiding van Azure Active Directory-ontwikkelaars.](../../active-directory/azuread-dev/v1-overview.md)
* U beheerdersgegevens vinden in [Uw Azure AD-tenantmap beheren.](../../active-directory/fundamentals/active-directory-administer.md)

### <a name="some-issues-in-implementation"></a>Enkele kwesties bij de uitvoering
Gebruik de volgende informatie over probleemoplossing voor hulp bij implementatieproblemen.

* De URL van de uitgever moet eindigen met "/". Het publiek moet de client-id van de spelertoepassing zijn. Voeg ook "/" toe aan het einde van de URL van de uitgever.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    In de [JWT Decoder](http://jwt.calebb.net/), zie je **aud** en **iss,** zoals weergegeven in de JWT:

    ![JWT JWT](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Machtigingen toevoegen aan de toepassing in Azure AD op het tabblad **Configureren** van de toepassing. Machtigingen zijn vereist voor elke toepassing, zowel lokale als geïmplementeerde versies.

    ![Machtigingen](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

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

## <a name="additional-topics-for-implementation"></a>Aanvullende onderwerpen voor implementatie
In dit gedeelte worden enkele aanvullende onderwerpen besproken in ontwerp en implementatie.

### <a name="http-or-https"></a>HTTP of HTTPS?
De ASP.NET MVC-spelertoepassing moet het volgende ondersteunen:

* Gebruikersverificatie via Azure AD, dat onder HTTPS staat.
* JWT-uitwisseling tussen de client en Azure AD, die onder HTTPS staat.
* DRM-licentieverwerving door de klant, die onder HTTPS moet vallen als de licentielevering wordt geleverd door Media Services. De PlayReady-productsuite verplicht geen HTTPS voor licentielevering. Als uw PlayReady-licentieserver zich buiten Media Services bevindt, u HTTP of HTTPS gebruiken.

De ASP.NET spelertoepassing gebruikt HTTPS als best practice, dus Media Player staat op een pagina onder HTTPS. Http heeft echter de voorkeur voor streaming, dus je moet rekening houden met het probleem van gemengde inhoud.

* De browser staat geen gemengde inhoud toe. Maar plug-ins zoals Silverlight en de OSMF plug-in voor smooth en DASH laten het toe. Gemengde inhoud is een beveiligingsprobleem vanwege de dreiging van de mogelijkheid om kwaadaardige JavaScript te injecteren, waardoor klantgegevens in gevaar kunnen komen. Browsers blokkeren deze mogelijkheid standaard. De enige manier om er omheen te werken is aan de server (oorsprong) kant door het toestaan van alle domeinen (ongeacht HTTPS of HTTP). Dit is waarschijnlijk ook geen goed idee.
* Vermijd gemengde inhoud. Zowel de player-applicatie als Media Player moeten HTTP of HTTPS gebruiken. Bij het afspelen van gemengde inhoud, de silverlightSS tech vereist clearing een mixed-content waarschuwing. De flashSS-technologie verwerkt gemengde inhoud zonder waarschuwing voor gemengde inhoud.
* Als je streaming-eindpunt vóór augustus 2014 is gemaakt, wordt er geen https ondersteund. Maak en gebruik in dit geval een nieuw streamingeindpunt voor HTTPS.

In de referentie-implementatie voor DRM-beveiligde inhoud staan zowel de toepassing als streaming onder HTTPS. Voor open inhoud heeft de speler geen verificatie of licentie nodig, dus u HTTP of HTTPS gebruiken.

### <a name="azure-active-directory-signing-key-rollover"></a>Rollover van Azure Active Directory-ondertekeningssleutel
Het ondertekenen van key rollover is een belangrijk punt om rekening mee te houden bij uw implementatie. Als je het negeert, werkt het afgewerkte systeem uiteindelijk niet meer volledig, hooguit binnen zes weken.

Azure AD gebruikt industriestandaarden om vertrouwen te creëren tussen zichzelf en toepassingen die Azure AD gebruiken. Azure AD gebruikt in het bijzonder een ondertekeningssleutel die bestaat uit een openbaar en privésleutelpaar. Wanneer Azure AD een beveiligingstoken maakt dat informatie over de gebruiker bevat, wordt het door Azure AD ondertekend met een privésleutel voordat het wordt teruggestuurd naar de toepassing. Om te controleren of het token geldig is en afkomstig is van Azure AD, moet de toepassing de handtekening van het token valideren. De toepassing maakt gebruik van de openbare sleutel die is blootgesteld door Azure AD en die is opgenomen in het metagegevensdocument van de federatie van de tenant. Deze openbare sleutel en de ondertekeningssleutel waaruit deze is afgeleid, is dezelfde die wordt gebruikt voor alle tenants in Azure AD.

Zie [Belangrijke informatie over het ondertekenen van sleutelrollover in Azure AD](../../active-directory/active-directory-signing-key-rollover.md)voor meer informatie over de rollover van Azure-ad-

Tussen het [publiek-private sleutelpaar](https://login.microsoftonline.com/common/discovery/keys/):

* De privésleutel wordt door Azure AD gebruikt om een JWT te genereren.
* De openbare sleutel wordt gebruikt door een toepassing zoals DRM-licentieservices in Media Services om de JWT te verifiëren.

Om veiligheidsredenen roteert Azure AD het certificaat periodiek (om de zes weken). In het geval van inbreuken op de beveiliging kan de sleutelrollover op elk gewenst moment plaatsvinden. Daarom moeten de licentieservices in Media Services de openbare sleutel bijwerken die wordt gebruikt omdat Azure AD het sleutelpaar roteert. Anders mislukt tokenverificatie in Media Services en wordt er geen licentie uitgegeven.

Als u deze service wilt instellen, stelt u TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument in wanneer u DRM-licentieservices configureert.

Hier is de JWT stroom:

* Azure AD geeft de JWT uit met de huidige privésleutel voor een geverifieerde gebruiker.
* Wanneer een speler een CENC met multi-DRM-beveiligde inhoud ziet, wordt eerst de JWT gevonden die is uitgegeven door Azure AD.
* De speler stuurt een licentie verwerving aanvraag met de JWT om de levering van licenties in Media Services licentie.
* De licentieservices in Media Services gebruiken de huidige/geldige openbare sleutel van Azure AD om de JWT te verifiëren voordat licenties worden uitgegeven.

DRM-licentieservices controleren altijd op de huidige/geldige openbare sleutel van Azure AD. De openbare sleutel die wordt gepresenteerd door Azure AD is de sleutel die wordt gebruikt om een JWT te verifiëren die is uitgegeven door Azure AD.

Wat gebeurt er als de sleutelrollover plaatsvindt nadat Azure AD een JWT genereert, maar voordat de JWT door spelers naar DRM-licentieservices in Media Services wordt verzonden voor verificatie?

Omdat een sleutel op elk moment kan worden omgerold, is er altijd meer dan één geldige openbare sleutel beschikbaar in het metagegevensdocument van de federatie. Media Services-licentielevering kan een van de sleutels gebruiken die in het document zijn opgegeven. Omdat een sleutel kan binnenkort worden gerold, een andere zou kunnen worden de vervanging, enzovoort.

### <a name="where-is-the-access-token"></a>Waar is het toegangstoken?
Als u kijkt naar hoe een web-app een API-app aanroept onder [Toepassingsidentiteit met OAuth 2.0-clientreferenties verlenen,](../../active-directory/azuread-dev/web-api.md)is de verificatiestroom als volgt:

* Een gebruiker meldt zich in de webtoepassing aan bij Azure AD. Zie [Webbrowser naar webtoepassing voor](../../active-directory/azuread-dev/web-app.md)meer informatie.
* Het eindpunt azure AD-autorisatie leidt de gebruikersagent terug naar de clienttoepassing met een autorisatiecode. De gebruikersagent retourneert de autorisatiecode naar de omleidingsURI van de clienttoepassing.
* De webtoepassing moet een toegangstoken aanschaffen, zodat deze kan worden geverifieerd naar de web-API en de gewenste bron kan ophalen. Het maakt een verzoek naar het eindpunt van het Azure AD-token en biedt de referentie- en client-id en de toepassings-ID URI van de web-API. Het presenteert de autorisatiecode om te bewijzen dat de gebruiker toestemming heeft gegeven.
* Azure AD verifieert de toepassing en retourneert een JWT-toegangstoken dat wordt gebruikt om de web-API aan te roepen.
* Via HTTPS gebruikt de webtoepassing het geretourneerde JWT-toegangstoken om de JWT-tekenreeks toe te voegen met een aanduiding "Drager" in de kop autorisatie van het verzoek aan de web-API. De web-API valideert vervolgens de JWT. Als de validatie is geslaagd, wordt de gewenste bron geretourneerd.

In deze identiteitsstroom van toepassingen vertrouwt de web-API dat de webtoepassing de gebruiker heeft geverifieerd. Om deze reden wordt dit patroon een vertrouwd subsysteem genoemd. Het [autorisatiestroomdiagram](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) beschrijft hoe de autorisatie-code-grant-stroom werkt.

Licentieverwerving met tokenbeperking volgt hetzelfde vertrouwde subsysteempatroon. De licentieleveringsservice in Media Services is de web-API-bron of de 'back-endbron' waartoe een webtoepassing toegang moet hebben. Waar is het toegangstoken?

Het toegangstoken wordt verkregen uit Azure AD. Na succesvolle gebruikersverificatie wordt een autorisatiecode geretourneerd. De autorisatiecode wordt vervolgens, samen met de client-id en de app-sleutel, gebruikt om te wisselen voor het toegangstoken. Het toegangstoken wordt gebruikt om toegang te krijgen tot een 'aanwijzer'-toepassing die verwijst naar of vertegenwoordigt naar de mediaservices-licentieservice.

Ga als volgt te werk om de aanwijzer-app in Azure AD te registreren en te configureren:

1. In de Azure AD-tenant:

   * Voeg een toepassing (resource) toe met de aanmeldings-URL https://[resource_name].azurewebsites.net/. 
   * Voeg een app-id toe met de URL https://[aad_tenant_name].onmicrosoft.com/[resource_name].

2. Voeg een nieuwe sleutel toe voor de resource-app.

3. Werk het app-manifestbestand bij zodat de eigenschap groupMembershipClaims de waarde "groupMembershipClaims" heeft: "Alles".

4. Voeg in de Azure AD-app die naar de web-app voor spelers verwijst, in de sectie **Machtigingen voor andere toepassingen**de resource-app toe die in stap 1 is toegevoegd. Selecteer **Onder Gedelegeerde machtiging**de optie Toegang **[resource_name]**. Met deze optie geeft de web-app toestemming om toegangstokens te maken die toegang hebben tot de bron-app. Doe dit voor zowel de lokale als geïmplementeerde versie van de web-app als u zich ontwikkelt met Visual Studio en de Azure-webapp.

De JWT uitgegeven door Azure AD is het toegangstoken dat wordt gebruikt om toegang te krijgen tot de aanwijzerbron.

### <a name="what-about-live-streaming"></a>Hoe zit het met live streaming?
De vorige discussie richtte zich op on-demand activa. Hoe zit het met live streaming?

U precies hetzelfde ontwerp en dezelfde implementatie gebruiken om live streaming in Media Services te beschermen door het item dat aan een programma is gekoppeld, als een VOD-asset te behandelen.

Om live streaming in Media Services te doen, moet je een kanaal maken en vervolgens een programma maken onder het kanaal. Als u het programma wilt maken, moet u een asset maken die het live archief voor het programma bevat. Als u CENC multi-DRM-bescherming van de live-inhoud wilt bieden, past u dezelfde instelling/verwerking toe op het actief alsof het een VOD-asset is voordat u het programma start.

### <a name="what-about-license-servers-outside-media-services"></a>Hoe zit het met licentieservers buiten Media Services?
Vaak hebben klanten geïnvesteerd in een licentieserverfarm in hun eigen datacenter of een bedrijf dat wordt gehost door DRM-serviceproviders. Met Media Services Content Protection u in hybride modus werken. Inhoud kan worden gehost en dynamisch worden beveiligd in Media Services, terwijl DRM-licenties worden geleverd door servers buiten Media Services. Houd in dit geval rekening met de volgende wijzigingen:

* STS moet tokens uitgeven die acceptabel zijn en kunnen worden geverifieerd door de licentieserverfarm. De Widevine-licentieservers van Axinom vereisen bijvoorbeeld een specifieke JWT die een rechtenbericht bevat. Daarom moet je een STS hebben om zo'n JWT uit te geven. Ga voor informatie over dit type implementatie naar het [Azure Documentation Center](https://azure.microsoft.com/documentation/) en zie [Axinom gebruiken om Widevine-licenties te leveren aan Azure Media Services.](media-services-axinom-integration.md)
* U hoeft geen licentieleveringsservice (ContentKeyAuthorizationPolicy) meer te configureren in Media Services. U moet url's voor licentieverwerving (voor PlayReady, Widevine en FairPlay) opgeven wanneer u AssetDeliveryPolicy configureert om CENC in te stellen met multi-DRM.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Wat als ik een aangepaste STS wil gebruiken?
Een klant kan ervoor kiezen om een aangepaste STS te gebruiken om JWT's te leveren. Redenen zijn onder meer:

* De IDP die door de klant wordt gebruikt, ondersteunt STS niet. In dit geval kan een aangepaste STS een optie zijn.
* De klant heeft mogelijk een flexibelere of strengere controle nodig om STS te integreren met het factureringssysteem voor abonnees van de klant. Een MVPD-operator kan bijvoorbeeld meerdere OTT-abonneepakketten aanbieden, zoals premium, basic en sport. De operator kan de claims in een token willen koppelen aan het pakket van een abonnee, zodat alleen de inhoud in een specifiek pakket beschikbaar wordt gesteld. In dit geval biedt een aangepaste STS de nodige flexibiliteit en controle.

Wanneer u een aangepaste STS gebruikt, moeten twee wijzigingen worden aangebracht:

* Wanneer u de licentieleveringsservice voor een asset configureert, moet u de beveiligingssleutel opgeven die wordt gebruikt voor verificatie door de aangepaste STS in plaats van de huidige sleutel van Azure AD. (Meer details volgen.) 
* Wanneer een JTW-token wordt gegenereerd, wordt een beveiligingssleutel opgegeven in plaats van de privésleutel van het huidige X509-certificaat in Azure AD.

Er zijn twee soorten beveiligingssleutels:

* Symmetrische sleutel: Dezelfde sleutel wordt gebruikt om een JWT te genereren en te verifiëren.
* Asymmetrische sleutel: Een publiek-private sleutelpaar in een X509-certificaat wordt gebruikt met een privésleutel om een JWT te versleutelen/genereren en met de openbare sleutel om het token te verifiëren.

> [!NOTE]
> Als u .NET Framework/C# als ontwikkelingsplatform gebruikt, moet het X509-certificaat dat wordt gebruikt voor een asymmetrische beveiligingssleutel ten minste 2048 een sleutellengte hebben. Dit is een vereiste van het klasse System.IdentityModel.Tokens.X509AsymmetricSecurityKey in .NET Framework. Anders wordt de volgende uitzondering gegooid:
> 
> IDX10630: Het 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' voor ondertekening mag niet kleiner zijn dan '2048' bits.

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

![Aangepast Azure AD-tenantdomeinaccount](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Microsoft-domeinaccount met smartcard:** de aanmeldingspagina die is aangepast door Microsoft corporate IT met tweestapsverificatie.

![Aangepast Azure AD-tenantdomeinaccount](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft-account**: de aanmeldingspagina van het Microsoft-account voor consumenten.

![Aangepast Azure AD-tenantdomeinaccount](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Versleutelde media-extensies gebruiken voor PlayReady
In een moderne browser met EME-ondersteuning (Encrypted Media Extensions) voor PlayReady, zoals Internet Explorer 11 op Windows 8.1 of hoger en Microsoft Edge-browser op Windows 10, is PlayReady de onderliggende DRM voor EME.

![EME gebruiken voor PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

Het gebied voor donkere spelers is omdat PlayReady-beveiliging voorkomt dat u een schermopname van beveiligde video maakt.

De volgende screenshot toont de plug-ins voor spelers en Microsoft Security Essentials (MSE)/EME-ondersteuning:

![Plug-ins voor spelers voor PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

Met EME in Microsoft Edge en Internet Explorer 11 op Windows 10 kan [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) worden aangeroepen op Windows 10-apparaten die het ondersteunen. PlayReady SL3000 ontgrendelt de stroom van verbeterde premium content (4K, HDR) en nieuwe content delivery modellen (voor verbeterde content).

Om zich te concentreren op de Windows-apparaten, PlayReady is de enige DRM in de hardware beschikbaar op Windows-apparaten (PlayReady SL3000). Een streamingdienst kan PlayReady via EME of via een Universal Windows Platform-toepassing gebruiken en een hogere videokwaliteit bieden door PlayReady SL3000 te gebruiken dan een andere DRM. Inhoud tot 2K stroomt meestal via Chrome of Firefox en inhoud tot 4K loopt via Microsoft Edge/Internet Explorer 11 of een Universele Windows Platform-toepassing op hetzelfde apparaat. Het bedrag is afhankelijk van service-instellingen en implementatie.

#### <a name="use-eme-for-widevine"></a>EME gebruiken voor Widevine
In een moderne browser met EME/Widevine-ondersteuning, zoals Chrome 41+ op Windows 10, Windows 8.1, Mac OSX Yosemite en Chrome op Android 4.4.4, is Google Widevine de DRM achter EME.

![EME gebruiken voor Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine weerhoudt u er niet van om een schermopname van beveiligde video te maken.

![Plug-ins voor spelers voor Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Gebruikers zonder recht
Als een gebruiker geen lid is van de groep 'Gebruikers recht', voldoet de gebruiker niet aan de controle van het recht. De multi-DRM licentie service weigert vervolgens om de gevraagde licentie uit te geven zoals getoond. De gedetailleerde beschrijving is "License acquire failed", wat is ontworpen.

![Gebruikers zonder recht](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Een aangepaste beveiligingstokenservice uitvoeren
Als u een aangepaste STS uitvoert, wordt de JWT uitgegeven door de aangepaste STS met behulp van een symmetrische of een asymmetrische toets.

In de volgende schermafbeelding wordt een scenario weergegeven waarin een symmetrische sleutel wordt gebruikt (met Chrome):

![Aangepaste STS met een symmetrische toets](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

De volgende schermafbeelding toont een scenario dat een asymmetrische sleutel gebruikt via een X509-certificaat (met behulp van een moderne Microsoft-browser):

![Aangepaste STS met een asymmetrische toets](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

In beide voorgaande gevallen blijft gebruikersverificatie hetzelfde. Het gebeurt via Azure AD. Het enige verschil is dat JWT's worden uitgegeven door de aangepaste STS in plaats van Azure AD. Wanneer u dynamische CENC-beveiliging configureert, geeft de licentieservicebeperking het type JWT aan, een symmetrische of een asymmetrische sleutel.

## <a name="summary"></a>Samenvatting

Dit document besprak CENC met multi-native DRM en toegangscontrole via tokenverificatie, het ontwerp en de implementatie ervan met azure, mediaservices en mediaspeler.

* Er werd een referentieontwerp gepresenteerd dat alle benodigde componenten bevat in een DRM/CENC-subsysteem.
* Er is een referentie-implementatie gepresenteerd op Azure, Media Services en Media Player.
* Enkele onderwerpen die direct betrokken zijn bij het ontwerp en de uitvoering werden ook besproken.

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
 
