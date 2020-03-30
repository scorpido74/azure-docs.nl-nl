---
title: Apps met één en meerdere tenant in Azure AD
titleSuffix: Microsoft identity platform
description: Meer informatie over de functies en verschillen tussen apps met één tenant en meerdere tenants in Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: 38cb1222a64b1759528749caa15dfb1bb906cef6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159910"
---
# <a name="tenancy-in-azure-active-directory"></a>Tenancy in Azure Active Directory

Azure Active Directory (Azure AD) organiseert objecten zoals gebruikers en apps in groepen die *tenants*worden genoemd. Met tenants kan een beheerder beleid instellen voor de gebruikers binnen de organisatie en de apps die de organisatie bezit om te voldoen aan hun beveiligings- en operationele beleid. 

## <a name="who-can-sign-in-to-your-app"></a>Wie kan zich aanmelden bij uw app?

Als het gaat om het ontwikkelen van apps, kunnen ontwikkelaars ervoor kiezen om hun app te configureren als single-tenant of multi-tenant tijdens app-registratie in de [Azure-portal.](https://portal.azure.com)
* Single-tenant apps zijn alleen beschikbaar in de huurder waarin ze zijn geregistreerd, ook wel bekend als hun woninghuurder.
* Multi-tenant apps zijn beschikbaar voor gebruikers in zowel hun woning huurder en andere huurders.

In de Azure-portal u uw app configureren als single-tenant of multi-tenant door het publiek als volgt in te stellen.

| Doelgroep | Single/multi-tenant | Wie kan zich aanmelden | 
|----------|--------| ---------|
| Alleen accounts in deze map | Eén tenant | Alle gebruikers- en gastaccounts in uw directory kunnen uw toepassing of API gebruiken.<br>*Gebruik deze optie als uw doelgroep intern is in uw organisatie.* |
| Accounts in een Azure AD-map | Multitenant | Alle gebruikers en gasten met een werk- of schoolaccount van Microsoft kunnen uw toepassing of API gebruiken. Dit geldt ook voor scholen en bedrijven die Office 365 gebruiken.<br>*Gebruik deze optie als uw doelgroep zakelijke of educatieve klanten zijn.* |
| Accounts in een Azure AD-map en persoonlijke Microsoft-accounts (zoals Skype, Xbox, Outlook.com) | Multitenant | Alle gebruikers met een werk- of school- of persoonlijk Microsoft-account kunnen uw toepassing of API gebruiken. Het omvat scholen en bedrijven die Office 365 gebruiken, evenals persoonlijke accounts die worden gebruikt om zich aan te melden bij services zoals Xbox en Skype.<br>*Gebruik deze optie om de breedste set Microsoft-accounts te targeten.* | 

## <a name="best-practices-for-multi-tenant-apps"></a>Aanbevolen procedures voor apps met meerdere huurders

Het bouwen van geweldige multi-tenant apps kan een uitdaging zijn vanwege het aantal verschillende beleidsregels dat IT-beheerders kunnen instellen in hun tenants. Als u ervoor kiest om een multi-tenant app te bouwen, volgt u de volgende aanbevolen procedures:

* Test uw app in een tenant die [beleid voor voorwaardelijke toegang](../azuread-dev/conditional-access-dev-guide.md)heeft geconfigureerd.
* Volg het principe van de minste gebruikerstoegang om ervoor te zorgen dat uw app alleen machtigingen opvraagt die het daadwerkelijk nodig heeft. Vermijd het aanvragen van machtigingen waarvoor toestemming van de beheerder vereist is, omdat gebruikers hierdoor in sommige organisaties uw app helemaal niet kunnen aanschaffen. 
* Geef de juiste namen en beschrijvingen op voor eventuele machtigingen die u als onderdeel van uw app blootstelt. Dit helpt gebruikers en beheerders te weten waar ze mee akkoord gaan wanneer ze de API's van uw app proberen te gebruiken. Zie de sectie Aanbevolen procedures in de [machtigingshandleiding](v2-permissions-and-consent.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Een app converteren naar multitenant](howto-convert-app-to-be-multi-tenant.md)
