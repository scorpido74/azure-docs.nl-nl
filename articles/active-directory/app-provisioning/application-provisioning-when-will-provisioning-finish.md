---
title: Gebruikers inrichten voor Azure AD Gallery-app neemt uren of meer in beslag
description: Nagaan waarom het inrichten van uw toepassing langer duurt dan verwacht
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.openlocfilehash: d663962dab058a108faa7d5310a3f2892c345a75
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593893"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Gebruikers die een Azure AD-galerie toepassing inrichten, nemen uren of meer in beslag

Wanneer u voor het eerst automatische inrichting voor een toepassing inschakelt, kan de eerste cyclus van 20 minuten tot enkele uren duren, afhankelijk van de grootte van de Azure AD-adres lijst en het aantal gebruikers in het bereik dat moet worden ingericht. 

Volgende synchronisaties na de eerste cyclus worden sneller uitgevoerd, omdat de inrichtings service de water merken opslaat die de status van beide systemen na de eerste cyclus vertegenwoordigen, waardoor de prestaties van volgende synchronisaties worden verbeterd.

## <a name="how-to-improve-provisioning-performance"></a>De inrichtings prestaties verbeteren

Als de eerste cyclus meer dan een paar uur duurt, kunt u een van de volgende dingen doen om de prestaties te verbeteren:

-   **Filters voor gebruikers bereik.** Met behulp van bereik filters kunt u de gegevens die door de inrichtings service worden geëxtraheerd, afstemmen op de gebruikers op basis van specifieke kenmerk waarden. Zie voor meer informatie over het bereik van filters [kenmerk toepassing inrichten met bereik filters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Volgende stappen
[Automate User Provisioning and Deprovisioning to SaaS Applications with Azure Active Directory](user-provisioning.md) (Automatisch gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory)

