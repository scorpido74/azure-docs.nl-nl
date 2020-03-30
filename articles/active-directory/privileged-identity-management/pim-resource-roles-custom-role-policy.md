---
title: Aangepaste rollen gebruiken voor Azure-resources in PIM - Azure AD | Microsoft Documenten
description: Meer informatie over het gebruik van aangepaste rollen voor Azure-resources in Azure AD Privileged Identity Management (PIM).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbe08cff2b57155f8f3315f5d3454abfbdad47a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847066"
---
# <a name="use-custom-roles-for-azure-resources-in-privileged-identity-management"></a>Aangepaste rollen gebruiken voor Azure-resources in Privileged Identity Management

Mogelijk moet u strikte PIM-instellingen (Privileged Identity Management) toepassen op sommige gebruikers in een bevoorrechte rol in uw Azure AD-organisatie (Azure Directory) en anderen meer autonomie bieden. Denk bijvoorbeeld aan een scenario waarin uw organisatie meerdere contractmedewerkers inhuurt om te helpen bij de ontwikkeling van een toepassing die wordt uitgevoerd in een Azure-abonnement.

Als resourcebeheerder wilt u dat werknemers in aanmerking komen voor toegang zonder dat hiervoor goedkeuring nodig is. Alle contractmedewerkers moeten echter worden goedgekeurd wanneer ze toegang vragen tot de resources van de organisatie.

Volg de stappen die in de volgende sectie worden beschreven om gerichte instellingen voor privileged Identity Management in te stellen voor Azure-bronrollen.

## <a name="create-the-custom-role"></a>De aangepaste rol maken

Als u een aangepaste rol voor een resource wilt maken, voert u de stappen uit die zijn beschreven in [Aangepaste rollen maken voor Azure Role-Based Access Control](../role-based-access-control-custom-roles.md).

Wanneer u een aangepaste rol maakt, moet u een beschrijvende naam opnemen, zodat u gemakkelijk onthouden welke ingebouwde rol u wilt dupliceren.

> [!NOTE]
> Zorg ervoor dat de aangepaste rol een duplicaat is van de ingebouwde rol die u wilt dupliceren en dat het bereik overeenkomt met de ingebouwde rol.

## <a name="apply-pim-settings"></a>PIM-instellingen toepassen

Nadat de rol is gemaakt in uw Azure AD-organisatie, gaat u naar de pagina **Privileged Identity Management - Azure resources** in de Azure-portal. Selecteer de resource waarop de rol van toepassing is.

![Het deelvenster 'Privileged Identity Management - Azure resources'](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[De functie-instellingen voor privileged identity management configureren](pim-resource-roles-configure-role-settings.md) die van toepassing moeten zijn op deze leden van de rol.

Wijs ten slotte [rollen toe](pim-resource-roles-assign-roles.md) aan de afzonderlijke groep leden die u met deze instellingen wilt targeten.

## <a name="next-steps"></a>Volgende stappen

- [Azure-bronrolinstellingen configureren in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Aangepaste rollen in Azure](../../role-based-access-control/custom-roles.md)
