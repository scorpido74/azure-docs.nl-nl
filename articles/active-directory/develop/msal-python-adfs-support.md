---
title: Azure AD FS-ondersteuning (MSAL Python)
titleSuffix: Microsoft identity platform
description: Meer informatie over AD FS-ondersteuning (Active Directory Federation Services) in Microsoft-verificatiebibliotheek voor Python
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/23/2019
ms.author: abpati
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 01d4cb626aabc83117e864b75b49eec63a6c0af0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76699543"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>Active Directory Federation Services-ondersteuning in MSAL voor Python

Met Active Directory Federation Services (AD FS) in Windows Server u verificatie en autorisatie op basis van OpenID Connect en OAuth 2.0 toevoegen aan uw apps met behulp van de Microsoft Authentication Library (MSAL) voor Python. Met de MSAL voor Python-bibliotheek kan uw app gebruikers rechtstreeks verifiëren tegen AD FS. Zie [AD FS-scenario's voor ontwikkelaars voor](/windows-server/identity/ad-fs/ad-fs-development)meer informatie over scenario's.

Er zijn meestal twee manieren om te authenticeren tegen AD FS:

- MSAL Python praat met Azure Active Directory, dat zelf wordt gefedeerd met andere identiteitsproviders. De federatie gebeurt via AD FS. MSAL Python maakt verbinding met Azure AD, dat gebruikers aantekent die worden beheerd in Azure AD (beheerde gebruikers) of gebruikers die worden beheerd door een andere identiteitsprovider, zoals AD FS (federatieve gebruikers). MSAL Python weet niet dat een gebruiker gefedereerd is. Het praat gewoon met Azure AD. De [autoriteit die](msal-client-application-configuration.md#authority) u in dit geval gebruikt, is de gebruikelijke autoriteit (naam van de instantie host + tenant, common of organisaties).
- MSAL Python praat rechtstreeks met een AD FS-autoriteit. Dit wordt alleen ondersteund door AD FS 2019 en later.

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>Verbinding maken met Active Directory met AD FS

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>Een token interactief aanschaffen voor een federatieve gebruiker

Dit geldt als u rechtstreeks verbinding maakt met Active Directory Federation Services (AD FS) of via Active Directory.

Wanneer u `acquire_token_by_authorization_code` `acquire_token_by_device_flow`belt of de gebruikerservaring is, is meestal als volgt:

1. De gebruiker voert zijn account-ID in.
2. Azure AD geeft kort het bericht 'U naar de pagina van uw organisatie' weer en de gebruiker wordt doorgestuurd naar de aanmeldingspagina van de identiteitsprovider. De aanmeldingspagina wordt meestal aangepast met het logo van de organisatie.

De ondersteunde AD FS-versies in dit federatieve scenario zijn:
- Active Directory Federation Services FS v2
- Active Directory Federation Services v3 (Windows Server 2012 R2)
- Active Directory Federation Services v4 (AD FS 2016)

### <a name="acquire-a-token-via-username-and-password"></a>Een token verkrijgen via gebruikersnaam en wachtwoord

Dit geldt als u rechtstreeks verbinding maakt met Active Directory Federation Services (AD FS) of via Active Directory.

Wanneer u een `acquire_token_by_username_password`token aanneemt met behulp van , laat MSAL Python de identiteitsprovider contact opnemen op basis van de gebruikersnaam. MSAL Python krijgt een [SAML 1.1-token](reference-saml-tokens.md) van de identiteitsprovider, die het vervolgens aan Azure AD levert, waarmee het JSON-webtoken (JWT) wordt geretourneerd.

## <a name="connecting-directly-to-ad-fs"></a>Rechtstreeks verbinding maken met AD FS

Wanneer u directory verbindt met AD FS, is de autoriteit die u wilt gebruiken om uw toepassing te bouwen,`https://somesite.contoso.com/adfs/`

MSAL Python ondersteunt ADFS 2019.

Het ondersteunt geen directe verbinding met ADFS 2016 of ADFS v2. Als u scenario's moet ondersteunen die een directe verbinding met ADFS 2016 vereisen, gebruikt u de nieuwste versie van ADAL Python. Zodra u uw on-premises systeem hebt geüpgraded naar ADFS 2019, u MSAL Python gebruiken.

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure Active Directory-aanmeldingsgedrag configureren voor een toepassing met behulp van een Home Realm Discovery-beleid voor](../manage-apps/configure-authentication-for-federated-users-portal.md) de federatieve aanvraag.