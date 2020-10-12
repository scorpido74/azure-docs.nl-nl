---
title: Ondersteuning voor AD FS (MSAL voor Java)
titleSuffix: Microsoft identity platform
description: Meer informatie over de ondersteuning van Active Directory Federation Services (AD FS) in micro soft Authentication Library voor Java (MSAL4j).
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
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 5d01d1143563637e21aaa06a3f997c1507e4d8f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88114775"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>Ondersteuning voor Active Directory Federation Services in MSAL voor Java

Met Active Directory Federation Services (AD FS) in Windows Server kunt u OpenID Connect Connect en OAuth 2,0-verificatie en-autorisatie toevoegen aan uw micro soft Authentication Library voor Java-app (MSAL for Java). Als uw app eenmaal is geïntegreerd, kan deze gebruikers verifiëren in AD FS, federatieve via Azure AD. Zie [AD FS scenario's voor ontwikkel aars](/windows-server/identity/ad-fs/ad-fs-development)voor meer informatie over scenario's.

Een app die gebruikmaakt van MSAL voor Java, wordt gecommuniceerd met Azure Active Directory (Azure AD), die vervolgens federeert naar AD FS.

MSAL voor Java maakt verbinding met Azure AD, waarmee gebruikers die worden beheerd in azure AD (beheerde gebruikers) of gebruikers die worden beheerd door een andere ID-provider, zoals AD FS (federatieve gebruikers), worden aangemeld. MSAL voor Java weet niet dat een gebruiker federatief is. Het gaat gewoon naar Azure AD.

De [instantie](msal-client-application-configuration.md#authority) die u in dit geval gebruikt, is de gebruikelijke instantie (hostnaam van de autoriteit + Tenant, common of organisaties).

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>Een token interactief aanschaffen voor een federatieve gebruiker

Wanneer u belt `ConfidentialClientApplication.AcquireToken()` of `PublicClientApplication.AcquireToken()` met `AuthorizationCodeParameters` of `DeviceCodeParameters` , wordt de gebruikers ervaring meestal:

1. De gebruiker voert de account-ID in.
2. In azure AD wordt weer gegeven hoe u naar de pagina van uw organisatie gaat. de gebruiker wordt omgeleid naar de aanmeldings pagina van de ID-provider. De aanmeldings pagina wordt doorgaans aangepast met het logo van de organisatie.

De ondersteunde AD FS versies in dit federatieve scenario zijn:
- Active Directory Federation Services FS v2
- Active Directory Federation Services v3 (Windows Server 2012 R2)
- Active Directory Federation Services v4 (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>Een token verkrijgen via gebruikers naam en wacht woord

Wanneer u een token aanschaft met `ConfidentialClientApplication.AcquireToken()` of `PublicClientApplication.AcquireToken()` met `IntegratedWindowsAuthenticationParameters` of `UsernamePasswordParameters` , wordt de ID-provider met MSAL voor Java opgehaald op basis van de gebruikers naam. MSAL voor Java haalt een [SAML 1,1-token](reference-saml-tokens.md) token op uit de ID-provider, die vervolgens aan Azure AD wordt verstrekt en die de JSON Web token (JWT) retourneert.

## <a name="next-steps"></a>Volgende stappen

Zie voor de federatieve situatie [Azure Active Directory aanmeldings gedrag configureren voor een toepassing met behulp van een beleid voor het detecteren van een thuis domein](../manage-apps/configure-authentication-for-federated-users-portal.md)