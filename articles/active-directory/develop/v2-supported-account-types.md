---
title: Ondersteunde accountstypen - Microsoft-identiteitsplatform | Azure
description: Conceptuele documentatie over doelgroepen en ondersteunde accounttypen in toepassingen
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: b3b0114bb5d545755fe59c49605d6def341d2275
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535771"
---
# <a name="supported-account-types"></a>Ondersteunde accounttypen

In dit artikel wordt uitgelegd welke accountstypen (soms doelgroepen genoemd) worden ondersteund in toepassingen.

<!-- This section can be in an include for many of the scenarios (SPA, web app signing-in users, protecting a web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Ondersteunde accountstypen in Microsoft Identity-platformtoepassingen

In de openbare Microsoft Azure-cloud kunnen de meeste typen apps zich aanmelden bij gebruikers met elk publiek:

- Als u een LOB-toepassing (Line of Business) schrijft, u zich aanmelden bij gebruikers in uw eigen organisatie. Een dergelijke toepassing wordt soms **één tenant**genoemd.
- Als u een ISV bent, u een toepassing schrijven die gebruikers aanmeldt:

  - In elke organisatie. Een dergelijke toepassing wordt een **multi-tenant** webapplicatie genoemd. Soms lees je dat het gebruikers aanmeldt met hun werk- of schoolaccounts.
  - Met hun werk of school of persoonlijk Microsoft-account.
  - Met alleen een persoonlijk Microsoft-account.
    > [!NOTE]
    > Momenteel ondersteunt het Microsoft-identiteitsplatform persoonlijke Microsoft-accounts alleen door een app voor persoonlijke accounts voor **werk of school of Microsoft te**registreren en vervolgens `https://login.microsoftonline.com/consumers`het aanmelden in de code voor de toepassing te beperken door een Azure AD-autoriteit op te geven bij het bouwen van de toepassing, zoals .

- Als u een toepassing voor een bedrijf schrijft aan consumenten, u zich ook aanmelden bij gebruikers met hun sociale identiteit met Azure AD B2C.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>Bepaalde verificatiestromen ondersteunen niet alle accounttypen

Sommige accounttypen kunnen niet worden gebruikt met bepaalde verificatiestromen. Bijvoorbeeld in desktop-, UWP-toepassingen of daemon-toepassingen:

- Daemon-toepassingen kunnen alleen worden gebruikt met Azure Active Directory-organisaties. Het heeft geen zin om te proberen daemon-toepassingen te gebruiken om persoonlijke accounts van Microsoft te manipuleren (de toestemming van de beheerder wordt nooit verleend).
- U de geïntegreerde Windows-verificatiestroom alleen gebruiken met werk- of schoolaccounts (in uw organisatie of een organisatie). Geïntegreerde Windows-verificatie werkt namelijk met domeinaccounts en vereist dat de machines worden samengevoegd of Azure AD is samengevoegd. Deze stroom heeft geen zin voor persoonlijke Microsoft-accounts.
- De [Resource Owner Password Grant](./v2-oauth-ropc.md) (Gebruikersnaam/Wachtwoord) kan niet worden gebruikt met persoonlijke Microsoft-accounts. Persoonlijke Microsoft-accounts vereisen namelijk dat de gebruiker toestemming geeft voor toegang tot persoonlijke bronnen bij elke aanmeldingssessie. Daarom is dit gedrag niet compatibel met niet-interactieve stromen.
- Apparaatcodestroom werkt nog niet met persoonlijke Microsoft-accounts.

## <a name="supported-account-types-in-national-clouds"></a>Ondersteunde accounttypen in nationale clouds

 Apps kunnen zich ook aanmelden voor gebruikers in [nationale clouds.](authentication-national-cloud.md) Persoonlijke microsoft-accounts worden echter niet ondersteund in deze clouds (per definitie van deze clouds). Daarom worden de ondersteunde accounttypen voor deze clouds gereduceerd tot uw organisatie (single tenant) of organisaties (multi-tenant toepassingen).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Tenancy in Azure Active Directory](./single-and-multi-tenant-apps.md)
- Meer informatie over [Nationale Clouds](./authentication-national-cloud.md)
