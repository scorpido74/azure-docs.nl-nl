---
title: AD FS-steun in MSAL.NET | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over AD FS-ondersteuning (Active Directory Federation Services) in Microsoft Authentication Library voor .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 15af18177cea217612a4d5276d130abe02d339f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160757"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Ondersteuning voor Active Directory Federation Services in MSAL.NET
Met Active Directory Federation Services (AD FS) in Windows Server u verificatie en autorisatie op basis van OpenID Connect en OAuth 2.0 toevoegen aan toepassingen die u ontwikkelt. Deze toepassingen kunnen gebruikers vervolgens rechtstreeks verifiëren tegen AD FS. Lees [AD FS-scenario's voor ontwikkelaars voor](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios)meer informatie.

Microsoft Authentication Library for .NET (MSAL.NET) ondersteunt twee scenario's voor het verifiëren ten opzichte van AD FS:

- MSAL.NET praat met Azure Active Directory, dat zelf wordt *gefedeerd* met AD FS.
- MSAL.NET **rechtstreeks** met een ADFS-instantie in gesprek. Dit wordt alleen ondersteund door AD FS 2019 en hoger. Een van de scenario's die dit benadrukt is [Azure Stack-ondersteuning](https://azure.microsoft.com/overview/azure-stack/)


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL maakt verbinding met Azure AD, dat wordt gefedeerd met AD FS
MSAL.NET ondersteunt verbinding maken met Azure AD, dat beheerde gebruikers (gebruikers die worden beheerd in Azure AD) of federatieve gebruikers (gebruikers die worden beheerd door een andere identiteitsprovider zoals AD FS) ondertekent. MSAL.NET weet niet over het feit dat gebruikers zijn federatief. Wat het betreft, het praat met Azure AD.

De [autoriteit die](msal-client-application-configuration.md#authority) u in dit geval gebruikt, is de gebruikelijke autoriteit (naam van de instantie host + tenant, common of organisaties).

### <a name="acquiring-a-token-interactively"></a>Een token interactief verwerven
Wanneer u `AcquireTokenInteractive` de methode aanroept, is de gebruikerservaring meestal:

1. De gebruiker voert zijn account-ID in.
2. Azure AD geeft kort het bericht 'U naar de pagina van uw organisatie' weer.
3. De gebruiker wordt doorgestuurd naar de aanmeldingspagina van de identiteitsprovider. De aanmeldingspagina wordt meestal aangepast met het logo van de organisatie.

Ondersteunde AD FS-versies in dit federatieve scenario zijn AD FS v2, AD FS v3 (Windows Server 2012 R2) en AD FS v4 (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>Een token aanschaffen met AcquireTokenByIntegratedAuthentication of AcquireTokenByUsernamePassword
Bij het verkrijgen van `AcquireTokenByIntegratedAuthentication` `AcquireTokenByUsernamePassword` een token met behulp van de of methoden, MSAL.NET krijgt de identiteit provider om contact op te nemen op basis van de gebruikersnaam.  MSAL.NET ontvangt een [SAML 1.1-token](reference-saml-tokens.md) nadat u contact hebt opgenomen met de identiteitsprovider.  MSAL.NET biedt vervolgens het SAML-token aan Azure AD als een bewering van de gebruiker (vergelijkbaar met de [stroom namens de stroom)](msal-authentication-flows.md#on-behalf-of)om een JWT terug te krijgen.

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL maakt rechtstreeks verbinding met AD FS
MSAL.NET ondersteunt het verbinden met AD FS 2019, dat Open ID Connect-compatibel is en PKCE en scopes begrijpt. Deze ondersteuning vereist dat een servicepack [KB 4490481](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481) wordt toegepast op Windows Server. Wanneer u rechtstreeks verbinding maakt met AD FS, is de autoriteit `https://mysite.contoso.com/adfs/`die u wilt gebruiken om uw toepassing te bouwen vergelijkbaar met .

Momenteel zijn er geen plannen om een directe verbinding met:

- AD FS 16, omdat het PKCE niet ondersteunt en nog steeds resources gebruikt, geen scope
- AD FS v2, die niet OIDC-compatibel is.

 Als u scenario's moet ondersteunen waarvoor een directe verbinding met AD FS 2016 vereist is, gebruikt u de nieuwste versie van [Azure Active Directory Authentication Library](../azuread-dev/active-directory-authentication-libraries.md#microsoft-supported-client-libraries). Wanneer u uw on-premises systeem hebt geüpgraded naar AD FS 2019, u MSAL.NET gebruiken.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Active Directory-aanmeldingsgedrag configureren voor een toepassing met behulp van een Home Realm Discovery-beleid voor](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) de federatieve aanvraag.
