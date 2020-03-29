---
title: Roltoewijzingen weergeven met Azure RBAC en de Azure-portal
description: Meer informatie over het bepalen van welke resources gebruikers, groepen, serviceprincipals of beheerde identiteiten toegang hebben tot het gebruik van RBAC (Azure role-based access control) en de Azure-portal.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 03a3d3c7d572d7ec5b8d3ac3d527d0d59e649bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062244"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Roltoewijzingen weergeven met Azure RBAC en de Azure-portal

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]In dit artikel wordt beschreven hoe u roltoewijzingen weergeven met behulp van de Azure-portal.

> [!NOTE]
> Als uw organisatie beheerfuncties heeft uitbesteed aan een serviceprovider die [azure-gedelegeerd resourcebeheer](../lighthouse/concepts/azure-delegated-resource-management.md)gebruikt, worden roltoewijzingen die zijn geautoriseerd door die serviceprovider, hier niet weergegeven.

## <a name="list-role-assignments-for-a-user-or-group"></a>Roltoewijzingen voor een gebruiker of groep weergeven

De eenvoudigste manier om de rollen te zien die zijn toegewezen aan een gebruiker of groep in een abonnement, is door het deelvenster **Azure-bronnen** te gebruiken.

1. Klik in de Azure-portal op **Alle services** en selecteer **Vervolgens Gebruikers** of **groepen**.

1. Klik op de gewenste gebruiker of groep, de roltoewijzingen voor.

1. Klik op **Azure-resources**.

    U ziet een lijst met rollen die aan de geselecteerde gebruiker of groep zijn toegewezen op verschillende scopes, zoals beheergroep, abonnement, resourcegroep of resource. Deze lijst bevat alle roltoewijzingen die u lezen.

    ![Roltoewijzingen voor een gebruiker](./media/role-assignments-list-portal/azure-resources-user.png)    

1. Als u het abonnement wilt wijzigen, klikt u op de lijst **Abonnementen.**

## <a name="list-owners-of-a-subscription"></a>Eigenaren van een abonnement aanbieden

