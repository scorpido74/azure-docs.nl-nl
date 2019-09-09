---
title: Aangepaste rollen gebruiken voor Azure-resources in PIM-Azure Active Directory | Microsoft Docs
description: Meer informatie over het gebruik van aangepaste rollen voor Azure-resources in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 03/30/2018
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d36514c97cf1f45ee0a435d3b716019d2762e5a
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804188"
---
# <a name="use-custom-roles-for-azure-resources-in-pim"></a>Aangepaste rollen gebruiken voor Azure-resources in PIM

U moet mogelijk strikte Azure Active Directory (Azure AD) Privileged Identity Management (PIM)-instellingen Toep assen op sommige leden van een rol, terwijl er meer autonomie voor anderen beschikbaar is. Overweeg een scenario waarin uw organisatie verschillende contract Associates inhuurt om u te helpen bij het ontwikkelen van een toepassing die wordt uitgevoerd in een Azure-abonnement.

Als resource beheerder wilt u dat werk nemers in aanmerking komen voor toegang zonder dat hiervoor goed keuring is vereist. Alle contract Associates moeten echter worden goedgekeurd wanneer ze toegang aanvragen tot de resources van de organisatie.

Volg de stappen die worden beschreven in de volgende sectie voor het instellen van specifieke PIM-instellingen voor Azure-resource rollen.

## <a name="create-the-custom-role"></a>De aangepaste rol maken

Als u een aangepaste rol voor een resource wilt maken, volgt u de stappen die worden beschreven in [aangepaste rollen maken voor op rollen gebaseerde Access Control van Azure](../role-based-access-control-custom-roles.md).

Wanneer u een aangepaste rol maakt, moet u een beschrijvende naam toevoegen, zodat u gemakkelijk kunt onthouden welke ingebouwde rol u wilde dupliceren.

> [!NOTE]
> Zorg ervoor dat de aangepaste rol een duplicaat is van de ingebouwde rol die u wilt dupliceren en dat het bereik overeenkomt met de ingebouwde rol.

## <a name="apply-pim-settings"></a>PIM-instellingen Toep assen

Nadat de functie is gemaakt in uw Tenant, gaat u in het Azure Portal naar het deel venster **privileged Identity Management-Azure-resources** . Selecteer de resource waarop de rol van toepassing is.

![Het deel venster Privileged Identity Management-Azure-resources](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Configureer instellingen voor PIM-rollen](pim-resource-roles-configure-role-settings.md) die moeten worden toegepast op deze leden van de rol.

Wijs tot slot [rollen](pim-resource-roles-assign-roles.md) toe aan de afzonderlijke groep leden waarop u deze instellingen wilt Toep assen.

## <a name="next-steps"></a>Volgende stappen

- [Instellingen voor Azure-resource rollen configureren in PIM](pim-resource-roles-configure-role-settings.md)
- [Aangepaste rollen in Azure](../../role-based-access-control/custom-roles.md)
