---
title: Beveiligingswaarschuwingen configureren voor Azure-bronrollen in Privileged Identity Management - Azure Active Directory | Microsoft Documenten
description: Meer informatie over het configureren van beveiligingswaarschuwingen voor Azure-bronrollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0b938dc808d9b02ad4105d85a5b3125135c51d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74023075"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>Beveiligingswaarschuwingen configureren voor Azure-bronrollen in Privileged Identity Management

Pim (Privileged Identity Management) genereert waarschuwingen wanneer er verdachte of onveilige activiteiten zijn in uw Azure AD-organisatie (Azure Directory). Wanneer een waarschuwing wordt geactiveerd, wordt deze weergegeven op de pagina Waarschuwingen.

![Azure-bronnen - Waarschuwingen pagina vermelding waarschuwing, risiconiveau en telling](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Waarschuwingen bekijken

Selecteer een waarschuwing om een rapport te zien met de gebruikers of rollen die de waarschuwing hebben geactiveerd, samen met herstelrichtlijnen.

![Waarschuwingsrapport met de laatste scantijd, beschrijving, mitigatiestappen, type, ernst, beveiligingsimpact en hoe de volgende keer te voorkomen](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Waarschuwingen

| Waarschuwing | Severity | Trigger | Aanbeveling |
| --- | --- | --- | --- |
| **Te veel eigenaren toegewezen aan een resource** |Middelgroot |Te veel gebruikers hebben de eigenaarrol. |Bekijk de gebruikers in de lijst en wijs sommige opnieuw toe aan minder bevoorrechte rollen. |
| **Te veel permanente eigenaren toegewezen aan een resource** |Middelgroot |Te veel gebruikers zijn permanent toegewezen aan een rol. |Controleer de gebruikers in de lijst en wijs er een aantal opnieuw toe om activering voor rolgebruik te vereisen. |
| **Dubbele rol gemaakt** |Middelgroot |Meerdere rollen hebben dezelfde criteria. |Gebruik slechts één van deze rollen. |

### <a name="severity"></a>Severity

- **Hoog**: Vereist onmiddellijke actie vanwege een beleidsschending. 
- **Medium:** Vereist geen onmiddellijke actie, maar signaleert een mogelijke schending van het beleid.
- **Laag:** vereist geen onmiddellijke actie, maar suggereert een voorkeursbeleidswijziging.

## <a name="configure-security-alert-settings"></a>Instellingen voor beveiligingswaarschuwingen configureren

Ga op de pagina Waarschuwingen naar **Instellingen**.

![Pagina Waarschuwingen met gemarkeerde instellingen](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Pas instellingen aan voor de verschillende waarschuwingen om te werken met uw omgeving en beveiligingsdoelen.

![Pagina instellen voor een waarschuwing om instellingen in te schakelen en te configureren](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure-bronrolinstellingen configureren in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
