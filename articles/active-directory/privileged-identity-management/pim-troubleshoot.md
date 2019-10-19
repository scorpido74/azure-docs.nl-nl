---
title: Problemen oplossen met Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Informatie over het oplossen van systeem fouten met rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b7fc3508f7f672e277577f92218ff1860b676cb
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72559474"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Problemen met Privileged Identity Management oplossen

Ondervindt u een probleem met Privileged Identity Management (PIM) in Azure Active Directory (Azure AD)? De volgende informatie kan u helpen bij het ophalen van dingen.

## <a name="access-to-azure-resources-denied"></a>Toegang tot Azure-resources geweigerd

### <a name="problem"></a>Probleem

U krijgt een autorisatie fout wanneer u een gebruiker probeert te maken die in aanmerking komt voor een Azure AD-beheerdersrol en u geen toegang hebt tot Azure-resources onder Privileged Identity Management. U hebt geen toegang tot Azure-resources onder Privileged Identity Management, zelfs niet als u een globale beheerder en de eigenaar van het abonnement bent.

### <a name="cause"></a>Oorzaak

Dit probleem kan zich voordoen wanneer de rol beheerder voor gebruikers toegang voor de PIM-Service-Principal per ongeluk is verwijderd uit het abonnement. De Privileged Identity Management-service kan alleen toegang krijgen tot Azure-resources als de MS-PIM-Service-Principal altijd wordt toegewezen aan de [rol van beheerder voor gebruikers toegang](../../role-based-access-control/built-in-roles.md#user-access-administrator) via het Azure-abonnement.

### <a name="resolution"></a>Resolutie

Wijs de rol beheerder voor gebruikers toegang toe aan de privileged Identity Management Service Principal Name (MS-PIM) op abonnements niveau. Deze toewijzing moet de privileged Identity Management-service toestaan om toegang te krijgen tot de Azure-resources. De rol kan worden toegewezen op een niveau van de beheer groep of op het abonnements niveau, afhankelijk van uw vereisten. Zie [een toepassing toewijzen aan een rol](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role)voor meer informatie over service-principals.

## <a name="next-steps"></a>Volgende stappen

- [Licentie vereisten voor het gebruik van Privileged Identity Management](subscription-requirements.md)
- [Bevoegde toegang beveiligen voor hybride implementaties en cloudimplementaties in Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Privileged Identity Management implementeren](pim-deployment-plan.md)
