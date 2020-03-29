---
title: Migreren naar Microsoft-verificatiebibliotheek (MSAL)
titleSuffix: Microsoft identity platform
description: Meer informatie over de verschillen tussen Microsoft Authentication Library (MSAL) en Azure AD Authentication Library (ADAL) en hoe u migreren naar MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/27/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3af18eb09fd9906a0caaebda0b786795400467f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164930"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Toepassingen migreren naar Microsoft-verificatiebibliotheek (MSAL)

Zowel Microsoft Authentication Library (MSAL) als Azure AD Authentication Library (ADAL) worden gebruikt om Azure AD-entiteiten te verifiëren en tokens van Azure AD aan te vragen. Tot nu toe hebben de meeste ontwikkelaars samengewerkt met Azure AD voor ontwikkelaarsplatform (v1.0) om Azure AD-identiteiten (werk- en schoolaccounts) te verifiëren door tokens aan te vragen met Azure AD-verificatiebibliotheek (ADAL). Met behulp van MSAL:

- U een bredere set Microsoft-identiteiten (Azure AD-identiteiten en Microsoft-accounts en sociale en lokale accounts verifiëren via Azure AD B2C) terwijl het het eindpunt van het Microsoft-identiteitsplatform wordt gebruikt.
- Uw gebruikers krijgen de beste single-sign-on ervaring.
- Uw toepassing kan incrementele toestemming inschakelen en het ondersteunen van voorwaardelijke toegang is eenvoudiger.
- U profiteert van de innovatie.

**MSAL is nu de aanbevolen auth-bibliotheek om te gebruiken met het Microsoft-identiteitsplatform.** Er worden geen nieuwe functies geïmplementeerd op ADAL. De inspanningen zijn gericht op het verbeteren van MSAL.

In de volgende artikelen worden de verschillen tussen de MSAL- en ADAL-bibliotheken beschreven en u migreren naar MSAL:
- [Migreren naar MSAL.NET](msal-net-migration.md)
- [Migreren naar MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [Migreren naar MSAL.Android](migrate-android-adal-msal.md)
- [Migreren naar MSAL.iOS / macOS](migrate-objc-adal-msal.md)
- [Migreren naar MSAL Python](migrate-python-adal-msal.md)
- [Migreren naar MSAL voor Java](migrate-adal-msal-java.md)
- [Xamarin-apps migreren met behulp van brokers naar MSAL.NET](msal-net-migration-ios-broker.md)
