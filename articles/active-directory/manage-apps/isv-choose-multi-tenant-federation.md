---
title: Kies het juiste federatieprotocol voor multi-tenanttoepassing
description: Richtlijnen voor onafhankelijke softwareleveranciers over de integratie met Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b3edbbe037c3874d639476e516b3732b7573d9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443378"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Kies het juiste federatieprotocol voor uw multi-tenant toepassing

Wanneer u uw software as a service (SaaS)-toepassing ontwikkelt, moet u het federatieprotocol selecteren dat het beste voldoet aan de behoeften van u en uw klanten. Deze beslissing is gebaseerd op uw ontwikkelingsplatform en uw wens om te integreren met gegevens die beschikbaar zijn in het Office 365- en Azure AD-ecosysteem van uw klanten.

Bekijk de volledige lijst met [protocollen die beschikbaar zijn voor SSO-integraties](what-is-single-sign-on.md) met Azure Active Directory.
In de volgende tabel wordt 
* Verificatie 2.0 openen (OAuth 2.0)
* Open ID Connect (OIDC)
* Security Assertion Markup Language (SAML)
* Web Services Federation (WSFed)

| Mogelijkheid| OAuth / OIDC| SAML / WSFed |
| - |-|-|
| Web-based Single sign-on| √| √ |
| Op internet gebaseerde afmelding| √| √ |
| Op mobiel gebaseerde enkele aanmelding| √| √* |
| Eenmalig aanmelden op basis van mobiel| √| √* |
| Beleid voor voorwaardelijke toegang voor mobiele toepassingen| √| X |
| Naadloze MFA-ervaring voor mobiele toepassingen| √| X |
| Microsoft Graph openen| √| X |

*Mogelijk, maar Microsoft biedt geen voorbeelden of richtlijnen.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 en Open ID Connect

OAuth 2.0 is een [industriestandaard](https://oauth.net/2/) protocol voor autorisatie. OIDC (OpenID Connect) is een [industriestandaard](https://openid.net/connect/) identiteitsverificatielaag die is gebouwd bovenop het OAuth 2.0-protocol.

### <a name="benefits"></a>Voordelen

Microsoft raadt aan oidc/OAuth 2.0 te gebruiken omdat verificatie en autorisatie zijn ingebouwd in de protocollen. Met SAML moet u bovendien autorisatie implementeren.

De autorisatie die inherent is aan deze protocollen stelt uw toepassing in staat om toegang te krijgen tot en te integreren met uitgebreide gebruikers- en organisatiegegevens via de Microsoft Graph API.

Het gebruik van OAuth 2.0 en OIDC vereenvoudigt de eindgebruikerservaring van uw klanten bij de vaststelling van SSO voor uw toepassing. U eenvoudig de benodigde machtigingssets definiëren, die vervolgens automatisch worden weergegeven aan de beheerder of de instemming van de eindgebruiker.

Bovendien stelt het gebruik van deze protocollen uw klanten in staat om voorwaardelijke toegang en MFA-beleid te gebruiken om de toegang tot de toepassingen te beheren. Microsoft biedt bibliotheken en [codevoorbeelden op meerdere technologieplatforms](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) om uw ontwikkeling te helpen.  

### <a name="implementation"></a>Implementatie

U registreert uw toepassing bij Microsoft Identity, een OAuth 2.0-provider. U uw OAuth 2.0-gebaseerde applicatie ook registreren bij elke andere Identity Provider waarmee u wilt integreren. 

Zie [Toegang tot webtoepassingen beheren met OpenID Connect en Azure Active Directory](../develop/sample-v2-code.md)voor informatie over het registreren van uw toepassing en het implementeren van deze protocollen voor SSO naar webapps.  Zie het volgende voor informatie over het implementeren van deze protocollen voor SSO in mobiele apps: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Universeel Windows-platform](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 en WSFed

Security Assertion Markup Language (SAML) wordt meestal gebruikt voor webtoepassingen. Zie [hoe Azure het SAML-protocol gebruikt](../develop/active-directory-saml-protocol-reference.md) voor een overzicht. 

Web Services Federation (WSFed) is een [industriestandaard](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) die over het algemeen wordt gebruikt voor webapplicaties die worden ontwikkeld met behulp van het .Net-platform.

### <a name="benefits"></a>Voordelen

SAML 2.0 is een volwassen standaard en de meeste technologieplatforms ondersteunen open-source bibliotheken voor SAML 2.0. U uw klanten een beheerinterface bieden om SAML SSO te configureren. Ze kunnen SAML SSO configureren voor Microsoft Azure AD en elke andere identiteitsprovider die SAML 2 ondersteunt

### <a name="trade-offs"></a>Afwegingen

Bij het gebruik van SAML 2.0- of WSFed-protocollen voor mobiele toepassingen hebben bepaalde conditional access-beleidsregels, waaronder Multi-factor Authentication (MFA), een verminderde ervaring. Als u toegang wilt tot de Microsoft Graph, moet u bovendien autorisatie implementeren via OAuth 2.0 om de benodigde tokens te genereren. 

### <a name="implementation"></a>Implementatie

Microsoft biedt geen bibliotheken voor saml-implementatie of beveelt specifieke bibliotheken aan. Er zijn veel open-source bibliotheken beschikbaar.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO en Microsoft Graph Rest API gebruiken 

Microsoft Graph is de gegevensstructuur voor alle Microsoft 365, waaronder Office 365, Windows 10 en Enterprise Mobility and Security, en aanvullende producten zoals Dynamics 365. Dit omvat de kernschema's van de entiteiten zoals Gebruikers, Groepen, Agenda, E-mail, Bestanden en meer, die de productiviteit van de gebruiker verhogen. Microsoft Graph biedt drie interfaces voor ontwikkelaars een REST-gebaseerde API, Microsoft Graph-gegevensverbinding en Connectoren waarmee ontwikkelaars hun eigen gegevens kunnen toevoegen aan de Microsoft Graph.  

Met behulp van een van de bovenstaande protocollen voor SSO kan uw toepassing toegang krijgen tot de uitgebreide gegevens die beschikbaar zijn via de Microsoft Graph REST API. Hierdoor kunnen uw klanten meer waarde halen uit hun investering in Microsoft 365. Uw toepassing kan bijvoorbeeld de Microsoft Graph API aanroepen om te integreren met het Office 365-exemplaar van uw klanten en microsoft Office- en SharePoint-items van Surface-gebruikers in uw toepassing. 

Als u Open ID Connect gebruikt om te verifiëren, is uw ontwikkelingservaring naadloos omdat u OAuth2, de basis van Open ID Connect, gebruikt om tokens te verkrijgen, kan worden gebruikt voor het aanroepen van Microsoft Graph API's. Als uw toepassing SAML of WSFed gebruikt, moet u extra code toevoegen in uw toepassing om deze OAuth2 te krijgen om de tokens te verkrijgen die nodig zijn om een beroep te doen op Microsoft Graph API's. 

## <a name="next-steps"></a>Volgende stappen

[SSO inschakelen voor uw toepassing met meerdere tenants](isv-sso-content.md)

[Documentatie maken voor uw multi-tenant toepassing](isv-create-sso-documentation.md)
