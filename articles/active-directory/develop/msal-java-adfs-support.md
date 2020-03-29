---
title: ONDERSTEUNING voor AD FS (MSAL voor Java)
titleSuffix: Microsoft identity platform
description: Meer informatie over AD FS-ondersteuning (Active Directory Federation Services) in Microsoft Authentication Library for Java (MSAL4j).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/21/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 3d834a8d1524595304c22fed9897094622dfd93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696211"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>Active Directory Federation Services-ondersteuning in MSAL voor Java

Met Active Directory Federation Services (AD FS) in Windows Server u verificatie en autorisatie op basis van OpenID Connect en OAuth 2.0 toevoegen aan uw Microsoft Authentication Library for Java (MSAL for Java)-app. Nadat deze app is geïntegreerd, kan uw app gebruikers verifiëren in AD FS, die wordt gefedereerd via Azure AD. Zie [AD FS-scenario's voor ontwikkelaars voor](/windows-server/identity/ad-fs/ad-fs-development)meer informatie over scenario's.

Een app die MSAL voor Java gebruikt, praat met Azure Active Directory (Azure AD), die vervolgens naar AD FS wordt gefingeert.

MSAL voor Java maakt verbinding met Azure AD, dat wordt inlogt bij gebruikers die worden beheerd in Azure AD (beheerde gebruikers) of gebruikers die worden beheerd door een andere identiteitsprovider, zoals AD FS (federatieve gebruikers). MSAL voor Java weet niet dat een gebruiker is gefedeerd. Het praat gewoon met Azure AD.

De [autoriteit die](msal-client-application-configuration.md#authority) u in dit geval gebruikt, is de gebruikelijke autoriteit (naam van de instantie host + tenant, common of organisaties).

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>Een token interactief aanschaffen voor een federatieve gebruiker

Wanneer u `ConfidentialClientApplication.AcquireToken()` `PublicClientApplication.AcquireToken()` belt `AuthorizationCodeParameters` `DeviceCodeParameters`of met of met of , de gebruikerservaring is meestal:

1. De gebruiker voert zijn account-ID in.
2. Azure AD geeft kort 'U naar de pagina van uw organisatie' weer en de gebruiker wordt doorgestuurd naar de aanmeldingspagina van de identiteitsprovider. De aanmeldingspagina wordt meestal aangepast met het logo van de organisatie.

De ondersteunde AD FS-versies in dit federatieve scenario zijn:
- Active Directory Federation Services FS v2
- Active Directory Federation Services v3 (Windows Server 2012 R2)
- Active Directory Federation Services v4 (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>Een token verkrijgen via gebruikersnaam en wachtwoord

Wanneer u een `ConfidentialClientApplication.AcquireToken()` token `PublicClientApplication.AcquireToken()` `IntegratedWindowsAuthenticationParameters` aandoet met of met of met of `UsernamePasswordParameters`, krijgt MSAL voor Java de identiteitsprovider om contact op te nemen op basis van de gebruikersnaam. MSAL voor Java krijgt een [SAML 1.1-token](reference-saml-tokens.md) van de identiteitsprovider, die het vervolgens aan Azure AD levert, waarmee het JSON-webtoken (JWT) wordt geretourneerd.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Active Directory-aanmeldingsgedrag configureren voor een toepassing met behulp van een Home Realm Discovery-beleid voor](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) de federatieve aanvraag.