---
title: Uw omgeving instellen voor Blueprint Operator
description: Meer informatie over het configureren van uw Azure-omgeving voor gebruik met de RBAC-rol (Blueprint Operator built-in role-based access control).
ms.date: 08/26/2019
ms.topic: how-to
ms.openlocfilehash: fba0dd3f2eeb69f768800d1d04640510462d3c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873213"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>Uw omgeving configureren voor een Blueprint Operator

Het beheer van uw blauwdrukdefinities en blauwdruktoewijzingen kan worden toegewezen aan verschillende teams. Het is gebruikelijk dat een architect of governanceteam verantwoordelijk is voor het levenscyclusbeheer van uw blauwdrukdefinities, terwijl een operationeel team verantwoordelijk is voor het beheren van opdrachten van die centraal gecontroleerde blauwdrukdefinities.

De Ingebouwde role-based access control (Blueprint **Operator)** is speciaal ontworpen voor gebruik in dit type scenario. De rol maakt het mogelijk voor operations type teams om de toewijzing van de organisaties blauwdruk definities te beheren, maar niet de mogelijkheid om ze te wijzigen. Hiervoor is een configuratie in uw Azure-omgeving vereist en in dit artikel worden de noodzakelijke stappen uitgelegd.

## <a name="grant-permission-to-the-blueprint-operator"></a>Toestemming verlenen aan de Blueprint Operator

De eerste stap is om de blueprint **operator** rol toe te kennen aan de account of beveiligingsgroep (aanbevolen) die gaat worden het toewijzen van blauwdrukken. Deze actie moet worden uitgevoerd op het hoogste niveau in de hiërarchie van de beheergroep die alle beheergroepen omvat en abonnementen waartoe het operations team toegang moet hebben tot blauwdruktoewijzing. Het wordt aanbevolen om het principe van de minste bevoegdheden te volgen bij het verlenen van deze machtigingen.

1. (Aanbevolen) [Een beveiligingsgroep maken en leden toevoegen](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. [Een roltoewijzing](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) van **Blueprint Operator** toevoegen aan de account- of beveiligingsgroep

## <a name="user-assign-managed-identity"></a>Beheerde identiteit van de gebruiker toewijzen

Een blauwdrukdefinitie kan beheerde identiteiten met het systeem of door de gebruiker toegewezen identiteiten gebruiken. Wanneer u echter de functie **Blueprint Operator** gebruikt, moet de blauwdrukdefinitie worden geconfigureerd om een door de gebruiker toegewezen beheerde identiteit te gebruiken. Bovendien moet de account- of beveiligingsgroep die de functie **Blueprint Operator** krijgt, de rol Managed **Identity Operator** op de door de gebruiker toegewezen beheerde identiteit krijgen. Zonder deze toestemming mislukken blauwdruktoewijzingen vanwege een gebrek aan machtigingen.

1. [Een door de gebruiker toegewezen beheerde identiteit maken](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) voor gebruik door een toegewezen blauwdruk

1. [Voeg een roltoewijzing](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) van **Managed Identity Operator** toe aan het account of de beveiligingsgroep. De roltoewijzing scopen naar de nieuwe door de gebruiker toegewezen beheerde identiteit.

1. [Wijs als](../create-blueprint-portal.md#assign-a-blueprint) **blueprintoperator**een blauwdruk toe die de nieuwe door de gebruiker toegewezen beheerde identiteit gebruikt.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [levenscyclus van de blauwdruk](../concepts/lifecycle.md).
- Begrijpen hoe [statische en dynamische parameters](../concepts/parameters.md)te gebruiken.
- Leer de volgorde van de [blauwdrukvolgorde](../concepts/sequencing-order.md)aan te passen.
- Ontdek hoe u gebruik maken van het vergrendelen van [blauwdrukbronnen.](../concepts/resource-locking.md)
- Los problemen op tijdens de toewijzing van een blauwdruk met [algemene probleemoplossing.](../troubleshoot/general.md)