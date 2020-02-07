---
title: Gebruikers inrichten voor Azure AD Gallery-app neemt uren of meer in beslag
description: Nagaan waarom het inrichten van uw toepassing langer duurt dan verwacht
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c5cc2c47eafba7d6afdf506eab03a17aa8987be
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77067043"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Gebruikers die een Azure AD-galerie toepassing inrichten, nemen uren of meer in beslag

Wanneer u voor het eerst automatische inrichting voor een toepassing inschakelt, kan de eerste cyclus van 20 minuten tot enkele uren duren, afhankelijk van de grootte van de Azure AD-adres lijst en het aantal gebruikers in het bereik dat moet worden ingericht. 

Volgende synchronisaties na de eerste cyclus worden sneller uitgevoerd, omdat de inrichtings service de water merken opslaat die de status van beide systemen na de eerste cyclus vertegenwoordigen, waardoor de prestaties van volgende synchronisaties worden verbeterd.

## <a name="how-to-improve-provisioning-performance"></a>De inrichtings prestaties verbeteren

Als de eerste cyclus meer dan een paar uur duurt, kunt u een van de volgende dingen doen om de prestaties te verbeteren:

-   **Filters voor gebruikers bereik.** Met behulp van bereik filters kunt u de gegevens die door de inrichtings service worden geëxtraheerd, afstemmen op de gebruikers op basis van specifieke kenmerk waarden. Zie voor meer informatie over het bereik van filters [kenmerk toepassing inrichten met bereik filters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Volgende stappen
[Automate User Provisioning and Deprovisioning to SaaS Applications with Azure Active Directory](user-provisioning.md) (Automatisch gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory)

