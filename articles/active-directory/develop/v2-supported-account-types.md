---
title: Ondersteunde account typen-micro soft Identity platform | Azure
description: Conceptuele documentatie over doel groepen en ondersteunde account typen in toepassingen
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: de90c81f56b6017b2d53ecbfb2c400a4c9f05d81
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78365241"
---
# <a name="supported-account-types"></a>Ondersteunde accounttypen

In dit artikel wordt uitgelegd welke typen accounts (soms benoemde doel groepen) worden ondersteund in toepassingen.

<!-- This section can be in an include for many of the scenarios (SPA, Web App signing-in users, protecting a Web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Ondersteunde account typen in micro soft Identity platform-toepassingen

In de Microsoft Azure open bare Cloud kunnen de meeste typen apps zich aanmelden met een wille keurige doel groep:

- Als u een LOB-toepassing (line-of-Business) schrijft, kunt u zich aanmelden bij gebruikers in uw eigen organisatie. Een dergelijke toepassing heeft soms de naam **enkele Tenant**.
- Als u een ISV bent, kunt u een toepassing schrijven die gebruikers aanmeldt:

  - In elke organisatie. Een dergelijke toepassing heet een webtoepassing **met meerdere tenants** . Soms leest u dat de gebruiker zich aanmeldt met hun werk-of school account.
  - Met hun werk-of school-of persoonlijke Microsoft-account.
  - Met alleen persoonlijke Microsoft-account.
    > [!NOTE]
    > Momenteel ondersteunt het micro soft-identiteits platform persoonlijke micro soft-accounts alleen door het registreren van een app voor **werk-of school-of micro soft-accounts**, en vervolgens het aanmelden in de code voor de toepassing beperken door een Azure AD-instantie op te geven bij het bouwen van de toepassing, zoals `https://login.microsoftonline.com/consumers`.

- Als u een bedrijf naar consumenten schrijft, kunt u gebruikers ook aanmelden met hun sociale identiteit, met behulp van Azure AD B2C.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>Bepaalde verificatie stromen ondersteunen niet alle account typen

Sommige account typen kunnen niet worden gebruikt met bepaalde verificatie stromen. Bijvoorbeeld in Desktop-, UWP-toepassingen of daemon-toepassingen:

- Daemon-toepassingen kunnen alleen worden gebruikt met Azure Active Directory organisaties. Het is niet zinvol om daemon-toepassingen te gebruiken voor het bewerken van persoonlijke micro soft-accounts (de beheerders toestemming wordt nooit verleend).  
- U kunt de geïntegreerde Windows-verificatie stroom alleen gebruiken met werk-of school accounts (in uw organisatie of een organisatie). Geïntegreerde Windows-verificatie werkt inderdaad met domein accounts en vereist dat de computers lid zijn van een domein of lid zijn van Azure AD. Deze stroom is niet zinvol voor persoonlijke micro soft-accounts.
- Het [wacht woord](./v2-oauth-ropc.md) voor de resource-eigenaar kan niet worden gebruikt met persoonlijke micro soft-accounts. Persoonlijke micro soft-accounts vereisen inderdaad dat de gebruiker toegang heeft tot persoonlijke bronnen bij elke aanmeldings sessie. Daarom is dit gedrag niet compatibel met niet-interactieve stromen.
- De code stroom van het apparaat werkt nog niet met persoonlijke micro soft-accounts.

## <a name="supported-account-types-in-national-clouds"></a>Ondersteunde account typen in nationale Clouds

 Apps kunnen zich ook aanmelden bij gebruikers in [nationale Clouds](authentication-national-cloud.md). Persoonlijke micro soft-accounts worden echter niet ondersteund in deze Clouds (per definitie van deze Clouds). Daarom zijn de ondersteunde account typen voor deze Clouds, naar uw organisatie (één Tenant) of voor alle organisaties (toepassingen met meerdere tenants) gereduceerd.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [pacht in azure Active Directory](./single-and-multi-tenant-apps.md)
- Meer informatie over [nationale Clouds](./authentication-national-cloud.md)
