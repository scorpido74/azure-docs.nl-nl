---
title: Roltoewijzingen toevoegen of verwijderen met RBAC en de Azure-portal
description: Meer informatie over het verlenen van toegang tot Azure-bronnen voor gebruikers, groepen, serviceprincipals of beheerde identiteiten met Behulp van RBAC (Azure Role-based Access Control) en de Azure-portal.
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
ms.date: 01/25/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e2458b07602bca3b6f12b2f486b509c11d705461
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246355"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Roltoewijzingen toevoegen of verwijderen met Azure RBAC en de Azure-portal

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]In dit artikel wordt beschreven hoe u rollen toewijzen met behulp van de Azure-portal.

Zie [Beheerdersrollen weergeven en toewijzen in Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md)als u beheerdersrollen in Azure Active Directory moet toewijzen.

## <a name="prerequisites"></a>Vereisten

Als u roltoewijzingen wilt toevoegen of verwijderen, moet u het als ander beschikken:

- `Microsoft.Authorization/roleAssignments/write`en `Microsoft.Authorization/roleAssignments/delete` machtigingen, zoals [beheerder van gebruikerstoegang](built-in-roles.md#user-access-administrator) of [eigenaar](built-in-roles.md#owner)

## <a name="access-control-iam"></a>Toegangsbeheer (IAM)

**Toegangsbeheer (IAM)** is het blad dat u gebruikt om rollen toe te wijzen om toegang te verlenen tot Azure-bronnen. Het is ook bekend als identiteits- en toegangsbeheer en wordt weergegeven op verschillende locaties in de Azure-portal. Hieronder ziet u een voorbeeld van het IAM-blad (Access Control) voor een abonnement.

![IAM-blade (Access Control) voor een abonnement](./media/role-assignments-portal/access-control-subscription.png)

Om het meest effectieve te zijn met het IAM-blad (Access Control), helpt het als u de volgende drie vragen beantwoorden wanneer u een rol probeert toe te wijzen:

1. **Wie heeft er toegang nodig?**

    Wie verwijst naar een gebruiker, groep, serviceprincipal of beheerde identiteit. Dit wordt ook wel een *security principal*genoemd.

1. **Welke rol hebben ze nodig?**

    Machtigingen worden gegroepeerd in rollen. U kiezen uit een lijst met verschillende [ingebouwde rollen](built-in-roles.md) of u uw eigen aangepaste rollen gebruiken.

1. **Waar hebben ze toegang nodig?**

    Wanneer verwijst naar de set van middelen die de toegang van toepassing is op. Waar kan een beheergroep, abonnement, resourcegroep of één resource zijn, zoals een opslagaccount. Dit wordt de *scope*genoemd.

## <a name="add-a-role-assignment"></a>Een roltoewijzing toevoegen

Als u in Azure RBAC toegang wilt verlenen tot een Azure-bron, voegt u een roltoewijzing toe. Volg deze stappen om een rol toe te wijzen.

1. Klik in de Azure-portal op **Alle services** en selecteer vervolgens het bereik waartoe u toegang wilt verlenen. U bijvoorbeeld **Beheergroepen,** **Abonnementen,** **Resourcegroepen**of een resource selecteren.

1. Klik op de specifieke resource voor dat bereik.

1. Klik op **Toegangsbeheer (IAM)**.

1. Klik op het tabblad **Roltoewijzingen** om de roltoewijzingen in dit bereik weer te geven.

    ![Tabblad Toegangsbeheer (IAM) en Toewijzingen van rollen](./media/role-assignments-portal/role-assignments.png)

1. Klik **op** > **Roltoewijzing toevoegen toevoegen**.

   Als u niet bent gemachtigd voor het toewijzen van rollen, is de optie Roltoewijzing toevoegen uitgeschakeld.

   ![Menu Toevoegen](./media/role-assignments-portal/add-menu.png)

    Het deelvenster Roltoewijzing toevoegen wordt geopend.

   ![Deelvenster Roltoewijzing toevoegen](./media/role-assignments-portal/add-role-assignment.png)

1. Selecteer in de vervolgkeuzelijst **Rol** een rol, zoals **Inzender voor virtuele machines**.

1. Selecteer **in** de lijst Selecteren een gebruiker, groep, serviceprincipal of beheerde identiteit. Als u de beveiligings-principal niet in de lijst ziet staan, kunt u tekst typen in het vak **Selecteren** om te zoeken naar weergavenamen, e-mailadressen en object-id's.

1. Klik op **Opslaan** om de rol toe te wijzen.

   Na enkele ogenblikken krijgt de beveiligingsprincipal de toegewezen rol op het geselecteerde bereik.

    ![Roltoewijzing toevoegen die is opgeslagen](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Een gebruiker toewijzen als beheerder van een abonnement

Als u een gebruiker een beheerder van een Azure-abonnement wilt maken, wijst u deze de rol [Eigenaar](built-in-roles.md#owner) toe op het abonnementsbereik. De rol Eigenaar geeft de gebruiker volledige toegang tot alle bronnen in het abonnement, inclusief de toestemming om anderen toegang te verlenen. Deze stappen zijn hetzelfde als die voor elke andere functietoewijzing.

1. Klik in de Azure-portal op de optie **Alle services** en vervolgens op **Abonnementen**.

1. Klik op het abonnement waartoe u toegang wilt verlenen.

1. Klik op **Toegangsbeheer (IAM)**.

1. Klik op het tabblad **Toewijzingen van rollen** om de roltoewijzingen voor dit abonnement weer te geven.

    ![Tabblad Toegangsbeheer (IAM) en Toewijzingen van rollen](./media/role-assignments-portal/role-assignments.png)

1. Klik **op** > **Roltoewijzing toevoegen toevoegen**.

   Als u niet bent gemachtigd voor het toewijzen van rollen, is de optie Roltoewijzing toevoegen uitgeschakeld.

   ![Menu Toevoegen](./media/role-assignments-portal/add-menu.png)

    Het deelvenster Roltoewijzing toevoegen wordt geopend.

   ![Deelvenster Roltoewijzing toevoegen](./media/role-assignments-portal/add-role-assignment.png)

1. Selecteer in de vervolgkeuzelijst **Rol** de rol **Eigenaar**.

1. Selecteer een gebruiker in de lijst **Selecteren**. Als u de gebruiker niet in de lijst ziet staan, kunt u tekst typen in het vak **Selecteren** om in de map te zoeken naar weergavenamen en e-mailadressen.

1. Klik op **Opslaan** om de rol toe te wijzen.

   Na enkele ogenblikken wordt de rol van eigenaar op abonnementsniveau toegewezen aan de gebruiker.

## <a name="remove-a-role-assignment"></a>Roltoewijzing verwijderen

Als u in Azure RBAC de toegang uit een Azure-bron wilt verwijderen, verwijdert u een roltoewijzing. Volg deze stappen om een roltoewijzing te verwijderen.

1. Open **Access-besturingselement (IAM)** op een bereik, zoals beheergroep, abonnement, resourcegroep of resource, waar u de toegang wilt verwijderen.

1. Klik op het tabblad **Roltoewijzingen** om alle roltoewijzingen voor dit abonnement weer te geven.

1. Schakel in de lijst met roltoewijzingen het selectievakje in naast de beveiligings-principal met de roltoewijzing die u wilt verwijderen.

   ![Bericht bij verwijderen van roltoewijzing](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Klik op **Verwijderen**.

   ![Bericht bij verwijderen van roltoewijzing](./media/role-assignments-portal/remove-role-assignment.png)

1. Klik op **Ja** om te bevestigen dat u de roltoewijzing inderdaad wilt verwijderen.

    Als u een bericht ziet dat overgenomen roltoewijzingen niet kunnen worden verwijderd, probeert u een roltoewijzing op een onderliggend bereik te verwijderen. U moet Toegangsbeheer (IAM) openen op het bereik waarin de rol is toegewezen en probeer het opnieuw. Een snelle manier om Access control (IAM) op het juiste bereik te openen, is door de kolom **Bereik** te bekijken en op de koppeling naast **(Overgenomen)** te klikken.

   ![Bericht bij verwijderen van roltoewijzing](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Volgende stappen

- [Roltoewijzingen weergeven met Azure RBAC en de Azure-portal](role-assignments-list-portal.md)
- [Zelfstudie: Een gebruiker toegang verlenen tot Azure-bronnen met RBAC en de Azure-portal](quickstart-assign-role-user-portal.md)
- [Problemen met RBAC voor Azure-resources oplossen](troubleshooting.md)
- [Uw resources ordenen met Azure-beheergroepen](../governance/management-groups/overview.md)
