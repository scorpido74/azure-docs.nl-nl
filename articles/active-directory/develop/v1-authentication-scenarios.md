---
title: Verificatie in het micro soft Identity platform (v 1.0) | Azure
description: "Meer informatie over de basis principes van verificatie in het micro soft Identity-platform: het app-model, de API, het inrichten en de meest voorkomende verificatie scenario's die door micro soft Identity platform worden ondersteund."
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/14/2019
ms.author: ryanwi
ms.reviewer: saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c7bf80c0471853008573bbcbb918fb33c6c43cd
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73149174"
---
# <a name="what-is-authentication"></a>Wat is verificatie?

Bij *verificatie* wordt een partij gevraagd om legitieme referenties. Op basis hiervan kan een beveiligingsprincipal worden gemaakt voor identiteits- en toegangsbeheer. Eenvoudiger gezegd wordt bij dit proces bewezen dat u bent wie u zegt te zijn. Verificatie wordt soms afgekort tot 'AuthN'.

Bij *autorisatie* krijgt een geverifieerde beveiligingsprincipal toestemming om iets te doen. Er wordt aangegeven welke gegevens mogen worden gebruikt en wat ermee mag worden gedaan. Autorisatie wordt soms afgekort tot 'AuthZ'.

Micro soft Identity platform vereenvoudigt verificatie voor toepassings ontwikkelaars door identiteit als een service te bieden, met ondersteuning voor industrie-standaard protocollen zoals OAuth 2,0 en OpenID Connect Connect, evenals open-source-bibliotheken voor verschillende platformen tot hulp bij het snel starten van code ring.

Er zijn twee primaire use-cases in het micro soft Identity platform-programmeer model:

* Tijdens een OAuth 2.0-stroom voor autorisatie - wanneer de resource-eigenaar autorisatie verleent aan de clienttoepassing, zodat de client toegang verkrijgt tot de resources van de resource-eigenaar.
* Wanneer de client resources opent - zoals geïmplementeerd door de resourceserver, met gebruik van de claimwaarden in het toegangstoken, om toegangsbeheerbeslissingen te nemen op basis van die waarden.

## <a name="authentication-basics-in-microsoft-identity-platform"></a>Basis beginselen van verificatie in het micro soft Identity-platform

Bedenk het meest eenvoudige scenario waarin identiteiten zijn vereist: een gebruiker moet zich in een webbrowser verifiëren bij een webtoepassing. In het volgende diagram is dit scenario te zien:

![Overzicht van het aanmelden bij een webtoepassing](./media/v1-authentication-scenarios/auth-basics-microsoft-identity-platform.svg)

Hier volgt wat u moet weten over de verschillende onderdelen in het diagram:

