---
title: UWP overwegingen (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over specifieke overwegingen bij het gebruik van Universeel Windows-platform met de micro soft Authentication Library voor .NET (MSAL.NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 549e36099e06d665ecab879ceb3c38987d000852
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695089"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>Universeel Windows-platform-specifieke overwegingen met MSAL.NET
In UWP moet u rekening houden met verschillende overwegingen bij het gebruik van MSAL.NET.

## <a name="the-usecorporatenetwork-property"></a>De eigenschap UseCorporateNetwork
In het WinRT-platform heeft `PublicClientApplication` de volgende Booleaanse eigenschap ``UseCorporateNetwork``. Met deze eigenschap kunnen win 8.1-en UWP-toepassingen profiteren van geïntegreerde Windows-authenticatie (en dus SSO waarbij de gebruiker zich aanmeldt met het besturings systeem) als de gebruiker is aangemeld met een account in een federatieve Azure AD-Tenant. Wanneer u deze eigenschap instelt, maakt MSAL.NET gebruik van WAB (Web authentication Broker).

> [!IMPORTANT]
> Als u deze eigenschap instelt op True, wordt ervan uitgegaan dat de ontwikkelaar van de toepassing geïntegreerde Windows-authenticatie (IWA) in de toepassing heeft ingeschakeld. Voor dit:
> - In de ``Package.appxmanifest`` voor uw UWP-toepassing schakelt u op het tabblad **mogelijkheden** de volgende mogelijkheden in:
>   - Enterprise-verificatie
>   - Particuliere netwerken (client & Server)
>   - Gedeeld gebruikers certificaat

IWA is niet standaard ingeschakeld, omdat toepassingen die de mogelijkheden voor ondernemings verificatie of gedeelde gebruikers certificaten aanvragen, een hoger verificatie niveau moeten hebben om te worden geaccepteerd in de Windows Store, en niet alle ontwikkel aars willen het hoger doen verificatie niveau.

De onderliggende implementatie op het UWP-platform (WAB) werkt niet correct in bedrijfs scenario's waarin voorwaardelijke toegang is ingeschakeld. Het symptoom is dat de gebruiker zich probeert aan te melden bij Windows hello en wordt voorgesteld om een certificaat te kiezen, maar:

- het certificaat voor de pincode is niet gevonden,
- of de gebruiker kiest deze, maar er wordt nooit om de pincode gevraagd.

Een tijdelijke oplossing is het gebruik van een alternatieve methode (gebruikers naam/wacht woord + telefoon verificatie), maar de ervaring is niet goed.

## <a name="troubleshooting"></a>Problemen oplossen

Sommige klanten hebben gerapporteerd dat in sommige specifieke bedrijfs omgevingen de volgende aanmeldings fout is opgetreden:

```Text
We can't connect to the service you need right now. Check your network connection or try this again later
```

dat ze weten dat ze een Internet verbinding hebben en dat werkt met een openbaar netwerk.

Een tijdelijke oplossing is om ervoor te zorgen dat WAB (het onderliggende Windows-onderdeel) een particulier netwerk toestaat. U kunt dit doen door een register sleutel in te stellen:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Zie voor meer informatie [Web authentication Broker-Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Volgende stappen
Meer informatie vindt u in de volgende voor beelden:

Voorbeeld | Platform | Beschrijving 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Een Universeel Windows-platform-client toepassing met behulp van msal.net, die toegang heeft tot de Microsoft Graph voor een gebruiker die is geverifieerd met het Azure AD v 2.0-eind punt. <br>![Topologie](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Een eenvoudige Xamarin Forms-app die laat zien hoe u MSAL kunt gebruiken om MSA en Azure AD te verifiëren via het AAD v 2.0-eind punt en toegang te krijgen tot de Microsoft Graph met het resulterende token. <br>![Topologie](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
