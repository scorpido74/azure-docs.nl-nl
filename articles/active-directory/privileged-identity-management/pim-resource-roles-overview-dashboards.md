---
title: Resourcedashboards voor toegangsbeoordelingen in PIM - Azure AD | Microsoft Documenten
description: Beschrijft hoe u een resourcedashboard gebruiken om een toegangscontrole uit te voeren in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6affa2ecc8919dabeb6173622b525280ce96bcfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847034"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>Een resourcedashboard gebruiken om een toegangscontrole uit te voeren in Privileged Identity Management

U een resourcedashboard gebruiken om een toegangscontrole uit te voeren in Privileged Identity Management (PIM). Het dashboard Beheerweergave in Azure Active Directory (Azure AD) bevat drie primaire componenten:

- Een grafische weergave van activeringen van resourcerollen
- Grafieken die de verdeling van roltoewijzingen per toewijzingstype weergeven
- Een gegevensgebied met informatie over nieuwe roltoewijzingen

![Schermafbeelding van het dashboard voor beheerweergave met grafieken en grafieken](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Schermafbeelding van het dashboard voor beheerweergave, met gegevenslijsten](media/pim-resource-roles-overview-dashboards/role-settings.png)

De grafische weergave van resourcerolactiveringen bestrijkt de afgelopen zeven dagen. Deze gegevens worden weergegeven in de geselecteerde bron en worden activeringen weergegeven voor de meest voorkomende rollen (eigenaar, bijdrager, gebruikerstoegangsbeheerder) en voor alle gecombineerde rollen.

Aan de ene kant van de activeringsgrafiek geven twee grafieken de verdeling van roltoewijzingen per toewijzingstype weer, voor zowel gebruikers als groepen. U de waarde wijzigen in een percentage (of omgekeerd), door een segment van de grafiek te selecteren.

Hieronder de grafieken worden het aantal gebruikers en groepen met nieuwe roltoewijzingen in de afgelopen 30 dagen vermeld en rollen gesorteerd op totale toewijzingen in aflopende volgorde.

## <a name="next-steps"></a>Volgende stappen

- [Een toegangscontrole voor Azure-bronrollen starten in Privileged Identity Management](pim-resource-roles-start-access-review.md)