* Micro soft Identity platform is de ID-provider. De identiteitsprovider is verantwoordelijk voor het verifiëren van de identiteit van gebruikers en toepassingen uit de directory van een organisatie. Er worden ook beveiligingstokens uitgegeven nadat die gebruikers en toepassingen zijn geverifieerd.
* Een toepassing die authenticatie voor het micro soft-identiteits platform wil uitbesteden, moet zijn geregistreerd in Azure Active Directory (Azure AD). Azure AD registreert de app in de directory en geeft deze een unieke id.
* Ontwikkel aars kunnen gebruikmaken van de open-source verificatie bibliotheken van micro soft Identity platform om eenvoudig verificatie te maken door de protocol gegevens voor u te verwerken. Zie verificatie bibliotheken van micro soft Identity platform [v 2.0](reference-v2-libraries.md) en [v 1.0-verificatie bibliotheken](active-directory-authentication-libraries.md)voor meer informatie.
* Wanneer een gebruiker is geverifieerd, moet de toepassing het beveiligingstoken van de gebruiker valideren om er zeker van te zijn dat de verificatie is gelukt. U kunt snelstartgidsen, zelfstudies, codevoorbeelden in verschillende talen en frameworks bekijken waarin u ziet wat de toepassing moet doen.
  * Om snel een app te bouwen en functionaliteit toe te voegen, zoals tokens ophalen, tokens vernieuwen, gebruikers aanmelden, bepaalde gebruikersgegevens weergeven, enzovoort, gaat u naar het gedeelte **Snelstartgidsen** in de documentatie.
  * Ga naar het gedeelte **Zelfstudies** van de documentatie voor uitgebreide, op scenario's gebaseerde procedures voor ontwikkelaarstaken (zoals toegangstokens verkrijgen en deze gebruiken in aanroepen naar de Microsoft Graph-API en andere API's en aanmelden met Microsoft implementeren in een traditionele webbrowser-app via OpenID Connect).
  * Ga naar [GitHub](https://github.com/Azure-Samples?q=active-directory) om codevoorbeelden te downloaden.
* De stroom van aanvragen en antwoorden in het verificatieproces wordt bepaald aan de hand van het gebruikte verificatieprotocol, zoals OAuth 2.0, OpenID Connect, WS-Federation of SAML 2.0. Zie de sectie **concepten > Authentication Protocol** (Engelstalig) in de documentatie voor meer informatie over protocollen.

In het bovenstaande voorbeeldscenario kunt u apps classificeren aan de hand van deze twee rollen:

* Apps die veilig toegang nodig hebben tot resources
* Apps die zelf de rol van resource hebben

### <a name="how-each-flow-emits-tokens-and-codes"></a>Hoe elke stroom tokens en codes uitstraalt

Afhankelijk van hoe uw client is gebouwd, kunnen er één (of meerdere) verificatie stromen worden gebruikt die worden ondersteund door het micro soft Identity-platform.  Deze stromen kunnen een aantal tokens (id_tokens, vernieuwings tokens, toegangs tokens) en autorisatie codes produceren, en vereisen verschillende tokens om ze te laten werken. Dit diagram bevat een overzicht:

|Stroom | Nodig | id_token | toegangs token | token vernieuwen | autorisatie code | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Autorisatie code stroom](v1-protocols-oauth-code.md) | | x | x | x | x|  
|[Impliciete stroom](v1-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybride OIDC-stroom](v1-protocols-openid-connect-code.md#get-access-tokens)| | x  | |          |            x   |
|[Aflossingen van token vernieuwen](v1-protocols-oauth-code.md#refreshing-the-access-tokens) | token vernieuwen | x | x | x| |
|[Namens-stroom](v1-oauth2-on-behalf-of-flow.md) | toegangs token| x| x| x| |
|[Clientreferenties](v1-oauth2-client-creds-grant-flow.md) | | | x (alleen app)| | |

Tokens die zijn uitgegeven via de impliciete modus, hebben een beperkte lengte omdat ze via de URL worden teruggestuurd naar de browser (waarbij `response_mode` is `query` of `fragment`).  Voor sommige browsers geldt een limiet voor de grootte van de URL die in de browser balk kan worden geplaatst en die kan worden uitgevoerd als deze te lang is.  Deze tokens hebben dus geen `groups`-of `wids`-claims. 

Nu u een overzicht van de basis principes hebt, leest u meer over het identiteits-app-model en de API, hoe Provisioning werkt in het micro soft Identity platform en koppelingen naar gedetailleerde informatie over de algemene scenario's die door micro soft Identity platform worden ondersteund.

## <a name="application-model"></a>Toepassingsmodel

Micro soft Identity platform vertegenwoordigt toepassingen volgens een specifiek model dat is ontworpen om te voldoen aan twee belang rijke functies:

* **De app identificeren op basis van de ondersteunde verificatieprotocollen** - dit omvat het inventariseren van alle id's, URL's, geheimen en gerelateerde informatie die nodig is tijdens de verificatie. Hier ziet u het micro soft Identity-platform:

    * Bewaart alle gegevens die nodig zijn om verificatie tijdens runtime te ondersteunen.
    * Bewaart alle gegevens om te beslissen welke resources een app mogelijk nodig heeft en of aanvragen moeten worden uitgevoerd (en onder welke omstandigheden).
    * Biedt de infrastructuur voor het implementeren van app-inrichting in de tenant van de app-ontwikkelaar en in andere Azure AD-tenants.

* Verwerk **de toestemming van de gebruiker tijdens de tijd van de token aanvraag en vereenvoudigt het dynamische inrichten van apps via tenants** -hier, het micro soft Identity-platform:

    * Stelt gebruikers en beheerders in staat om de app dynamisch toestemming te geven of weigeren om resources namens hen te gebruiken.
    * Stelt beheerders in staat om te beslissen wat apps mogen doen, welke gebruikers gebruik mogen maken van specifieke apps en hoe de directoryresources kunnen worden benaderd.

In het micro soft Identity-platform beschrijft een **toepassings object** een toepassing als een abstracte entiteit. Ontwikkelaars werken met toepassingen. Tijdens de implementatie gebruikt micro soft Identity platform een gegeven toepassings object als een blauw druk om een **Service-Principal**te maken. Dit is een concreet exemplaar van een toepassing in een directory of Tenant. De service-principal bepaalt wat de app daadwerkelijk kan doen in een specifieke doeldirectory, wie deze mag gebruiken, tot welke resources deze toegang heeft en meer. Met micro soft Identity platform maakt u via **toestemming**een service-principal van een toepassings object.

Het volgende diagram toont een vereenvoudigd micro soft Identity platform inrichtings stroom op basis van toestemming.  Er bestaan twee tenants (A en B), waarbij Tenant A eigenaar is van de toepassing, en Tenant B de toepassing instantiëren via een service-principal.  

![Vereenvoudigde inrichtingsstroom op basis van toestemming](./media/v1-authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

De inrichtingsstroom verloopt als volgt:

1. Een gebruiker van Tenant B probeert zich aan te melden bij de app. het autorisatie-eind punt vraagt een token voor de toepassing aan.
1. De gebruikers referenties zijn verkregen en geverifieerd voor verificatie
1. De gebruiker wordt gevraagd toestemming te geven voor de app om toegang te krijgen tot Tenant B
1. Micro soft Identity platform gebruikt het toepassings object in Tenant A als een blauw druk voor het maken van een Service-Principal in Tenant B
1. De gebruiker ontvangt de aangevraagde token

U kunt dit proces zo vaak herhalen als u wilt (voor andere tenants, zoals C, D, enzovoort). Tenant A behoudt de blauw druk voor de app (toepassings object). De gebruikers en beheerders van de andere tenants waarvoor de app toestemming heeft, behouden controle over wat de toepassing mag doen. Dit kan worden beheerd met het bijbehorende service-principalobject in elke tenant. Zie [Application and Service Principal Objects in micro soft Identity platform](app-objects-and-service-principals.md)(Engelstalig) voor meer informatie.

## <a name="claims-in-microsoft-identity-platform-security-tokens"></a>Claims in micro soft Identity platform-beveiligings tokens

Beveiligings tokens (toegangs-en ID-tokens) die zijn uitgegeven door het micro soft Identity-platform bevatten claims of bevestigingen van informatie over het onderwerp dat is geverifieerd. Toepassingen kunnen voor verschillende taken gebruikmaken van claims, waaronder:

* Het token valideren
* De directorytenant van het onderwerp identificeren
* Gebruikersgegevens weergeven
* De autorisatie van het onderwerp bepalen

Welke claims aanwezig zijn in een bepaald beveiligingstoken, is afhankelijk van het type token, het type referentie dat is gebruikt om de gebruiker te verifiëren en de configuratie van de toepassing.

In de onderstaande tabel vindt u een korte beschrijving van elk type claim dat wordt verzonden door het micro soft Identity-platform. Zie het [toegangs tokens](access-tokens.md) en de [id-tokens](id-tokens.md) die zijn uitgegeven door micro soft Identity platform voor meer gedetailleerde informatie.

| Claim | Beschrijving |
| --- | --- |
| Toepassings-id | Identificeert de toepassing die gebruikmaakt van het token. |
| Doelgroep | Identificeert de ontvangende resource waar het token voor is bedoeld. |
| Application Authentication Context Class Reference | Geeft aan hoe de client is geverifieerd (openbare client of vertrouwelijke client). |
| Verificatiemoment | Registreert de datum en tijd waarop de verificatie heeft plaatsgevonden. |
| Verificatiemethode | Geeft aan hoe het onderwerp van het token is geverifieerd (wachtwoord, certificaat, enzovoort). |
| Voornaam | Biedt de naam van de gebruiker zoals ingesteld in Azure AD. |
| Groepen | Bevat de object-id's van de Azure AD-groepen waar de gebruiker lid van is. |
| Id-provider | Registreert de identiteitsprovider waarmee het onderwerp van het token is geverifieerd. |
| Uitgegeven om | Registreert de tijd waarop het token is uitgegeven (vaak gebruikt om te controleren hoe nieuw het token is). |
| Verlener | Identificeert de beveiligingstokenservice die het token heeft uitgegeven en de Azure AD-tenant. |
| Achternaam | Biedt de achternaam van de gebruiker, zoals ingesteld in Azure AD. |
| Naam | Biedt een voor mensen leesbare waarde waarmee het onderwerp van het token wordt geïdentificeerd. |
| Object-id | Bevat een onveranderbare, unieke id voor het onderwerp in Azure AD. |
| Rollen | Bevat beschrijvende namen voor de Azure AD-toepassingsrollen die zijn toegewezen aan de gebruiker. |
| Scope | Geeft aan welke machtigingen zijn verleend aan de clienttoepassing. |
| Onderwerp | Geeft aan over welke principal het token informatie bevat. |
| Tenant-id | Bevat een onveranderbare, unieke id voor de directorytenant die het token heeft uitgegeven. |
| Levensduur van token | Definieert gedurende welke periode een token geldig is. |
| User principal name | Bevat de user principal name van het onderwerp. |
| Version | Bevat het versienummer van het token. |

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [toepassings typen en scenario's die worden ondersteund in micro soft Identity platform](app-types.md)