Gebruikers aan de [eigenaarrol](built-in-roles.md#owner) voor een abonnement kunnen alles in het abonnement beheren. Volg deze stappen om de eigenaren van een abonnement aan te geven.

1. Klik in de Azure-portal op de optie **Alle services** en vervolgens op **Abonnementen**.

1. Klik op het abonnement waarvan u de eigenaren wilt vermelden.

1. Klik op **Toegangsbeheer (IAM)**.

1. Klik op het tabblad **Roltoewijzingen** om alle roltoewijzingen voor dit abonnement weer te geven.

1. Blader naar de sectie **Eigenaren** om alle gebruikers te zien die de rol Eigenaar voor dit abonnement hebben toegewezen.

   ![Besturingselement voor abonnementstoegang - tabblad Toewijzingen van rollen](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>Roltoewijzingen op een bereik aanbieden

1. Klik in de Azure-portal op **Alle services** en selecteer het bereik. U bijvoorbeeld **Beheergroepen,** **Abonnementen,** **Resourcegroepen**of een resource selecteren.

1. Klik op de specifieke resource.

1. Klik op **Toegangsbeheer (IAM)**.

1. Klik op het tabblad **Roltoewijzingen** om alle roltoewijzingen in dit bereik weer te geven.

   ![Toegangsbeheer - tabblad Toewijzingen van rollen](./media/role-assignments-list-portal/access-control-role-assignments.png)

   Op het tabblad Toewijzingen van rollen u zien wie toegang heeft tot dit bereik. U ziet dat voor sommige rollen het bereik is ingesteld op **Deze resource**, terwijl andere zijn **(Overgenomen)** uit een ander bereik. Toegang is specifiek toegewezen aan deze bron of overgenomen van een toewijzing aan het bovenliggende bereik.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Roltoewijzingen voor een gebruiker op een bereik aanbieden

Als u toegang wilt aanbieden voor een gebruiker, groep, serviceprincipal of beheerde identiteit, geeft u hun roltoewijzingen aan. Volg deze stappen om de roltoewijzingen voor één gebruiker, groep, serviceprincipal of beheerde identiteit op een bepaald bereik weer te geven.

1. Klik in de Azure-portal op **Alle services** en selecteer het bereik. U bijvoorbeeld **Beheergroepen,** **Abonnementen,** **Resourcegroepen**of een resource selecteren.

1. Klik op de specifieke resource.

1. Klik op **Toegangsbeheer (IAM)**.

1. Klik op het tabblad **Toegang controleren**.

    ![Toegangsbeheer - Tabblad Toegang controleren](./media/role-assignments-list-portal/access-control-check-access.png)

1. Selecteer in de lijst **Zoeken** het type beveiligings-principal waarvoor u de toegang wilt controleren.

1. Voer in het zoekvak een tekenreeks in om de map te doorzoeken op weergavenamen, e-mailadressen of object-id's.

    ![De toegangsselectielijst controleren](./media/role-assignments-list-portal/check-access-select.png)

1. Klik op de beveiligings-principal om het deelvenster **Toewijzingen** te openen.

    ![Deelvenster Toewijzingen](./media/role-assignments-list-portal/check-access-assignments.png)

    In dit deelvenster ziet u de rollen die zijn toegewezen aan de geselecteerde beveiligings-principal en het bereik. Als er een in dit bereik toewijzingen zijn geweigerd of zijn overgenomen, worden deze weergegeven.

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>Roltoewijzingen voor een door het systeem toegewezen beheerde identiteit weergeven

1. Open in de Azure-portal een door het systeem toegewezen beheerde identiteit.

1. Klik in het linkermenu op **Identiteit**.

    ![Door het systeem toegewezen beheerde identiteit](./media/role-assignments-list-portal/identity-system-assigned.png)

1. Klik **onder Toewijzingen voor**rollen op De Azure **RBAC-rollen weergeven die aan deze beheerde identiteit zijn toegewezen**.

    U ziet een lijst met rollen die zijn toegewezen aan de geselecteerde door het systeem toegewezen beheerde identiteit op verschillende scopes, zoals beheergroep, abonnement, resourcegroep of resource. Deze lijst bevat alle roltoewijzingen die u lezen.

    ![Roltoewijzingen voor een door het systeem toegewezen beheerde identiteit](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>Roltoewijzingen voor een door de gebruiker toegewezen beheerde identiteit weergeven

1. Open in de Azure-portal een door de gebruiker toegewezen beheerde identiteit.

1. Klik op **Azure-resources**.

    U ziet een lijst met rollen die zijn toegewezen aan de geselecteerde door de gebruiker toegewezen beheerde identiteit op verschillende scopes, zoals beheergroep, abonnement, resourcegroep of resource. Deze lijst bevat alle roltoewijzingen die u lezen.

    ![Roltoewijzingen voor een door het systeem toegewezen beheerde identiteit](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. Als u het abonnement wilt wijzigen, klikt u op de lijst **Abonnementen.**

## <a name="list-number-of-role-assignments"></a>Lijst aantal roltoewijzingen

U maximaal **2000** roltoewijzingen in elk abonnement uitvoeren. Om u te helpen deze limiet bij te houden, bevat het tabblad **Toewijzingen van rollen** een grafiek met het aantal roltoewijzingen voor het huidige abonnement.

![Toegangsbeheer - Aantal toewijzingengrafiek](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Als u dicht bij het maximumaantal komt en u probeert meer roltoewijzingen toe te voegen, ziet u een waarschuwing in het deelvenster **Roltoewijzing toevoegen.** Zie [Problemen met Azure RBAC](troubleshooting.md#azure-role-assignments-limit)oplossen voor manieren waarop u het aantal roltoewijzingen verminderen.

![Toegangsbeheer - Waarschuwing voor roltoewijzing toevoegen](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="next-steps"></a>Volgende stappen

- [Roltoewijzingen toevoegen of verwijderen met Azure RBAC en de Azure-portal](role-assignments-portal.md)
- [Problemen met RBAC voor Azure-resources oplossen](troubleshooting.md)
