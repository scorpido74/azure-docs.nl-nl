---
title: Microsoft Enterprise SSO-invoegtoepassing voor Apple-apparaten
titleSuffix: Microsoft identity platform | Azure
description: Meer informatie over de Azure Active Directory SSO-invoeg toepassing van micro soft voor iOS-en macOS-apparaten.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80982579"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten (preview-versie)

> [!NOTE]
> Deze functie is beschikbaar voor openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

De *micro soft Enter PRISE SSO-invoeg toepassing voor Apple-apparaten* biedt eenmalige aanmelding (SSO) voor Azure Active Directory (Azure AD)-accounts voor alle toepassingen die ondersteuning bieden voor de functie voor [eenmalige aanmelding voor ondernemingen](https://developer.apple.com/documentation/authenticationservices) van Apple. Micro soft werkte nauw samen met Apple voor het ontwikkelen van deze invoeg toepassing om de bruikbaarheid van uw toepassingen te verhogen en de beste beveiliging te bieden die door Apple en micro soft kan worden geboden.

In deze open bare preview-versie is de Enter prise SSO-invoeg toepassing alleen beschikbaar voor iOS-apparaten en wordt deze in bepaalde micro soft-toepassingen gedistribueerd.

Het eerste gebruik van de Enter prise SSO-invoeg toepassing is met onze nieuwe functie voor [gedeelde apparaten](msal-ios-shared-devices.md) .

## <a name="features"></a>Functies

De micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten biedt de volgende voor delen:

- Voorziet in SSO voor Azure AD-accounts voor alle toepassingen die ondersteuning bieden voor de functie voor eenmalige aanmelding van Apple.
- Wordt automatisch geleverd in de Microsoft Authenticator en kan worden ingeschakeld door elke Mobile Device Management (MDM)-oplossing.

## <a name="requirements"></a>Vereisten

Micro soft Enter prise SSO-invoeg toepassing gebruiken voor Apple-apparaten:

- iOS 13,0 of hoger moet op het apparaat zijn geïnstalleerd.
- Een micro soft-toepassing die de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten levert, moet op het apparaat zijn geïnstalleerd. Voor een open bare preview omvatten deze toepassingen de [app Microsoft Authenticator](../user-help/user-help-auth-app-overview.md).
- Apparaat moet zijn Inge schreven bij MDM (bijvoorbeeld met Microsoft Intune).
- De configuratie moet worden gepusht naar het apparaat om de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten op het apparaat in te scha kelen. Deze beveiligings beperking wordt vereist door Apple.

## <a name="enable-the-sso-extension-with-mobile-device-management-mdm"></a>De SSO-extensie inschakelen met Mobile Device Management (MDM)

Als u de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten wilt inschakelen, moeten uw apparaten een signaal verzenden via een MDM-service. Omdat micro soft de SSO-invoeg toepassing voor bedrijven in de [Microsoft Authenticator-app](..//user-help/user-help-auth-app-overview.md)bevat, gebruikt u uw MDM om de app te configureren om de micro soft Enter prise SSO-invoeg toepassing in te scha kelen.

Gebruik de volgende para meters voor het configureren van de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten:

- **Type**: omleiden
- **Extensie-id**:`com.microsoft.azureauthenticator.ssoextension`
- **Team-ID**:`SGGM6D27TK`
- **Url's**:
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

U kunt Microsoft Intune als uw MDM-service gebruiken om de configuratie van de micro soft Enter prise SSO-invoeg toepassing te vereenvoudigen. Zie de [intune-configuratie documentatie](https://docs.microsoft.com/intune/configuration/ios-device-features-settings)voor meer informatie.

## <a name="using-the-sso-extension-in-your-application"></a>De SSO-extensie in uw toepassing gebruiken

De [micro soft Authentication Library (MSAL) voor Apple-apparaten](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versie 1.1.0 en hoger ondersteunt de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten.

Als u de modus voor gedeeld apparaat wilt ondersteunen die wordt geboden door de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten, moet u ervoor zorgen dat uw toepassingen gebruikmaken van de opgegeven mini maal vereiste versie van MSAL.

## <a name="next-steps"></a>Volgende stappen

Zie [modus voor gedeeld apparaat voor IOS-apparaten](msal-ios-shared-devices.md)voor meer informatie over de modus gedeeld apparaat op Ios.
