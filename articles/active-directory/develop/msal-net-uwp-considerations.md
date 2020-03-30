---
title: OVERWEGINGEn van uwp (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over overwegingen voor het gebruik van Universal Windows Platform (UWP) met Microsoft Authentication Library voor .NET (MSAL.NET).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eeec28569cf31af4542d6cd7aca1fb27d77b1e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132531"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>Overwegingen voor het gebruik van Universal Windows Platform met MSAL.NET
Ontwikkelaars van toepassingen die Universal Windows Platform (UWP) gebruiken met MSAL.NET moeten rekening houden met de concepten die dit artikel presenteert.

## <a name="the-usecorporatenetwork-property"></a>De eigenschap UseCorporateNetwork
Op het Windows Runtime (WinRT) `PublicClientApplication` platform, `UseCorporateNetwork`heeft de Booleaanse eigenschap . Met deze eigenschap kunnen Windows 8.1-toepassingen en UWP-toepassingen profiteren van Geïntegreerde Windows-verificatie (IWA) als de gebruiker is aangemeld bij een account met een azure AD-tenant (Federated Azure Active Directory). Gebruikers die zijn aangemeld bij het besturingssysteem kunnen ook gebruik maken van single sign-on (SSO). Wanneer u `UseCorporateNetwork` de eigenschap instelt, gebruikt MSAL.NET een webverificatiebroker (WAB).

> [!IMPORTANT]
> Als `UseCorporateNetwork` u de eigenschap op true instelt, wordt ervan uitgegaan dat de toepassingsontwikkelaar IWA in de toepassing heeft ingeschakeld. Om IWA in te schakelen:
> - Schakel in uw UWP-toepassing `Package.appxmanifest`op het tabblad **Mogelijkheden** de volgende mogelijkheden in:
>   - **Bedrijfsverificatie**
>   - **Privénetwerken (client & server)**
>   - **Gebruikerscertificaat voor gedeelde gebruikers**

IWA is standaard niet ingeschakeld omdat microsoft store een hoog verificatieniveau vereist voordat het toepassingen accepteert die de mogelijkheden van bedrijfsverificatie of gedeelde gebruikerscertificaten aanvragen. Niet alle ontwikkelaars willen dit niveau van verificatie doen.

Op het UWP-platform werkt de onderliggende WAB-implementatie niet goed in bedrijfsscenario's waarin voorwaardelijke toegang is ingeschakeld. Gebruikers zien symptomen van dit probleem wanneer ze proberen in te loggen met Windows Hello. Wanneer de gebruiker wordt gevraagd een certificaat te kiezen:

- Het certificaat voor de pincode wordt niet gevonden.
- Nadat de gebruiker een certificaat heeft gekozen, wordt deze niet gevraagd om de pincode.

U proberen om dit probleem te voorkomen met behulp van een alternatieve methode, zoals gebruikersnaam-wachtwoord en telefoon authenticatie, maar de ervaring is niet goed.

## <a name="troubleshooting"></a>Problemen oplossen

Sommige klanten hebben melding gemaakt van de volgende aanmeldingsfout in specifieke bedrijfsomgevingen waarin ze weten dat ze een internetverbinding hebben en dat de verbinding werkt met een openbaar netwerk.

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

U dit probleem voorkomen door ervoor te zorgen dat WAB (de onderliggende Windows-component) een privénetwerk toestaat. U dat doen door een registersleutel in te stellen:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Zie [Webverificatiebroker - Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
De volgende monsters geven meer informatie.

Voorbeeld | Platform | Beschrijving 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Een UWP-clienttoepassing die MSAL.NET gebruikt. Het heeft toegang tot Microsoft Graph voor een gebruiker die zich verifieert met behulp van een Azure AD 2.0-eindpunt. <br>![Topologie](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Een eenvoudige App Xamarin Forms die laat zien hoe u MSAL gebruikt om persoonlijke accounts van Microsoft en Azure AD te verifiëren via het Azure AD 2.0-eindpunt. Het toont ook hoe u toegang krijgt tot Microsoft Graph en toont het resulterende token. <br>![Topologie](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
