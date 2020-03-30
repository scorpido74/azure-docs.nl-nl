---
title: MSAL voor iOS-& macOS-verschillen | Azure
titleSuffix: Microsoft identity platform
description: Beschrijft msal-gebruiksverschillen (Microsoft Authentication Library) tussen iOS en macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 62b79ee7398286b8e6c8ed8612bd001595e1f6ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084977"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>Verschillen tussen de Microsoft-verificatiebibliotheek voor iOS en macOS

In dit artikel worden de verschillen in functionaliteit tussen de Microsoft Authentication Library (MSAL) voor iOS en macOS uitgelegd.

> [!NOTE]
> Op de Mac ondersteunt MSAL alleen macOS-apps.

## <a name="general-differences"></a>Algemene verschillen

MSAL voor macOS is een subset van de functionaliteit die beschikbaar is voor iOS.

MSAL voor macOS biedt geen ondersteuning voor:

- verschillende browsertypen `ASWebAuthenticationSession`zoals `SFAuthenticationSession` `SFSafariViewController`, , .
- gebrokereerde verificatie via de Microsoft Authenticator-app wordt niet ondersteund voor macOS.

Het delen van sleutelhangers tussen apps van dezelfde uitgever is beperkter op macOS 10.14 en eerder. Gebruik [toegangscontrolelijsten](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc) om de paden naar de apps op te geven die de sleutelhanger moeten delen. De gebruiker kan aanvullende sleutelhangeraanwijzingen zien.

Op macOS 10.15+ is het gedrag van MSAL hetzelfde tussen iOS en macOS. MSAL maakt gebruik [van sleutelhangertoegangsgroepen](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) voor het delen van sleutelhangers. 

### <a name="conditional-access-authentication-differences"></a>Verschillen in verificatie voor voorwaardelijke toegang

Voor scenario's voor voorwaardelijke toegang zijn er minder gebruikersprompts wanneer u MSAL voor iOS gebruikt. Dit komt omdat iOS de broker-app (Microsoft Authenticator) gebruikt, die de noodzaak ontkent om de gebruiker in sommige gevallen te vragen.

### <a name="project-setup-differences"></a>Verschillen in projectinstellingen

**Macos**

- Wanneer u uw project instelt op macOS, moet u ervoor zorgen dat uw toepassing is ondertekend met een geldig ontwikkelings- of productiecertificaat. MSAL werkt nog steeds in de niet-ondertekende modus, maar het zal zich anders gedragen met betrekking tot cache persistentie. De app mag alleen niet-ondertekend worden uitgevoerd voor foutopsporingsdoeleinden. Als u de app niet-ondertekend distribueert, wordt het als volgt weergegeven:
1. Op 10.14 en eerder zal MSAL de gebruiker vragen om een sleutelhangerwachtwoord elke keer dat hij de app opnieuw opstart.
2. Op 10.15+ zal MSAL de gebruiker om referenties vragen voor elke token-acquisitie. 

- macOS-apps hoeven het appDelegate-gesprek niet te implementeren.

**iOS**

- Er zijn extra stappen om uw project in te stellen om de stroom van verificatiebroker's te ondersteunen. De stappen worden opgeroepen in de tutorial.
- iOS-projecten moeten aangepaste schema's registreren in de info.plist. Dit is niet vereist op macOS.
