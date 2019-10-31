---
title: Kies het juiste Federatie protocol voor uw multi tenant-toepassing
description: Richt lijnen voor onafhankelijke software leveranciers op het integreren met Azure Active Directory
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
ms.openlocfilehash: ede458e7d4c1cb1a8d7e3f2e2c9df54d5925d6d8
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175980"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Kies het juiste Federatie protocol voor uw multi tenant-toepassing

Wanneer u uw SaaS-toepassing (Software as a Service) ontwikkelt, moet u het federatieve protocol selecteren dat het beste voldoet aan de behoeften van uw klanten. Deze beslissing is gebaseerd op uw ontwikkel platform en uw wensen te integreren met gegevens die beschikbaar zijn in het Office 365-en Azure AD-ecosysteem van uw klanten.

Bekijk de volledige lijst met [protocollen die beschikbaar zijn voor SSO-integraties](what-is-single-sign-on.md) met Azure Active Directory.
De volgende tabel vergelijkt 
* Open verificatie 2,0 (OAuth 2,0)
* Open ID Connect (OIDC)
* Security Assertion Markup Language (SAML)
* Webservices-federatie (WSFed)

| Mogelijkheid| OAuth-OIDC| SAML-WSFed |
| - |-|-|
| Op het web gebaseerde eenmalige aanmelding| √| √ |
| Op het web gebaseerde eenmalige afmelding| √| √ |
| Eenmalige aanmelding op basis van mobiel| √| √ |
| Op mobiele apparaten gebaseerde eenmalige afmelding| √| √ |
| Beleid voor voorwaardelijke toegang voor mobiele toepassingen| √| X |
| Naadloze MFA-ervaring voor mobiele toepassingen| √| X |
| Toegang Microsoft Graph| √| X |

\* Mogelijk, maar micro soft biedt geen voor beelden of richt lijnen.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2,0 en open ID Connect

OAuth 2,0 is een protocol voor de [industrie standaard](https://oauth.net/2/) voor autorisatie. OIDC (OpenID Connect Connect) is een [industrie standaard](https://openid.net/connect/) -identiteits verificatie laag die boven op het Oath 2,0-protocol is gebouwd.

### <a name="benefits"></a>Voordelen

Micro soft raadt aan om OIDC/OAuth 2,0 te gebruiken als verificatie en autorisatie zijn ingebouwd in de protocollen. Met SAML moet u ook autorisatie implementeren.

Met de autorisatie inherent aan deze protocollen kan uw toepassing toegang krijgen tot en worden geïntegreerd met uitgebreide gebruikers-en organisatie gegevens via de Microsoft Graph-API.

Door OAuth 2,0 en OIDC te gebruiken, vereenvoudigt u de gebruikers ervaring van uw klanten bij het aannemen van SSO voor uw toepassing. U kunt eenvoudig de benodigde machtigingen sets definiëren, die vervolgens automatisch worden weer gegeven aan de beheerder of de eind gebruiker.

Daarnaast kunnen uw klanten met deze protocollen voorwaardelijke toegang en MFA-beleid gebruiken om de toegang tot de toepassingen te beheren. Micro soft biedt bibliotheken en [code voorbeelden op meerdere platformen](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) om u te helpen bij het ontwikkelen.  

### <a name="implementation"></a>Implementatie

U registreert uw toepassing met micro soft-identiteit, een OAuth 2,0-provider. U kunt de op OAuth 2,0 gebaseerde toepassing vervolgens ook registreren bij een andere ID-provider die u wilt integreren met. 

Zie [toegang tot webtoepassingen toestaan met OpenID Connect Connect en Azure Active Directory](../develop/sample-v2-code.md)voor meer informatie over het registreren van uw toepassing en het implementeren van deze protocollen voor SSO naar web apps.  Ga voor informatie over het implementeren van deze protocollen voor SSO in mobiele apps naar het volgende: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Universeel Windows-platform](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2,0 en WSFed

Security Assertion Markup Language (SAML) wordt meestal gebruikt voor webtoepassingen. Bekijk [hoe Azure het SAML-protocol gebruikt](../develop/active-directory-saml-protocol-reference.md) voor een overzicht. 

Webservices-federatie (WSFed) is een [industrie standaard](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) die algemeen wordt gebruikt voor webtoepassingen die zijn ontwikkeld met behulp van het .NET-platform.

### <a name="benefits"></a>Voordelen

SAML 2,0 is een volwassene standaard en de meeste technologie platforms ondersteunen open-source bibliotheken voor SAML 2,0. U kunt uw klanten een beheer interface bieden voor het configureren van SAML SSO. Ze kunnen SAML SSO configureren voor Microsoft Azure AD en elke andere ID-provider die ondersteuning biedt voor SAML 2

### <a name="trade-offs"></a>Commerciële afschrijving

Wanneer u SAML 2,0-of WSFed-protocollen gebruikt voor mobiele toepassingen, heeft bepaalde beleids regels voor voorwaardelijke toegang, waaronder multi-factor Authentication (MFA), een gedegradeerde ervaring. Als u toegang wilt krijgen tot de Microsoft Graph, moet u ook autorisatie implementeren via OAuth 2,0 om de benodigde tokens te genereren. 

### <a name="implementation"></a>Implementatie

Micro soft biedt geen bibliotheken voor SAML-implementatie of adviseert specifieke bibliotheken. Er zijn veel open-source bibliotheken beschikbaar.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO en gebruiken van Microsoft Graph rest API 

Microsoft Graph is de gegevens infrastructuur voor alle Microsoft 365, waaronder Office 365, Windows 10 en Enter prise Mobility en Security, en aanvullende producten zoals Dynamics 365. Dit omvat de kern schema's van de entiteiten zoals gebruikers, groepen, agenda, E-mail, bestanden en meer, die de productiviteit van de gebruiker verhogen. Microsoft Graph biedt ontwikkel aars drie interfaces voor een REST gebaseerde API, Microsoft Graph gegevens verbinding en connectors waarmee ontwikkel aars hun eigen gegevens kunnen toevoegen aan de Microsoft Graph.  

Door gebruik te maken van een van de bovenstaande protocollen voor SSO, kan de toegang van uw toepassing tot de uitgebreide gegevens die beschikbaar zijn via de Microsoft Graph REST API. Zo kunnen uw klanten meer waarde krijgen van hun investering in Microsoft 365. Uw toepassing kan bijvoorbeeld de Microsoft Graph-API aanroepen om te integreren met het Office 365-exemplaar en de Microsoft Office en share point-items van uw klanten in uw toepassing. 

Als u open ID Connect to authenticatie gebruikt, is uw ontwikkel ervaring naadloos omdat u OAuth2 gebruikt, de basis van Open-ID-verbinding voor het verkrijgen van tokens kan worden gebruikt voor het aanroepen van Microsoft Graph-Api's. Als uw toepassing gebruikmaakt van SAML of WSFed, moet u extra code in uw toepassing toevoegen om deze OAuth2 te verkrijgen om de tokens te verkrijgen die nodig zijn om Microsoft Graph-Api's aan te roepen. 

## <a name="next-steps"></a>Volgende stappen

[Eenmalige aanmelding inschakelen voor uw toepassing met meerdere tenants](isv-sso-content.md)

[Documentatie maken voor uw toepassing met meerdere tenants](isv-create-sso-documentation.md)
