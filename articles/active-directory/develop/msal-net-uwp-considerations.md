---
title: UWP overwegingen (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over overwegingen voor het gebruik van Universeel Windows-platform (UWP) met micro soft Authentication Library voor .NET (MSAL.NET).
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
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132531"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>Overwegingen voor het gebruik van Universeel Windows-platform met MSAL.NET
Ontwikkel aars van toepassingen die gebruikmaken van Universeel Windows-platform (UWP) met MSAL.NET, moeten rekening houden met de concepten in dit artikel.

## <a name="the-usecorporatenetwork-property"></a>De eigenschap UseCorporateNetwork
Op het Windows Runtime (WinRT)-platform heeft `PublicClientApplication` de Booleaanse eigenschap `UseCorporateNetwork`. Met deze eigenschap kunnen Windows 8,1-toepassingen en UWP-toepassingen profiteren van geïntegreerde Windows-authenticatie (IWA) als de gebruiker is aangemeld bij een account met een federatieve Azure Active Directory-Tenant (Azure AD). Gebruikers die zijn aangemeld bij het besturings systeem, kunnen ook gebruikmaken van eenmalige aanmelding (SSO). Wanneer u de eigenschap `UseCorporateNetwork` instelt, maakt MSAL.NET gebruik van een Web authentication Broker (WAB).

> [!IMPORTANT]
> Als u de eigenschap `UseCorporateNetwork` instelt op True, wordt ervan uitgegaan dat de ontwikkelaar van de toepassing IWA in de toepassing heeft ingeschakeld. IWA inschakelen:
> - In de `Package.appxmanifest`van uw UWP-toepassing schakelt u op het tabblad **mogelijkheden** de volgende mogelijkheden in:
>   - **Ondernemings verificatie**
>   - **Particuliere netwerken (client & Server)**
>   - **Gedeeld gebruikers certificaat**

IWA is niet standaard ingeschakeld omdat Microsoft Store een hoog verificatie niveau vereist voordat het toepassingen accepteert die de mogelijkheden van ondernemings verificatie of gedeelde gebruikers certificaten aanvragen. Niet alle ontwikkel aars willen dit verificatie niveau.

Op het UWP-platform werkt de onderliggende WAB-implementatie niet goed in bedrijfs scenario's waarin voorwaardelijke toegang is ingeschakeld. Gebruikers zien symptomen van dit probleem wanneer ze zich proberen aan te melden met behulp van Windows hello. Wanneer de gebruiker wordt gevraagd een certificaat te kiezen:

- Het certificaat voor de pincode is niet gevonden.
- Nadat de gebruiker een certificaat heeft gekozen, wordt niet gevraagd om de pincode.

U kunt proberen dit probleem te vermijden door gebruik te maken van een alternatieve methode, zoals gebruikers naam en telefoon verificatie, maar de ervaring is niet goed.

## <a name="troubleshooting"></a>Problemen oplossen

Sommige klanten hebben de volgende aanmeldings fout gerapporteerd in specifieke bedrijfs omgevingen waarin ze weten dat ze een Internet verbinding hebben en dat de verbinding met een openbaar netwerk werkt.

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

U kunt dit probleem voor komen door ervoor te zorgen dat WAB (het onderliggende Windows-onderdeel) een particulier netwerk toestaat. U kunt dit doen door een register sleutel in te stellen:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Zie voor meer informatie [Web authentication Broker-Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Volgende stappen
In de volgende voor beelden vindt u meer informatie.

Voorbeeld | Platform | Beschrijving 
|------ | -------- | -----------|
|[Active-Directory-DotNet-native-UWP-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Een UWP-client toepassing die gebruikmaakt van MSAL.NET. Er wordt gebruikgemaakt van Microsoft Graph voor een gebruiker die zich verifieert met behulp van een Azure AD 2,0-eind punt. <br>![Topologie](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[Active-Directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Een eenvoudige Xamarin Forms-app die laat zien hoe u MSAL kunt gebruiken om micro soft-persoonlijke accounts en Azure AD te verifiëren via het Azure AD 2,0-eind punt. Ook wordt uitgelegd hoe u toegang krijgt tot Microsoft Graph en hoe het resulterende token wordt weer gegeven. <br>![Topologie](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
