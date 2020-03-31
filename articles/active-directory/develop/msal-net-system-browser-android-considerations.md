---
title: Xamarin Android-systeem browser overwegingen (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over overwegingen voor het gebruik van systeembrowsers op Xamarin Android met Microsoft Authentication Library voor .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad26a4d619a7984f08a8decc87f9339adae47cdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132614"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>Xamarin Android-systeem browser overwegingen voor het gebruik van MSAL.NET

In dit artikel wordt beschreven waar u rekening mee moet houden wanneer u de systeembrowser op Xamarin Android met Microsoft Authentication Library voor .NET (MSAL.NET) gebruikt.

Vanaf MSAL.NET 2.4.0 Preview ondersteunt MSAL.NET andere browsers dan Chrome. Het vereist niet langer Chrome worden geïnstalleerd op het Android-apparaat voor authenticatie.

We raden u aan browsers te gebruiken die aangepaste tabbladen ondersteunen. Hier zijn enkele voorbeelden van deze browsers:

| Ondersteuning voor browsers met aangepaste tabbladen | Naam van het pakket |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia Ecosia | com.ecosia.android|
|Kiwi | com.kiwibrowser.browser|
|Dappere | com.brave.browser|

Naast het identificeren van browsers die ondersteuning bieden voor aangepaste tabbladen, geven onze tests aan dat een paar browsers die geen aangepaste tabbladen ondersteunen, ook werken voor verificatie. Deze browsers omvatten Opera, Opera Mini, InBrowser en Maxthon. 

## <a name="tested-devices-and-browsers"></a>Geteste apparaten en browsers
In de volgende tabel worden de apparaten en browsers weergegeven die zijn getest op verificatiecompatibiliteit.

| Apparaat | Browser     |  Resultaat  | 
| ------------- |:-------------:|:-----:|
| Huawei/One+ | Chrome\* | Pass|
| Huawei/One+ | Edge\* | Pass|
| Huawei/One+ | Firefox\* | Pass|
| Huawei/One+ | Dappere\* | Pass|
| Een+ | Ecosia Ecosia\* | Pass|
| Een+ | Kiwi\* | Pass|
| Huawei/One+ | Opera | Pass|
| Huawei | OperaMini (OperaMini) | Pass|
| Huawei/One+ | InBrowser | Pass|
| Een+ | Maxthon | Pass|
| Huawei/One+ | DuckDuckGo DuckDuckGo | Door de gebruiker geannuleerde verificatie|
| Huawei/One+ | UC-browser | Door de gebruiker geannuleerde verificatie|
| Een+ | Dolphin | Door de gebruiker geannuleerde verificatie|
| Een+ | CM-browser | Door de gebruiker geannuleerde verificatie|
| Huawei/One+ | None | Uitzondering op AndroidActivityNotFound|

\*Ondersteunt aangepaste tabbladen

## <a name="known-issues"></a>Bekende problemen

Als de gebruiker geen browser heeft ingeschakeld op `AndroidActivityNotFound` het apparaat, MSAL.NET een uitzondering.  
  - **Mitigatie:** Vraag de gebruiker om een browser op zijn apparaat in te schakelen. Beveel een browser aan die aangepaste tabbladen ondersteunt.

Als verificatie mislukt (bijvoorbeeld als verificatie wordt gestart met `AuthenticationCanceled MsalClientException`DuckDuckGo), wordt MSAL.NET teruggegeven. 
  - **Hoofdprobleem:** een browser die aangepaste tabbladen ondersteunt, is niet ingeschakeld op het apparaat. Verificatie gestart met een browser die de verificatie niet kon voltooien. 
  - **Mitigatie:** Vraag de gebruiker om een browser op zijn apparaat in te schakelen. Beveel een browser aan die aangepaste tabbladen ondersteunt.

## <a name="next-steps"></a>Volgende stappen
Zie [Kiezen tussen een ingesloten webbrowser en een systeembrowser op Xamarin Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) en [Embedded versus system web UI](msal-net-web-browsers.md#embedded-vs-system-web-ui)voor meer informatie en codevoorbeelden.  