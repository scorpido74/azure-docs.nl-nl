---
title: Azure AD voor ontwikkelaars (v1.0) | Azure
description: Lees de basisbeginselen voor verificatie voor Azure AD voor ontwikkelaars (v1.0), zoals het app-model, api, inrichting en de meest voorkomende verificatiescenario's.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 10/14/2019
ms.author: ryanwi
ms.reviewer: saeeda, sureshja, hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 36b39f3706db615e40ebfadebf36be4d8b29c33e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154726"
---
# <a name="what-is-authentication"></a>Wat is verificatie?

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Bij *verificatie* wordt een partij gevraagd om legitieme referenties. Op basis hiervan kan een beveiligingsprincipal worden gemaakt voor identiteits- en toegangsbeheer. Eenvoudiger gezegd wordt bij dit proces bewezen dat u bent wie u zegt te zijn. Verificatie wordt soms afgekort tot 'AuthN'.

Bij *autorisatie* krijgt een geverifieerde beveiligingsprincipal toestemming om iets te doen. Er wordt aangegeven welke gegevens mogen worden gebruikt en wat ermee mag worden gedaan. Autorisatie wordt soms afgekort tot 'AuthZ'.

Azure Active Directory voor ontwikkelaars (v1.0) (Azure AD) vereenvoudigt verificatie voor toepassingsontwikkelaars door identiteit als service te bieden, met ondersteuning voor industriestandaardprotocollen zoals OAuth 2.0 en OpenID Connect, evenals open-sourcebibliotheken voor verschillende platforms om u te helpen snel te beginnen met coderen.

Er zijn twee primaire gebruiksscenario's in het Azure AD-programmeermodel:

* Tijdens een OAuth 2.0-stroom voor autorisatie - wanneer de resource-eigenaar autorisatie verleent aan de clienttoepassing, zodat de client toegang verkrijgt tot de resources van de resource-eigenaar.
* Wanneer de client resources opent - zoals geïmplementeerd door de resourceserver, met gebruik van de claimwaarden in het toegangstoken, om toegangsbeheerbeslissingen te nemen op basis van die waarden.

## <a name="authentication-basics-in-azure-ad"></a>Basisbeginselen voor verificatie in Azure AD

Bedenk het meest eenvoudige scenario waarin identiteiten zijn vereist: een gebruiker moet zich in een webbrowser verifiëren bij een webtoepassing. In het volgende diagram is dit scenario te zien:

![Overzicht van het aanmelden bij een webtoepassing](./media/v1-authentication-scenarios/auth-basics-microsoft-identity-platform.svg)

Dit is wat u moet weten over de verschillende onderdelen in het diagram:

