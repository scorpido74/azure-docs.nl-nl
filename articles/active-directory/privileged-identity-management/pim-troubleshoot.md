---
title: Een probleem met Privileged Identity Management oplossen - Azure Active Directory | Microsoft Documenten
description: Meer informatie over het oplossen van systeemfouten met rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/18/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 474f2634e6f7ddc1840548c39ae86cb54c3bf08e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299666"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Een probleem met Privileged Identity Management oplossen

Heeft u een probleem met Privileged Identity Management (PIM) in Azure Active Directory (Azure AD)? De informatie die volgt kan u helpen om dingen weer aan het werk te krijgen.

## <a name="access-to-azure-resources-denied"></a>Toegang tot Azure-bronnen geweigerd

### <a name="problem"></a>Probleem

Als actieve eigenaar of gebruikerstoegangsbeheerder voor een Azure-bron u uw bron zien in Privileged Identity Management, maar u geen acties uitvoeren, zoals het maken van een in aanmerking komende toewijzing of het weergeven van een lijst met roltoewijzingen uit de bron overzichtspagina. Een van deze acties resulteert in een autorisatiefout.

### <a name="cause"></a>Oorzaak

Dit probleem kan optreden wanneer de rol gebruikerstoegangsbeheerders voor de PIM-serviceprincipal per ongeluk uit het abonnement is verwijderd. Als de service Privileged Identity Management toegang wil krijgen tot Azure-bronnen, moet de MS-PIM-serviceprincipal altijd de [functie Gebruikerstoegangsbeheerder](../../role-based-access-control/built-in-roles.md#user-access-administrator) boven het Azure-abonnement hebben toegewezen.

### <a name="resolution"></a>Oplossing

Wijs de functie Gebruikerstoegangsbeheerder toe aan de hoofdnaam van de Privileged Identity Management-service (MS–PIM) op abonnementsniveau. Met deze toewijzing moet de service Voor beheer van de bevoegde identiteit toegang krijgen tot de Azure-bronnen. De rol kan worden toegewezen op het niveau van de managementgroep of op abonnementsniveau, afhankelijk van uw vereisten. Zie Een toepassing toewijzen [aan een rol voor](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application)meer informatieserviceprincipals.

## <a name="next-steps"></a>Volgende stappen

- [Licentievereisten voor het gebruik van Privileged Identity Management](subscription-requirements.md)
- [Bevoegde toegang beveiligen voor hybride implementaties en cloudimplementaties in Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Privileged Identity Management implementeren](pim-deployment-plan.md)
