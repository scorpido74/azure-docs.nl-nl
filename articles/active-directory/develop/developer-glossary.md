---
title: Woordenlijst voor ontwikkelaars van Microsoft-identiteitsplatform | Azure
description: Een lijst met termen voor veelgebruikte concepten en functies van Microsoft-identiteitsplatformontwikkelaars.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/13/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.openlocfilehash: ce98d2db86c87ac6aa8fa4872bc076714467d32f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263047"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Woordenlijst voor ontwikkelaars van Microsoft-identiteitsplatform

Dit artikel bevat definities voor enkele van de belangrijkste ontwikkelaarsconcepten en terminologie, die handig zijn bij het leren over applicatieontwikkeling met behulp van het Microsoft-identiteitsplatform.

## <a name="access-token"></a>toegangstoken

Een type [beveiligingstoken](#security-token) dat is uitgegeven door een [autorisatieserver](#authorization-server)en wordt gebruikt door een [clienttoepassing](#client-application) om toegang te krijgen tot een [beveiligde resourceserver](#resource-server). Typisch in de vorm van een [JSON Web Token (JWT)][JWT], het token belichaamt de autorisatie verleend aan de client door de eigenaar van de [bron](#resource-owner), voor een aangevraagd niveau van toegang. Het token bevat alle toepasselijke [claims](#claim) over het onderwerp, waardoor de clienttoepassing het kan gebruiken als een vorm van referenties bij het openen van een bepaalde bron. Dit elimineert ook de noodzaak voor de eigenaar van de bron om referenties bloot te stellen aan de client.

Toegangstokens worden soms aangeduid als 'User+App' of 'App-Only', afhankelijk van de referenties die worden weergegeven. Wanneer een clienttoepassing bijvoorbeeld de:

* [Autorisatiecode verlening](#authorization-grant), de eindgebruiker verifieert eerst als de eigenaar van de bron, delegeren van autorisatie aan de client om toegang te krijgen tot de bron. De client verifieert daarna bij het verkrijgen van het toegangstoken. Het token kan soms specifieker worden aangeduid als een "User+App"-token, omdat het zowel de gebruiker vertegenwoordigt die de clienttoepassing heeft geautoriseerd als de toepassing.
* ["Clientreferenties" autorisatieverlening](#authorization-grant), de client biedt de enige verificatie, functioneren zonder authenticatie/ autorisatie van de bron-eigenaar, zodat het token soms kan worden aangeduid als een "App-Only"-token.

Zie [Microsoft-identiteitsplatform Token Reference][AAD-Tokens-Claims] voor meer informatie.

## <a name="application-id-client-id"></a>toepassings-id (client-id)

De unieke id Azure AD geeft problemen met een toepassingsregistratie die een specifieke toepassing en de bijbehorende configuraties identificeert. Deze toepassings-ID[(client-ID)](https://tools.ietf.org/html/rfc6749#page-15)wordt gebruikt bij het uitvoeren van verificatieaanvragen en wordt in ontwikkeltijd aan de verificatiebibliotheken verstrekt. De toepassings-ID (client-ID) is geen geheim.

## <a name="application-manifest"></a>toepassingsmanifest

Een functie die wordt geleverd door de [Azure-portal][AZURE-portal], die een JSON-weergave van de identiteitsconfiguratie van de toepassing produceert, wordt gebruikt als een mechanisme voor het bijwerken van de bijbehorende [entiteiten voor toepassing][Graph-App-Resource] en [serviceprincipal.][Graph-Sp-Resource] Zie [Het Azure Active Directory-toepassingsmanifest voor][AAD-App-Manifest] meer informatie bekijken.

## <a name="application-object"></a>toepassingsobject

Wanneer u een toepassing registreert/updatet in de [Azure-portal,][AZURE-portal]maakt/werkt de portal zowel een toepassingsobject als een bijbehorend [servicehoofdobject](#service-principal-object) voor die tenant. Het toepassingsobject *definieert* de identiteitsconfiguratie van de toepassing wereldwijd (voor alle tenants waar deze toegang heeft), en biedt een sjabloon waaruit het bijbehorende servicehoofdobject(en) is *afgeleid* voor lokaal gebruik tijdens run-time (in een specifieke tenant).

Zie [Hoofdobjecten voor toepassingen en servicetoepassingen][AAD-App-SP-Objects]voor meer informatie.

## <a name="application-registration"></a>toepassingsregistratie

Als u een toepassing wilt laten integreren met en delegeren van identiteits- en toegangsbeheerfuncties aan Azure AD, moet deze zijn geregistreerd bij een Azure [AD-tenant.](#tenant) Wanneer u uw toepassing registreert bij Azure AD, biedt u een identiteitsconfiguratie voor uw toepassing, zodat deze kan worden geïntegreerd met Azure AD en functies gebruiken zoals:

* Robuust beheer van single sign-on met Azure AD Identity Management en [OpenID Connect-protocolimplementatie][OpenIDConnect]
* Brokered access to [protected resources](#resource-server) by [client applications](#client-application), via OAuth 2.0 authorization [server](#authorization-server)
* [Toestemmingskader](#consent) voor het beheren van clienttoegang tot beveiligde bronnen, op basis van autorisatie voor de eigenaar van resources.

Zie [Toepassingen integreren met Azure Active Directory][AAD-Integrating-Apps] voor meer informatie.

## <a name="authentication"></a>verificatie

De handeling van het uitdagen van een partij voor legitieme referenties, het verstrekken van de basis voor de oprichting van een security principal te worden gebruikt voor identiteit en toegangscontrole. Tijdens een [OAuth2-autorisatiesubsidie](#authorization-grant) vult de partij die bijvoorbeeld de rol van [resource-eigenaar](#resource-owner) of [clientaanvraag](#client-application)invult, afhankelijk van de gebruikte subsidie.

## <a name="authorization"></a>autorisatie

De handeling van het verlenen van een geverifieerde security principal toestemming om iets te doen. Er zijn twee primaire gebruiksscenario's in het Azure AD-programmeermodel:

* Tijdens een [OAuth2-autorisatiesubsidiestroom:](#authorization-grant) wanneer de [eigenaar van](#resource-owner) de resource autorisatie verleent aan de [clienttoepassing,](#client-application)zodat de client toegang heeft tot de resources van de broneigenaar.
* Tijdens de toegang tot resources door de client: zoals geïmplementeerd door de [resourceserver,](#resource-server)gebruikt u de [claimwaarden](#claim) die aanwezig zijn in het [toegangstoken](#access-token) om op basis daarvan beslissingen over toegangsbeheer te nemen.

## <a name="authorization-code"></a>autorisatiecode

Een kortstondig "token" die door het [autorisatieeindpunt](#authorization-endpoint)aan een [clientaanvraag](#client-application) wordt verstrekt , als onderdeel van de "autorisatiecode"-stroom , een van de vier OAuth2-autorisatiesubsidies . [authorization grants](#authorization-grant) De code wordt teruggestuurd naar de clienttoepassing als reactie op verificatie van een [broneigenaar,](#resource-owner)wat aangeeft dat de eigenaar van de bron de autorisatie heeft gedelegeerd om toegang te krijgen tot de gevraagde bronnen. Als onderdeel van de stroom wordt de code later ingewisseld voor een [toegangstoken.](#access-token)

## <a name="authorization-endpoint"></a>eindpunt van de vergunning

Een van de eindpunten geïmplementeerd door de [autorisatieserver](#authorization-server), gebruikt om te communiceren met de [eigenaar van](#resource-owner) de resource om een [autorisatie subsidie](#authorization-grant) te verlenen tijdens een OAuth2 autorisatie subsidie stroom. Afhankelijk van de gebruikte autorisatiesubsidiestroom kan de werkelijke subsidie variëren, inclusief een [autorisatiecode](#authorization-code) of [beveiligingstoken.](#security-token)

Zie de [typetypen autorisatieverlening][OAuth2-AuthZ-Grant-Types] en [eindpuntsecties van][OAuth2-AuthZ-Endpoint] de OAuth2-specificatie en de [OpenIDConnect-specificatie][OpenIDConnect-AuthZ-Endpoint] voor meer details.

## <a name="authorization-grant"></a>vergunningverlening

Een referentie die de [autorisatie](#authorization) van de [broneigenaar](#resource-owner) vertegenwoordigt om toegang te krijgen tot de beveiligde resources, verleend aan een [clienttoepassing.](#client-application) Een clienttoepassing kan een van de vier subsidietypen gebruiken [die zijn gedefinieerd door het OAuth2 Authorization Framework][OAuth2-AuthZ-Grant-Types] om een subsidie te verkrijgen, afhankelijk van het type/de vereisten van de client: "autorisatiecodegrant", "clientcredentials grant", "implicit grant" en "resource owner password credentials grant". De referentie die aan de client wordt geretourneerd, is een [toegangstoken](#access-token)of een [autorisatiecode](#authorization-code) (later ingeruild voor een toegangstoken), afhankelijk van het type autorisatieverlening dat wordt gebruikt.

## <a name="authorization-server"></a>autorisatieserver

Zoals gedefinieerd door het [OAuth2 Authorization Framework][OAuth2-Role-Def], de server die verantwoordelijk is voor de uitgifte van toegangstokens aan de [client](#client-application) na het succesvol verifiëren van de eigenaar van de [bron](#resource-owner) en het verkrijgen van de autorisatie. Een [clienttoepassing](#client-application) werkt op runtime met de autorisatieserver via de [autorisatie-](#authorization-endpoint) en [tokeneindpunten,](#token-endpoint) in overeenstemming met de door OAuth2 gedefinieerde [autorisatiesubsidies.](#authorization-grant)

In het geval van microsoft-identiteitsplatform-toepassingsintegratie implementeert microsoft-identiteitsplatform de autorisatieserverrol voor Azure AD-toepassingen en [Microsoft-service-API's, bijvoorbeeld Microsoft Graph API's][Microsoft-Graph].

## <a name="claim"></a>Vordering

Een [beveiligingstoken](#security-token) bevat claims die beweringen geven over een entiteit (zoals een [clienttoepassing](#client-application) of [broneigenaar)](#resource-owner)aan een andere entiteit (zoals de [resourceserver).](#resource-server) Claims zijn naam/waardeparen die feiten over het tokenonderwerp doorgeven (bijvoorbeeld de beveiligingsprincipal die is geverifieerd door de [autorisatieserver](#authorization-server)). De claims in een bepaald token zijn afhankelijk van verschillende variabelen, waaronder het type token, het type referentie dat wordt gebruikt om het onderwerp te verifiëren, de toepassingsconfiguratie, enz.

Zie [Microsoft-tokenreferentie][AAD-Tokens-Claims] voor identiteitsplatforms voor meer informatie.

## <a name="client-application"></a>clienttoepassing

Zoals gedefinieerd door het [OAuth2 Authorization Framework][OAuth2-Role-Def], een toepassing die namens de eigenaar van de resource aanvragen voor beveiligde resources [.](#resource-owner) De term "client" impliceert geen specifieke hardware-implementatiekenmerken (bijvoorbeeld of de toepassing wordt uitgevoerd op een server, een desktop of andere apparaten).

Een clienttoepassing vraagt [toestemming](#authorization) aan van een broneigenaar om deel te nemen aan een [oauth2-autorisatieverleningsstroom](#authorization-grant) en heeft namens de eigenaar van de resource toegang tot API's/gegevens. Het OAuth2 Authorization Framework [definieert twee soorten clients][OAuth2-Client-Types], "vertrouwelijk" en "openbaar", op basis van het vermogen van de klant om de vertrouwelijkheid van zijn referenties te behouden. Toepassingen kunnen een [webclient (vertrouwelijk)](#web-client) implementeren die wordt uitgevoerd op een webserver, een [native client (openbaar)](#native-client) die op een apparaat is geïnstalleerd of een [client op basis van gebruikersagent (openbaar)](#user-agent-based-client) die in de browser van een apparaat wordt uitgevoerd.

## <a name="consent"></a>Toestemming

Het proces van een [broneigenaar](#resource-owner) die namens de eigenaar van de resource een autorisatie verleent aan een [clienttoepassing,](#client-application)om toegang te krijgen tot beveiligde bronnen onder specifieke [machtigingen.](#permissions) Afhankelijk van de door de client gevraagde machtigingen wordt een beheerder of gebruiker om toestemming gevraagd om toegang te verlenen tot respectievelijk hun organisatie/individuele gegevens. Opmerking, in een [multi-tenant](#multi-tenant-application) scenario wordt de [serviceprincipal](#service-principal-object) van de toepassing ook geregistreerd in de tenant van de instemmende gebruiker.

Zie [toestemmingskader](consent-framework.md) voor meer informatie.

## <a name="id-token"></a>ID-token

Een [OpenID][OpenIDConnect-ID-Token] [Connect-beveiligingstoken](#security-token) dat wordt geleverd door het [autorisatieeindpunt](#authorization-endpoint)van een [autorisatieserver](#authorization-server) , dat [claims](#claim) bevat met betrekking tot de verificatie van de [eigenaar](#resource-owner)van een bron van een eindgebruiker . Net als een toegangstoken worden ID-tokens ook weergegeven als een digitaal ondertekende [JSON-webtoken (JWT).][JWT] In tegenstelling tot een toegangstoken worden de claims van een ID-token echter niet gebruikt voor doeleinden die verband houden met toegang tot resources en specifiek toegangsbeheer.

Zie [Microsoft-tokenreferentie][AAD-Tokens-Claims] voor identiteitsplatforms voor meer informatie.

## <a name="microsoft-identity-platform"></a>Microsoft Identity Platform

Het Microsoft Identity Platform bouwt voort op de identiteitsservice en het ontwikkelaarsplatform van Azure Active Directory (Azure AD). Met het Microsoft Identity Platform kunnen ontwikkelaars toepassingen maken waarbij gebruikers zich met alle Microsoft-identiteiten kunnen aanmelden en waarmee tokens worden opgehaald voor het aanroepen van Microsoft Graph, andere Microsoft-API's of API's die door ontwikkelaars zijn gemaakt. Het is een volledig uitgerust platform dat bestaat uit een verificatieservice, bibliotheken, applicatieregistratie en -configuratie, volledige documentatie van ontwikkelaars, codevoorbeelden en andere ontwikkelaarsinhoud. Het Microsoft Identity Platform biedt ondersteuning voor standaardprotocollen als OAuth 2.0 en OpenID Connect. Zie [Over het identiteitsplatform van Microsoft](about-microsoft-identity-platform.md) voor meer informatie.

## <a name="multi-tenant-application"></a>multi-tenant toepassing

Een toepassingsklasse die aanmelding en [toestemming](#consent) biedt voor gebruikers die zijn ingericht in een Azure [AD-tenant,](#tenant)inclusief andere tenants dan die waar de client is geregistreerd. [Native clienttoepassingen](#native-client) zijn standaard multitenant, terwijl [webclient-](#web-client) en [webbron-/API-toepassingen](#resource-server) de mogelijkheid hebben om te kiezen tussen één of meerdere tenant. Een webtoepassing die als één tenant is geregistreerd, staat daarentegen alleen aanmeldingen toe van gebruikersaccounts die zijn ingericht in dezelfde tenant als die waarin de toepassing is geregistreerd.

Zie [Hoe u zich aanmeldt bij een Azure AD-gebruiker met behulp van het toepassingspatroon met meerdere tenants][AAD-Multi-Tenant-Overview] voor meer informatie.

## <a name="native-client"></a>native client

Een type [clienttoepassing](#client-application) dat native op een apparaat is geïnstalleerd. Aangezien alle code wordt uitgevoerd op een apparaat, wordt het beschouwd als een "openbare" client vanwege het onvermogen om referenties privé / vertrouwelijk op te slaan. Zie [OAuth2-clienttypen en -profielen][OAuth2-Client-Types] voor meer informatie.

## <a name="permissions"></a>Machtigingen

Een [clienttoepassing](#client-application) krijgt toegang tot een [resourceserver](#resource-server) door toestemmingsaanvragen in te dienen. Er zijn twee typen beschikbaar:

* Machtigingen voor gedelegeerde machtigingen, die [scope-gebaseerde](#scopes) toegang opgeven met behulp van gedelegeerde autorisatie van de eigenaar van de aangemelde [resource,](#resource-owner)worden tijdens verloop van tijd aan de resource gepresenteerd als ["scp"-claims](#claim) in het [toegangstoken](#access-token)van de client .
* "Toepassingsmachtigingen", die [op rollen gebaseerde](#roles) toegang opgeven met behulp van de referenties/identiteit van de clienttoepassing, worden tijdens verloop van tijd aan de resource gepresenteerd als ["rollen" claims](#claim) in het toegangstoken van de client.

Ze duiken ook op tijdens het [toestemmingsproces,](#consent) waardoor de beheerder of resource-eigenaar de mogelijkheid krijgt om de client toegang te verlenen/weigeren tot bronnen in hun tenant.

Machtigingsaanvragen worden geconfigureerd op de pagina **API-machtigingen** voor een toepassing in de [Azure-portal][AZURE-portal]door de gewenste 'Gedelegeerde machtigingen' en 'Toepassingsmachtigingen' te selecteren (deze laatste vereist lidmaatschap in de rol Globale beheerder). Omdat een [openbare client](#client-application) niet veilig referenties kan onderhouden, kan deze alleen gedelegeerde machtigingen aanvragen, terwijl een [vertrouwelijke client](#client-application) de mogelijkheid heeft om zowel gedelegeerde als toepassingsmachtigingen aan te vragen. Het [toepassingsobject](#application-object) van de client slaat de gedeclareerde machtigingen op in de [vereiste Eigenschap ResourceAccess.][Graph-App-Resource]

## <a name="resource-owner"></a>resource-eigenaar

Zoals gedefinieerd door het [OAuth2 Authorization Framework][OAuth2-Role-Def], een entiteit die toegang kan verlenen tot een beschermde bron. Wanneer de eigenaar van de resource een persoon is, wordt deze een eindgebruiker genoemd. Wanneer een [clienttoepassing](#client-application) bijvoorbeeld toegang wil krijgen tot het postvak van een gebruiker via de [Microsoft Graph-API,][Microsoft-Graph]is hiervoor toestemming vereist van de broneigenaar van het postvak.

## <a name="resource-server"></a>resourceserver

Zoals gedefinieerd door het [OAuth2 Authorization Framework][OAuth2-Role-Def], een server die beveiligde bronnen host, die in staat is om beveiligde resourceaanvragen te accepteren en te beantwoorden door [clienttoepassingen](#client-application) die een [toegangstoken](#access-token)presenteren. Ook wel bekend als een beveiligde resourceserver of brontoepassing.

Een resourceserver legt API's bloot en dwingt toegang tot de beveiligde bronnen via [scopes](#scopes) en [rollen,](#roles)met behulp van het OAuth 2.0 Authorization Framework. Voorbeelden hiervan zijn de [Microsoft Graph API][Microsoft-Graph] die toegang biedt tot Azure AD-tenantgegevens en de Office 365-API's die toegang bieden tot gegevens zoals e-mail en agenda. 

Net als bij een clienttoepassing wordt de identiteitsconfiguratie van de resourcetoepassing tot stand gebracht via [registratie](#application-registration) in een Azure AD-tenant, die zowel het hoofdobject van de toepassing als de service levert. Sommige door Microsoft geleverde API's, zoals de Microsoft Graph API, hebben vooraf geregistreerde serviceprincipals beschikbaar gesteld in alle tenants tijdens de inrichting.

## <a name="roles"></a>rolls

Net als [scopes](#scopes)bieden rollen een manier voor een [resourceserver](#resource-server) om de toegang tot de beveiligde bronnen te regelen. Er zijn twee typen: een 'gebruiker'-rol implementeert op rollen gebaseerd toegangscontrole voor gebruikers/groepen die toegang tot de bron vereisen, terwijl een 'toepassingsrol' hetzelfde implementeert voor [clienttoepassingen](#client-application) waarvoor toegang nodig is.

Rollen zijn door resources gedefinieerde tekenreeksen (bijvoorbeeld 'Onkostengoedkeuringsbeleid', 'Alleen-lezen', 'Directory.ReadWrite.All'), beheerd in de [Azure-portal][AZURE-portal] via het [toepassingsmanifest](#application-manifest)van de bron en opgeslagen in de [eigenschap appRoles][Graph-Sp-Resource]van de bron. De Azure-portal wordt ook gebruikt om gebruikers toe te wijzen aan 'gebruikersrollen' en [clienttoepassingsmachtigingen](#permissions) te configureren om toegang te krijgen tot een rol met toepassing.

Zie [Graph API Permission Scopes][Graph-Perm-Scopes]voor een gedetailleerde bespreking van de toepassingsrollen die door de Microsoft Graph API worden weergegeven. Zie [Toegang beheren met RBAC en de Azure-portal][AAD-RBAC]voor een stapsgewijs implementatievoorbeeld.

## <a name="scopes"></a>bereiken

Net als [rollen](#roles)bieden scopes een manier voor een [resourceserver](#resource-server) om de toegang tot de beveiligde bronnen te regelen. Scopes worden gebruikt voor het implementeren van [scope-based][OAuth2-Access-Token-Scopes] access control, voor een [clienttoepassing](#client-application) die door de eigenaar gedelegeerde toegang tot de bron heeft gekregen.

Scopes zijn door resources gedefinieerde tekenreeksen (bijvoorbeeld 'Mail.Read', 'Directory.ReadWrite.All'), beheerd in de [Azure-portal][AZURE-portal] via het [toepassingsmanifest](#application-manifest)van de bron en opgeslagen in de [eigenschap oauth2Permissions][Graph-Sp-Resource]van de bron. De Azure-portal wordt ook gebruikt om [gedelegeerde machtigingen](#permissions) voor clienttoepassingen te configureren om toegang te krijgen tot een bereik.

Een afspraak voor het benoemen van aanbevolen procedures is het gebruik van een indeling 'resource.operation.constraint'. Zie [Graph API Permission Scopes][Graph-Perm-Scopes]voor een gedetailleerde bespreking van de scopes die door de Microsoft Graph API worden weergegeven. Zie [Office 365 API-machtigingen verwijzing][O365-Perm-Ref]voor scopes die worden weergegeven door Office 365-services.

## <a name="security-token"></a>Beveiligingstoken

Een ondertekend document met claims, zoals een OAuth2-token of EEN SAML 2.0-bewering. Voor een OAuth2-autorisatieverlening zijn een [access token](#access-token) (OAuth2) en een [ID-token](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) typen beveiligingstokens, die beide worden geïmplementeerd als een [JSON-webtoken (JWT).][JWT] [authorization grant](#authorization-grant)

## <a name="service-principal-object"></a>hoofdobject van de service

Wanneer u een toepassing registreert/updatet in de [Azure-portal,][AZURE-portal]maakt/werkt de portal zowel een [toepassingsobject](#application-object) als een bijbehorend servicehoofdobject voor die tenant. Het toepassingsobject *definieert* de identiteitsconfiguratie van de toepassing wereldwijd (voor alle tenants waar de bijbehorende toepassing toegang heeft gekregen) en is de sjabloon waaruit het bijbehorende servicehoofdobject(en) is *afgeleid* voor lokaal gebruik tijdens run-time (in een specifieke tenant).

Zie [Hoofdobjecten voor toepassingen en servicetoepassingen][AAD-App-SP-Objects]voor meer informatie.

## <a name="sign-in"></a>aanmelden

Het proces van een [clienttoepassing](#client-application) die verificatie door eindgebruikers initieert en de gerelateerde status vastlegt, met het oog op het verkrijgen van een [beveiligingstoken](#security-token) en het aanskaderen van de toepassingssessie naar die status. Status kan artefacten bevatten, zoals gebruikersprofielinformatie en informatie die is afgeleid van tokenclaims.

De aanmeldingsfunctie van een toepassing wordt meestal gebruikt voor het implementeren van single-sign-on (SSO). Het kan ook worden voorafgegaan door een "aanmeldingsfunctie", als toegangspunt voor een eindgebruiker om toegang te krijgen tot een toepassing (bij de eerste aanmelding). De aanmeldingsfunctie wordt gebruikt om extra status te verzamelen en voort te houden die specifiek is voor de gebruiker en vereist mogelijk [toestemming van de gebruiker.](#consent)

## <a name="sign-out"></a>afmelden

Het proces van het niet-verifiëren van een eindgebruiker, waarbij de gebruikersstatus die is gekoppeld aan de [clienttoepassingssessie](#client-application) tijdens [het aanmelden](#sign-in) wordt losgekoppeld

## <a name="tenant"></a>tenant

Een instantie van een Azure AD-map wordt een Azure AD-tenant genoemd. Het biedt verschillende functies, waaronder:

* een registerservice voor geïntegreerde toepassingen
* verificatie van gebruikersaccounts en geregistreerde toepassingen
* REST-eindpunten die nodig zijn om verschillende protocollen te ondersteunen, waaronder OAuth2 en SAML, waaronder het [autorisatieeindpunt,](#authorization-endpoint) [tokeneindpunt](#token-endpoint) en het 'gemeenschappelijke' eindpunt dat wordt gebruikt door [multi-tenanttoepassingen](#multi-tenant-application).

Azure AD-tenants worden tijdens het aanmelden gemaakt/gekoppeld aan Azure- en Office 365-abonnementen, waardoor & Access Management-functies voor het abonnement worden verstrekt. Azure-abonnementsbeheerders kunnen ook extra Azure AD-tenants maken via de Azure-portal. Zie [Hoe u een Azure Active Directory-tenant krijgen][AAD-How-To-Tenant] voor meer informatie over de verschillende manieren waarop u toegang krijgen tot een tenant. Zie [hoe Azure-abonnementen zijn gekoppeld aan Azure Active Directory][AAD-How-Subscriptions-Assoc] voor details over de relatie tussen abonnementen en een Azure AD-tenant.

## <a name="token-endpoint"></a>tokeneindpunt

Een van de eindpunten die door de [autorisatieserver](#authorization-server) zijn geïmplementeerd om OAuth2-autorisatieverleningen te ondersteunen. [authorization grants](#authorization-grant) Afhankelijk van de subsidie kan het worden gebruikt om een [toegangstoken](#access-token) (en bijbehorend 'refresh'-token) te verkrijgen voor een [client](#client-application)of [ID-token](#id-token) wanneer het wordt gebruikt met het [OpenID Connect-protocol.][OpenIDConnect]

## <a name="user-agent-based-client"></a>Op gebruikersagent gebaseerde client

Een type [clienttoepassing](#client-application) dat code van een webserver downloadt en wordt uitgevoerd binnen een gebruikersagent (bijvoorbeeld een webbrowser), zoals een toepassing met één pagina (SPA). Aangezien alle code wordt uitgevoerd op een apparaat, wordt het beschouwd als een "openbare" client vanwege het onvermogen om referenties privé / vertrouwelijk op te slaan. Zie [OAuth2-clienttypen en -profielen][OAuth2-Client-Types]voor meer informatie.

## <a name="user-principal"></a>aangever van de gebruiker

Net als bij de manier waarop een hoofdobject voor de service wordt gebruikt om een toepassingsinstantie weer te geven, is een hoofdobject van de gebruiker een ander type beveiligingsprincipal, dat een gebruiker vertegenwoordigt. Het resourcetype Microsoft [Graph-gebruiker][Graph-User-Resource] definieert het schema voor een gebruikersobject, inclusief gebruikersgerelateerde eigenschappen zoals voor- en achternaam, gebruikersnaam, directoryrollidmaatschap, enz. Dit biedt de configuratie van de gebruikersidentiteit voor Azure AD om een gebruikersprincipal op run-time vast te stellen. De user principal wordt gebruikt om een geverifieerde gebruiker [consent](#consent) te vertegenwoordigen voor Single Sign-On, het opnemen van toestemmingsdelegatie, het nemen van toegangscontrolebeslissingen, enz.

## <a name="web-client"></a>webclient

Een type [clienttoepassing](#client-application) dat alle code uitvoert op een webserver en in staat is om te functioneren als een "vertrouwelijke" client door de referenties op de server veilig op te slaan. Zie [OAuth2-clienttypen en -profielen][OAuth2-Client-Types]voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

De [Microsoft-identiteitsplatform Developer's Guide][AAD-Dev-Guide] is de bestemmingspagina die u gebruiken voor alle onderwerpen die betrekking hebben op de ontwikkeling van microsoft-identiteitsplatforms, inclusief een overzicht van [toepassingsintegratie][AAD-How-To-Integrate] en de basisprincipes van verificatie van [het Microsoft-identiteitsplatform en ondersteunde verificatiescenario's.][AAD-Auth-Scenarios] U ook codevoorbeelden vinden & zelfstudies over hoe u snel aan de slag op [GitHub.](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=)

Gebruik de volgende opmerkingensectie om feedback te geven en deze inhoud te verfijnen en vorm te geven, inclusief verzoeken om nieuwe definities of het bijwerken van bestaande inhoud!

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[Graph-Perm-Scopes]: /graph/permissions-reference
[Graph-App-Resource]: /graph/api/resources/application
[Graph-Sp-Resource]: /graph/api/resources/serviceprincipal?view=graph-rest-beta
[Graph-User-Resource]: /graph/api/resources/user
[AAD-How-Subscriptions-Assoc]:../fundamentals/active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]:quickstart-create-new-tenant.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Multi-Tenant-Overview]:howto-convert-app-to-be-multi-tenant.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AZURE-portal]: https://portal.azure.com
[AAD-RBAC]: ../../role-based-access-control/role-assignments-portal.md
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://developer.microsoft.com/graph
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
