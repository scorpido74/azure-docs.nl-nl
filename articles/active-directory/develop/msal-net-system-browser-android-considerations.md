---
title: Aandachtspunten voor Xamarin Android-systeem browser (micro soft Authentication Library voor .NET)
titleSuffix: Microsoft identity platform
description: Meer informatie over specifieke overwegingen bij het gebruik van systeem browsers op Xamarin Android wanneer u gebruikmaakt van de micro soft-verificatie bibliotheek voor .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f83283cc02c60eb452d0e414c0b21843b48f343b
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150360"
---
#  <a name="xamarin-android-system-browser-considerations-with-msalnet"></a>Xamarin Android-systeem browser overwegingen met MSAL.NET

In dit artikel worden specifieke overwegingen beschreven voor het gebruik van de systeem browser op Xamarin Android met de micro soft Authentication Library voor .NET (MSAL.NET).

Vanaf MSAL.NET 2.4.0-Preview ondersteunt MSAL.NET andere browsers dan Chrome en hoeft niet langer Chrome te worden geïnstalleerd op het Android-apparaat voor verificatie.

We raden u aan om browsers te gebruiken die aangepaste tabbladen ondersteunen, zoals deze:

| Browsers met aangepaste tabbladen ondersteunen | Pakket naam |
|------| ------- |
|Chrome | com. Android. Chrome|
|Microsoft Edge | com. micro soft. emmx|
|Firefox | org. mozilla. Firefox|
|Ecosia | com. ecosia. Android|
|Kiwi | com. kiwibrowser. browser|
|Brave | com. Brave. browser|

Naast browsers met aangepaste tabbladen ondersteuning, op basis van onze tests, kunnen enkele browsers die geen aangepaste tabbladen ondersteunen, ook worden gebruikt voor verificatie: Opera, Opera Mini-, inbrowser-en Maxthon. Lees voor meer informatie de [tabel voor test resultaten](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested).

## <a name="known-issues"></a>Bekende problemen

- Als de gebruiker geen browser heeft ingeschakeld op het apparaat, wordt door MSAL.NET een `AndroidActivityNotFound`-uitzonde ring gegenereerd. 
  - **Risico beperking**: Informeer de gebruiker of hij of zij een browser (bij voor keur een met aangepaste tabbladen-ondersteuning) op hun apparaat moet inschakelen.

- Als verificatie mislukt (bijvoorbeeld verificatie wordt gestart met DuckDuckGo), retourneert MSAL.NET een `AuthenticationCanceled MsalClientException`. 
  - **Hoofd probleem**: er is geen ondersteuning voor een browser met aangepaste tabbladen ingeschakeld op het apparaat. Verificatie is gestart met een alternatieve browser, waardoor de verificatie niet kan worden voltooid. 
  - **Risico beperking**: Informeer de gebruiker dat deze een browser (bij voor keur een met aangepaste tabblad ondersteuning) moet installeren op hun apparaat.

## <a name="devices-and-browsers-tested"></a>Apparaten en browsers getest
De volgende tabel geeft een lijst van de apparaten en browsers die zijn getest.

| | Browser&ast;     |  Resultaat  | 
| ------------- |:-------------:|:-----:|
| Huawei/één + | Chrome&ast; | Door|
| Huawei/één + | Edge-&ast; | Door|
| Huawei/één + | Firefox-&ast; | Door|
| Huawei/één + | Brave&ast; | Door|
| Eén + | Ecosia&ast; | Door|
| Eén + | Kiwi&ast; | Door|
| Huawei/één + | Opera | Door|
| Huawei | OperaMini | Door|
| Huawei/één + | Inbrowser | Door|
| Eén + | Maxthon | Door|
| Huawei/één + | DuckDuckGo | Door de gebruiker geannuleerde authenticatie|
| Huawei/één + | UC-browser | Door de gebruiker geannuleerde authenticatie|
| Eén + | Dolfijnen | Door de gebruiker geannuleerde authenticatie|
| Eén + | CM-browser | Door de gebruiker geannuleerde authenticatie|
| Huawei/één + | geen geïnstalleerd | AndroidActivityNotFound ex|

&ast; ondersteunt aangepaste tabbladen

## <a name="next-steps"></a>Volgende stappen
Lees deze [hand leiding](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid)voor code fragmenten en aanvullende informatie over het gebruik van System browser met Xamarin Android.  