* Azure AD is de identiteitsprovider. De identiteitsprovider is verantwoordelijk voor het verifiëren van de identiteit van gebruikers en toepassingen die in de directory van een organisatie aanwezig zijn, en geeft beveiligingstokens uit bij succesvolle verificatie van die gebruikers en toepassingen.
* Een toepassing die verificatie wil uitbesteden aan Azure AD, moet zijn geregistreerd in Azure Active Directory (Azure AD). Azure AD registreert de app in de directory en geeft deze een unieke id.
* Ontwikkelaars kunnen de open-sourceverificatiebibliotheken van Azure AD gebruiken om de verificatie te vereenvoudigen. Hierbij worden de protocolgegevens voor u verwerkt. Zie Microsoft-identiteitsplatform [v2.0-verificatiebibliotheken](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) en [v1.0-verificatiebibliotheken](active-directory-authentication-libraries.md)voor meer informatie.
* Zodra een gebruiker is geverifieerd, moet de toepassing het beveiligingstoken van de gebruiker valideren om ervoor te zorgen dat de verificatie is geslaagd. U kunt snelstartgidsen, zelfstudies, codevoorbeelden in verschillende talen en frameworks bekijken waarin u ziet wat de toepassing moet doen.
  * Om snel een app te bouwen en functionaliteit toe te voegen, zoals tokens ophalen, tokens vernieuwen, gebruikers aanmelden, bepaalde gebruikersgegevens weergeven, enzovoort, gaat u naar het gedeelte **Snelstartgidsen** in de documentatie.
  * Ga naar het gedeelte **Zelfstudies** van de documentatie voor uitgebreide, op scenario's gebaseerde procedures voor ontwikkelaarstaken (zoals toegangstokens verkrijgen en deze gebruiken in aanroepen naar de Microsoft Graph-API en andere API's en aanmelden met Microsoft implementeren in een traditionele webbrowser-app via OpenID Connect).
  * Ga naar [GitHub](https://github.com/Azure-Samples?q=active-directory) om codevoorbeelden te downloaden.
* De stroom van aanvragen en antwoorden in het verificatieproces wordt bepaald aan de hand van het gebruikte verificatieprotocol, zoals OAuth 2.0, OpenID Connect, WS-Federation of SAML 2.0. Zie de sectie Concepts **> Authentication protocol** van de documentatie voor meer informatie over protocollen.

In het bovenstaande voorbeeldscenario kunt u apps classificeren aan de hand van deze twee rollen:

* Apps die veilig toegang nodig hebben tot resources
* Apps die zelf de rol van resource hebben

### <a name="how-each-flow-emits-tokens-and-codes"></a>Hoe elke stroom tokens en codes uitzendt

Afhankelijk van hoe uw client is opgebouwd, kan deze één (of meerdere) van de verificatiestromen gebruiken die worden ondersteund door Azure AD. Deze stromen kunnen een verscheidenheid aan tokens produceren (id_tokens, tokens vernieuwen, toegangstokens) en autorisatiecodes en vereisen verschillende tokens om ze te laten werken. Deze grafiek geeft een overzicht:

|Stroom | Vereist | id_token | toegangstoken | token vernieuwen | autorisatiecode | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Machtigingscodestroom](v1-protocols-oauth-code.md) | | x | x | x | x|  
|[Impliciete stroom](v1-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybride OIDC-stroom](v1-protocols-openid-connect-code.md#get-access-tokens)| | x  | |          |            x   |
|[Tokeninwisseling vernieuwen](v1-protocols-oauth-code.md#refreshing-the-access-tokens) | token vernieuwen | x | x | x| |
|[Namens-stroom](v1-oauth2-on-behalf-of-flow.md) | toegangstoken| x| x| x| |
|[Clientreferenties](v1-oauth2-client-creds-grant-flow.md) | | | x (alleen app)| | |

Tokens uitgegeven via de impliciete modus hebben een lengtebeperking als gevolg `response_mode` van `query` `fragment`wordt doorgegeven terug naar de browser via de URL (waar is of ).  Sommige browsers hebben een limiet op de grootte van de URL die in de browserbalk kan worden geplaatst en mislukken wanneer deze te lang is.  Dus, deze tokens `groups` hebben `wids` geen of claims. 

Nu u bekend bent met de basisinformatie, kunt u verder lezen voor inzicht in het identiteit-app-model en de bijbehorende API, voor meer informatie over inrichten in Azure AD en voor koppelingen naar gedetailleerde informatie over algemene scenario's waar Azure AD ondersteuning voor biedt.

## <a name="application-model"></a>Toepassingsmodel

Azure AD vertegenwoordigt toepassingen met een specifiek model dat speciaal bedoeld is voor twee belangrijke functies:

* **De app identificeren op basis van de ondersteunde verificatieprotocollen** - dit omvat het inventariseren van alle id's, URL's, geheimen en gerelateerde informatie die nodig is tijdens de verificatie. Azure AD doet dan het volgende:

    * Bewaart alle gegevens die nodig zijn om verificatie tijdens runtime te ondersteunen.
    * Bewaart alle gegevens om te beslissen welke resources een app mogelijk nodig heeft en of aanvragen moeten worden uitgevoerd (en onder welke omstandigheden).
    * Biedt de infrastructuur voor het implementeren van app-inrichting in de tenant van de app-ontwikkelaar en in andere Azure AD-tenants.

* **Verwerkt de gebruikerstoestemming tijdens het aanvragen van het token en maakt het dynamisch inrichten van apps in tenants mogelijk** - Hier doet Azure AD het volgende:

    * Stelt gebruikers en beheerders in staat om de app dynamisch toestemming te geven of weigeren om resources namens hen te gebruiken.
    * Stelt beheerders in staat om te beslissen wat apps mogen doen, welke gebruikers gebruik mogen maken van specifieke apps en hoe de directoryresources kunnen worden benaderd.

In Azure AD beschrijft een **toepassingsobject** een toepassing als een abstracte entiteit. Ontwikkelaars werken met toepassingen. Tijdens de implementatie gebruikt Azure AD een specifiek toepassingsobject als blauwdruk om een **service-principal** te maken; deze vertegenwoordigt een concreet exemplaar van een toepassing binnen een directory of tenant. De service-principal bepaalt wat de app daadwerkelijk kan doen in een specifieke doeldirectory, wie deze mag gebruiken, tot welke resources deze toegang heeft en meer. Azure AD maakt op basis van **toestemming** een service-principal van een toepassingsobject.

In het volgende diagram staat een vereenvoudigde Azure AD-inrichtingsstroom op basis van toestemming.  Daarin bestaan twee huurders (A en B), waarbij huurder A eigenaar is van de applicatie, en huurder B de aanvraag via een serviceprincipal instantiating.  

![Vereenvoudigde inrichtingsstroom op basis van toestemming](./media/v1-authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

De inrichtingsstroom verloopt als volgt:

1. Een gebruiker van tenant B probeert zich aan te melden met de app, het eindpunt van de autorisatie vraagt een token voor de toepassing aan.
1. De gebruikersreferenties worden verkregen en geverifieerd voor verificatie
1. De gebruiker wordt gevraagd toestemming te geven voor de app om toegang te krijgen tot tenant B
1. Azure AD gebruikt het toepassingsobject in tenant A als blauwdruk voor het maken van een serviceprincipal in tenant B
1. De gebruiker ontvangt de aangevraagde token

U kunt dit proces zo vaak herhalen als u wilt (voor andere tenants, zoals C, D, enzovoort). Tenant A behoudt de blauwdruk voor de app (toepassingsobject). De gebruikers en beheerders van de andere tenants waarvoor de app toestemming heeft, behouden controle over wat de toepassing mag doen. Dit kan worden beheerd met het bijbehorende service-principalobject in elke tenant. Zie [Hoofdobjecten voor toepassingen en service in het Microsoft-identiteitsplatform](../develop/app-objects-and-service-principals.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)voor meer informatie.

## <a name="claims-in-azure-ad-security-tokens"></a>Claims in Azure AD-beveiligingstokens

Beveiligingstokens (toegangs- en id-tokens) die worden uitgegeven door Azure AD bevatten claims of asserties van informatie over het onderwerp dat is geverifieerd. Toepassingen kunnen voor verschillende taken gebruikmaken van claims, waaronder:

* Het token valideren
* De directorytenant van het onderwerp identificeren
* Gebruikersgegevens weergeven
* De autorisatie van het onderwerp bepalen

Welke claims aanwezig zijn in een bepaald beveiligingstoken, is afhankelijk van het type token, het type referentie dat is gebruikt om de gebruiker te verifiëren en de configuratie van de toepassing.

In de onderstaande tabel staat een korte beschrijving van elk type claim dat door Azure AD wordt uitgegeven. Zie de [toegangstokens](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) en [ID-tokens](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) die zijn uitgegeven door het Azure AD voor meer gedetailleerde informatie.

| Claim | Beschrijving |
| --- | --- |
| Toepassings-id | Identificeert de toepassing die gebruikmaakt van het token. |
| Doelgroep | Identificeert de ontvangende resource waar het token voor is bedoeld. |
| Application Authentication Context Class Reference | Geeft aan hoe de client is geverifieerd (openbare client of vertrouwelijke client). |
| Verificatiemoment | Registreert de datum en tijd waarop de verificatie heeft plaatsgevonden. |
| Verificatiemethode | Geeft aan hoe het onderwerp van het token is geverifieerd (wachtwoord, certificaat, enzovoort). |
| Voornaam | Biedt de naam van de gebruiker zoals ingesteld in Azure AD. |
| Groepen | Bevat de object-id's van de Azure AD-groepen waar de gebruiker lid van is. |
| Identiteitsprovider | Registreert de identiteitsprovider waarmee het onderwerp van het token is geverifieerd. |
| Uitgegeven om | Registreert de tijd waarop het token is uitgegeven (vaak gebruikt om te controleren hoe nieuw het token is). |
| Verlener | Identificeert de beveiligingstokenservice die het token heeft uitgegeven en de Azure AD-tenant. |
| Achternaam | Biedt de achternaam van de gebruiker, zoals ingesteld in Azure AD. |
| Name | Biedt een voor mensen leesbare waarde waarmee het onderwerp van het token wordt geïdentificeerd. |
| Object-id | Bevat een onveranderbare, unieke id voor het onderwerp in Azure AD. |
| Rollen | Bevat beschrijvende namen voor de Azure AD-toepassingsrollen die zijn toegewezen aan de gebruiker. |
| Bereik | Geeft aan welke machtigingen zijn verleend aan de clienttoepassing. |
| Onderwerp | Geeft aan over welke principal het token informatie bevat. |
| Tenant-id | Bevat een onveranderbare, unieke id voor de directorytenant die het token heeft uitgegeven. |
| Levensduur van token | Definieert gedurende welke periode een token geldig is. |
| User principal name | Bevat de user principal name van het onderwerp. |
| Versie | Bevat het versienummer van het token. |

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [toepassingstypen en scenario's die worden ondersteund in het Microsoft-identiteitsplatform](app-types.md)
