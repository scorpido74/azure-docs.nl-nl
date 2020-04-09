---
title: Microsoft Enterprise SSO-invoegtoepassing voor Apple-apparaten
titleSuffix: Microsoft identity platform | Azure
description: Meer informatie over de Azure Active Directory SSO-invoegtoepassing van Microsoft voor iOS- en macOS-apparaten.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: a6af1a78582ebf62aed82bad8fa06cac37f511ea
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982579"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Microsoft Enterprise SSO-plug-in voor Apple-apparaten (Preview)

> [!NOTE]
> Deze functie is beschikbaar voor openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

De *Microsoft Enterprise SSO-plug-in voor Apple-apparaten* biedt eenmalige aanmeldings (SSO) voor Azure Active Directory-accounts (Azure AD) voor alle toepassingen die de [Functie Onderneming enkele aanmelding](https://developer.apple.com/documentation/authenticationservices) van Apple ondersteunen. Microsoft werkte nauw samen met Apple om deze plug-in te ontwikkelen om de bruikbaarheid van uw toepassing te vergroten en tegelijkertijd de beste bescherming te bieden die Apple en Microsoft kunnen bieden.

In deze Public Preview-release is de Enterprise SSO-plug-in alleen beschikbaar voor iOS-apparaten en wordt deze gedistribueerd in bepaalde Microsoft-toepassingen.

Ons eerste gebruik van de Enterprise SSO-plug-in is met onze nieuwe functie [voor de modus voor gedeelde apparaten.](msal-ios-shared-devices.md)

## <a name="features"></a>Functies

De Microsoft Enterprise SSO-plug-in voor Apple-apparaten biedt de volgende voordelen:

- Biedt SSO voor Azure AD-accounts voor alle toepassingen die de Enterprise Single Sign-On-functie van Apple ondersteunen.
- Automatisch geleverd in de Microsoft Authenticator en kan worden ingeschakeld door elke MDM-oplossing (Mobile Device Management).

## <a name="requirements"></a>Vereisten

Ga als bedoeld als het gaat om microsoft Enterprise SSO-plug-in sto-invoegtoepassing voor Apple-apparaten:

- iOS 13.0 of hoger moet op het apparaat worden geïnstalleerd.
- Een Microsoft-toepassing die de Microsoft Enterprise SSO-plug-in voor Apple-apparaten biedt, moet op het apparaat worden geïnstalleerd. Voor Openbare preview bevatten deze toepassingen de [Microsoft Authenticator-app](../user-help/user-help-auth-app-overview.md).
- Apparaat moet mdm-ingeschreven zijn (bijvoorbeeld met Microsoft Intune).
- De configuratie moet naar het apparaat worden gepusht om de Microsoft Enterprise SSO-plug-in voor Apple-apparaten op het apparaat in te schakelen. Deze beveiligingsbeperking is vereist door Apple.

## <a name="enable-the-sso-extension-with-mobile-device-management-mdm"></a>De SSO-extensie inschakelen met mobiel apparaatbeheer (MDM)

Om de Microsoft Enterprise SSO-plug-in voor Apple-apparaten in te schakelen, moeten uw apparaten een signaal ontvangen via een MDM-service. Aangezien Microsoft de Enterprise SSO-plug-in in de [Microsoft Authenticator-app](..//user-help/user-help-auth-app-overview.md)opneemt, gebruikt u uw MDM om de app te configureren om de Microsoft Enterprise SSO-plug-in in te schakelen.

Gebruik de volgende parameters om de Microsoft Enterprise SSO-plug-in voor Apple-apparaten te configureren:

- **Type**: Omleiden
- **Uitbreidings-ID**:`com.microsoft.azureauthenticator.ssoextension`
- **Team-ID**:`SGGM6D27TK`
- **URL's**:
  - `https://login.microsoftonline.com`
  - `https://login.windows.net`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`

U Microsoft Intune gebruiken als MDM-service om de configuratie van de Microsoft Enterprise SSO-plug-in te vergemakkelijken. Zie de [configuratiedocumentatie van Intune](https://docs.microsoft.com/intune/configuration/ios-device-features-settings)voor meer informatie.

## <a name="using-the-sso-extension-in-your-application"></a>De SSO-extensie in uw toepassing gebruiken

De [Microsoft Authentication Library (MSAL) voor Apple-apparaten](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versie 1.1.0 en hoger ondersteunt de Microsoft Enterprise SSO-plug-in voor Apple-apparaten.

Als u de modus voor gedeelde apparaten wilt ondersteunen die wordt geleverd door de Microsoft Enterprise SSO-plug-in voor Apple-apparaten, moet u ervoor zorgen dat uw toepassingen de opgegeven minimaal vereiste versie van MSAL gebruiken.

## <a name="next-steps"></a>Volgende stappen

Zie [Gedeelde apparaatmodus voor iOS-apparaten voor](msal-ios-shared-devices.md)meer informatie over de modus gedeeld apparaat op iOS.
