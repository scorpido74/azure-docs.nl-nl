---
title: Gebruikers inrichten voor Azure AD Gallery-app neemt uren of meer in beslag
description: Nagaan waarom het inrichten van uw toepassing langer duurt dan verwacht
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: f0f572e5153b6e79bf7731d0d57b66e601a0054c
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/15/2020
ms.locfileid: "84781970"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Gebruikers die een Azure AD-galerie toepassing inrichten, nemen uren of meer in beslag

Wanneer u voor het eerst automatische inrichting voor een toepassing inschakelt, kan de eerste cyclus van 20 minuten tot enkele uren duren, afhankelijk van de grootte van de Azure AD-adres lijst en het aantal gebruikers in het bereik dat moet worden ingericht. 

Volgende synchronisaties na de eerste cyclus worden sneller uitgevoerd, omdat de inrichtings service de water merken opslaat die de status van beide systemen na de eerste cyclus vertegenwoordigen, waardoor de prestaties van volgende synchronisaties worden verbeterd.

## <a name="how-to-improve-provisioning-performance"></a>De inrichtings prestaties verbeteren

Als de eerste cyclus meer dan een paar uur duurt, kunt u een van de volgende dingen doen om de prestaties te verbeteren:

-   **Filters voor gebruikers bereik.** Met behulp van bereik filters kunt u de gegevens die door de inrichtings service worden geëxtraheerd, afstemmen op de gebruikers op basis van specifieke kenmerk waarden. Zie voor meer informatie over het bereik van filters [kenmerk toepassing inrichten met bereik filters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Volgende stappen
[Automate User Provisioning and Deprovisioning to SaaS Applications with Azure Active Directory](user-provisioning.md) (Automatisch gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory)

