---
title: Migreren naar micro soft Authentication Library (MSAL)
titleSuffix: Microsoft identity platform
description: Meer informatie over de verschillen tussen micro soft Authentication Library (MSAL) en Azure AD Authentication Library (ADAL) en hoe u migreert naar MSAL.
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
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164930"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Toepassingen migreren naar micro soft Authentication Library (MSAL)

Zowel micro soft Authentication Library (MSAL) als Azure AD Authentication Library (ADAL) worden gebruikt voor het verifiëren van Azure AD-entiteiten en het aanvragen van tokens van Azure AD. Tot nu toe hebben de meeste ontwikkel aars met Azure AD voor ontwikkel aars platform (v 1.0) gewerkt voor het verifiëren van Azure AD-identiteiten (werk-en school accounts) door tokens aan te vragen met behulp van Azure AD Authentication Library (ADAL). MSAL gebruiken:

- U kunt een bredere set met micro soft-identiteiten (Azure AD-identiteiten en micro soft-accounts en sociale en lokale accounts via Azure AD B2C) verifiëren, zoals het micro soft Identity platform-eind punt gebruikt.
- Uw gebruikers krijgen de beste ervaring voor eenmalige aanmelding.
- Uw toepassing kan stapsgewijze toestemming bieden en het ondersteunen van voorwaardelijke toegang is eenvoudiger.
- U profiteert van de innovatie.

**MSAL is nu de aanbevolen verificatie bibliotheek voor gebruik met het micro soft Identity-platform**. Er worden geen nieuwe functies geïmplementeerd op ADAL. De inspanningen zijn gericht op het verbeteren van MSAL.

In de volgende artikelen worden de verschillen tussen de MSAL-en ADAL-bibliotheken beschreven en kunt u migreren naar MSAL:
- [Migreren naar MSAL.NET](msal-net-migration.md)
- [Migreren naar MSAL. js](msal-compare-msal-js-and-adal-js.md)
- [Migreren naar MSAL. Android](migrate-android-adal-msal.md)
- [Migreren naar MSAL. iOS/macOS](migrate-objc-adal-msal.md)
- [Migreren naar MSAL python](migrate-python-adal-msal.md)
- [Migreren naar MSAL voor Java](migrate-adal-msal-java.md)
- [Xamarin-apps migreren met behulp van Brokers naar MSAL.NET](msal-net-migration-ios-broker.md)
