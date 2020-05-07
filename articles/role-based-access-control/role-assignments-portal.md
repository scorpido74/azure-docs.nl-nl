---
title: Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure Portal-Azure RBAC
description: Meer informatie over het verlenen van toegang tot Azure-resources voor gebruikers, groepen, service-principals of beheerde identiteiten met behulp van de Azure Portal en Azure op rollen gebaseerd toegangs beheer (Azure RBAC).
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
ms.openlocfilehash: 88cb1e9893e0ca058e57ce12f00747a59248fb19
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735688"
---
# <a name="add-or-remove-azure-role-assignments-using-the-azure-portal"></a>Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure Portal

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]In dit artikel wordt beschreven hoe u rollen toewijst met behulp van de Azure Portal.

Zie [beheerders rollen weer geven en toewijzen in azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md)als u beheerders rollen wilt toewijzen in azure Active Directory.

## <a name="prerequisites"></a>Vereisten

Om roltoewijzingen toe te voegen of te verwijderen, hebt u het volgende nodig:

- `Microsoft.Authorization/roleAssignments/write`en `Microsoft.Authorization/roleAssignments/delete` machtigingen, zoals beheerder of [eigenaar](built-in-roles.md#owner) van [gebruikers toegang](built-in-roles.md#user-access-administrator)

## <a name="access-control-iam"></a>Toegangsbeheer (IAM)

**Toegangs beheer (IAM)** is de Blade die u gebruikt om rollen toe te wijzen om toegang te verlenen tot Azure-resources. Het is ook bekend als identiteits-en toegangs beheer en wordt weer gegeven op verschillende locaties in de Azure Portal. Hieronder ziet u een voor beeld van de Blade toegangs beheer (IAM) voor een abonnement.

![Blade toegangs beheer (IAM) voor een abonnement](./media/role-assignments-portal/access-control-subscription.png)

Als u de Blade toegangs beheer (IAM) het meest effectief wilt maken, kunt u het beste de volgende drie vragen beantwoorden wanneer u een rol probeert toe te wijzen:

1. **Wie moet er toegang toe hebben?**

    Wie verwijst naar een gebruiker, groep, Service-Principal of beheerde identiteit. Dit wordt ook wel een *beveiligingsprincipal*genoemd.

1. **Welke rol hebt u nodig?**

    Machtigingen worden samen in rollen gegroepeerd. U kunt kiezen uit een lijst met verschillende [ingebouwde rollen](built-in-roles.md) of u kunt uw eigen aangepaste rollen gebruiken.

1. **Waar hebben ze toegang nodig?**

    Waar verwijst naar de set resources waarop de toegang van toepassing is. Waar kan een beheer groep, een abonnement, een resource groep of een enkele resource, zoals een opslag account, zijn. Dit wordt het *bereik*genoemd.

## <a name="add-a-role-assignment"></a>Een roltoewijzing toevoegen

In azure RBAC kunt u een roltoewijzing toevoegen om toegang te verlenen aan een Azure-resource. Volg deze stappen om een rol toe te wijzen.

1. Klik in de Azure Portal op **alle services** en selecteer vervolgens het bereik waaraan u toegang wilt verlenen. U kunt bijvoorbeeld **beheer groepen**, **abonnementen**, **resource groepen**of een resource selecteren.

1. Klik op de specifieke resource voor dat bereik.

1. Klik op **Toegangsbeheer (IAM)**.

1. Klik op **het tabblad roltoewijzingen om de roltoewijzingen** in dit bereik weer te geven.

    ![Het tabblad toegangs beheer (IAM) en roltoewijzingen](./media/role-assignments-portal/role-assignments.png)

1. Klik op**toewijzing van roltoewijzing** **toevoegen.** > 

   Als u niet bent gemachtigd voor het toewijzen van rollen, is de optie Roltoewijzing toevoegen uitgeschakeld.

   ![Menu Toevoegen](./media/role-assignments-portal/add-menu.png)

    Het deelvenster Roltoewijzing toevoegen wordt geopend.

   ![Deelvenster Roltoewijzing toevoegen](./media/role-assignments-portal/add-role-assignment.png)

1. Selecteer in de vervolgkeuzelijst **Rol** een rol, zoals **Inzender voor virtuele machines**.

1. Selecteer een gebruiker, groep, Service-Principal of beheerde identiteit in de **selectie** lijst. Als u de beveiligings-principal niet in de lijst ziet staan, kunt u tekst typen in het vak **Selecteren** om te zoeken naar weergavenamen, e-mailadressen en object-id's.

1. Klik op **Opslaan** om de rol toe te wijzen.

   Na enkele ogen blikken wordt de rol bij de geselecteerde scope toegewezen aan de beveiligingsprincipal.

    ![Roltoewijzing van rol toevoegen is opgeslagen](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Een gebruiker toewijzen als beheerder van een abonnement

Als u een gebruiker beheerder van een Azure-abonnement wilt maken, wijst u deze toe aan de rol van [eigenaar](built-in-roles.md#owner) op het abonnements bereik. De rol eigenaar geeft de gebruiker volledige toegang tot alle resources in het abonnement, met inbegrip van de machtiging om toegang tot anderen te verlenen. Deze stappen zijn hetzelfde als die voor elke andere functietoewijzing.

1. Klik in de Azure-portal op de optie **Alle services** en vervolgens op **Abonnementen**.

1. Klik op het abonnement waartoe u toegang wilt verlenen.

1. Klik op **Toegangsbeheer (IAM)**.

1. Klik op **het tabblad roltoewijzingen om de roltoewijzingen** voor dit abonnement weer te geven.

    ![Het tabblad toegangs beheer (IAM) en roltoewijzingen](./media/role-assignments-portal/role-assignments.png)

1. Klik op**toewijzing van roltoewijzing** **toevoegen.** > 

   Als u niet bent gemachtigd voor het toewijzen van rollen, is de optie Roltoewijzing toevoegen uitgeschakeld.

   ![Menu Toevoegen](./media/role-assignments-portal/add-menu.png)

    Het deelvenster Roltoewijzing toevoegen wordt geopend.

   ![Deelvenster Roltoewijzing toevoegen](./media/role-assignments-portal/add-role-assignment.png)

1. Selecteer in de vervolgkeuzelijst **Rol** de rol **Eigenaar**.

1. Selecteer een gebruiker in de lijst **Selecteren**. Als u de gebruiker niet in de lijst ziet staan, kunt u tekst typen in het vak **Selecteren** om in de map te zoeken naar weergavenamen en e-mailadressen.

1. Klik op **Opslaan** om de rol toe te wijzen.

   Na enkele ogenblikken wordt de rol van eigenaar op abonnementsniveau toegewezen aan de gebruiker.

## <a name="remove-a-role-assignment"></a>Roltoewijzing verwijderen

In azure RBAC kunt u de toegang van een Azure-resource verwijderen door een roltoewijzing te verwijderen. Volg deze stappen om een roltoewijzing te verwijderen.

1. Open **toegangs beheer (IAM)** in een bereik, zoals de beheer groep, het abonnement, de resource groep of de resource, waar u de toegang wilt verwijderen.

1. Klik op het tabblad **Roltoewijzingen** om alle roltoewijzingen voor dit abonnement weer te geven.

1. Schakel in de lijst met roltoewijzingen het selectievakje in naast de beveiligings-principal met de roltoewijzing die u wilt verwijderen.

   ![Bericht bij verwijderen van roltoewijzing](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Klik op **Verwijderen**.

   ![Bericht bij verwijderen van roltoewijzing](./media/role-assignments-portal/remove-role-assignment.png)

1. Klik op **Ja** om te bevestigen dat u de roltoewijzing inderdaad wilt verwijderen.

    Als u een bericht ziet dat overgenomen roltoewijzingen niet kunnen worden verwijderd, probeert u een roltoewijzing te verwijderen uit een onderliggend bereik. Open toegangs beheer (IAM) bij het bereik waaraan de rol is toegewezen en probeer het opnieuw. Een snelle manier om toegangs beheer (IAM) in het juiste bereik te openen, is door de kolom **bereik** te bekijken en op de koppeling naast **(overgenomen)** te klikken.

   ![Bericht bij verwijderen van roltoewijzing](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure-roltoewijzingen weer geven met behulp van de Azure Portal](role-assignments-list-portal.md)
- [Zelf studie: een gebruiker toegang verlenen tot Azure-resources met behulp van de Azure Portal](quickstart-assign-role-user-portal.md)
- [Problemen met Azure RBAC oplossen](troubleshooting.md)
- [Uw resources organiseren met Azure-beheer groepen](../governance/management-groups/overview.md)
