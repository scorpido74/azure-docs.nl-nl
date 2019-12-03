---
title: Roltoewijzingen toevoegen of verwijderen met behulp van Azure RBAC en de Azure Portal
description: Meer informatie over het verlenen van toegang tot Azure-resources voor gebruikers, groepen, service-principals of beheerde identiteiten met behulp van op rollen gebaseerd toegangs beheer (RBAC) en de Azure Portal van Azure.
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 17a325e96e9709b60da2f23d1dc68e3300fde80c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707868"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Roltoewijzingen toevoegen of verwijderen met behulp van Azure RBAC en de Azure Portal

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] in dit artikel wordt beschreven hoe u rollen toewijst met behulp van de Azure Portal.

Zie [beheerders rollen weer geven en toewijzen in azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md)als u beheerders rollen wilt toewijzen in azure Active Directory.

## <a name="prerequisites"></a>Vereisten

Om roltoewijzingen toe te voegen of te verwijderen, hebt u het volgende nodig:

- `Microsoft.Authorization/roleAssignments/write`-en `Microsoft.Authorization/roleAssignments/delete` machtigingen, zoals beheerder of [eigenaar](built-in-roles.md#owner) van [gebruikers toegang](built-in-roles.md#user-access-administrator)

## <a name="overview-of-access-control-iam"></a>Overzicht van toegangs beheer (IAM)

**Toegangs beheer (IAM)** is de Blade die u gebruikt om rollen toe te wijzen. Het is ook bekend als identiteits-en toegangs beheer en wordt weer gegeven op verschillende locaties in de Azure Portal. Hieronder ziet u een voor beeld van de Blade toegangs beheer (IAM) voor een abonnement.

![Blade toegangs beheer (IAM) voor een abonnement](./media/role-assignments-portal/access-control-subscription.png)

Als u de Blade toegangs beheer (IAM) het meest effectief wilt maken, kunt u het beste de volgende drie vragen beantwoorden wanneer u een rol probeert toe te wijzen:

1. **Wie moet er toegang toe hebben?**

    Wie verwijst naar een gebruiker, groep, Service-Principal of beheerde identiteit. Dit wordt ook wel een *beveiligingsprincipal*genoemd.

1. **Welke rol hebt u nodig?**

    Machtigingen worden samen in rollen gegroepeerd. U kunt kiezen uit een lijst met verschillende [ingebouwde rollen](built-in-roles.md) of uw eigen aangepaste rollen gebruiken.

1. **Waar hebben ze toegang nodig?**

    Waar verwijst naar de set resources waarop de toegang van toepassing is. Waar kan een beheer groep, een abonnement, een resource groep of een enkele resource, zoals een opslag account, zijn. Dit wordt het *bereik*genoemd.

## <a name="add-a-role-assignment"></a>Een roltoewijzing toevoegen

Volg deze stappen om een rol toe te wijzen aan verschillende bereiken.

1. Klik in de Azure Portal op **alle services** en selecteer vervolgens het bereik. U kunt bijvoorbeeld **beheer groepen**, **abonnementen**, **resource groepen**of een resource selecteren.

1. Klik op de specifieke resource.

1. Klik op **Toegangsbeheer (IAM)** .

1. Klik op **het tabblad roltoewijzingen om** alle roltoewijzingen in dit bereik weer te geven.

1. Klik op **Toevoegen** > **Roltoewijzing toevoegen** om het deelvenster Roltoewijzing toevoegen te openen.

   Als u niet bent gemachtigd voor het toewijzen van rollen, is de optie Roltoewijzing toevoegen uitgeschakeld.

   ![Menu Toevoegen](./media/role-assignments-portal/add-menu.png)

   ![Deelvenster Roltoewijzing toevoegen](./media/role-assignments-portal/add-role-assignment.png)

1. Selecteer in de vervolgkeuzelijst **Rol** een rol, zoals **Inzender voor virtuele machines**.

1. Selecteer een gebruiker, groep, Service-Principal of beheerde identiteit in de **selectie** lijst. Als u de beveiligings-principal niet in de lijst ziet staan, kunt u tekst typen in het vak **Selecteren** om te zoeken naar weergavenamen, e-mailadressen en object-id's.

1. Klik op **Opslaan** om de rol toe te wijzen.

   Na enkele ogen blikken wordt de rol bij de geselecteerde scope toegewezen aan de beveiligingsprincipal.

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Een gebruiker toewijzen als beheerder van een abonnement

Als u een gebruiker beheerder van een Azure-abonnement wilt maken, wijst u deze toe aan de rol van [eigenaar](built-in-roles.md#owner) op het abonnements bereik. De rol eigenaar geeft de gebruiker volledige toegang tot alle resources in het abonnement, met inbegrip van de machtiging om toegang tot anderen te verlenen. Deze stappen zijn hetzelfde als die voor elke andere functietoewijzing.

1. Klik in de Azure-portal op de optie **Alle services** en vervolgens op **Abonnementen**.

1. Klik op het abonnement waaraan u een roltoewijzing wilt toevoegen.

1. Klik op **Toegangsbeheer (IAM)** .

1. Klik op het tabblad **Roltoewijzingen** om alle roltoewijzingen voor dit abonnement weer te geven.

1. Klik op **Toevoegen** > **Roltoewijzing toevoegen** om het deelvenster Roltoewijzing toevoegen te openen.

   Als u niet bent gemachtigd voor het toewijzen van rollen, is de optie Roltoewijzing toevoegen uitgeschakeld.

   ![Menu Toevoegen](./media/role-assignments-portal/add-menu.png)

   ![Deelvenster Roltoewijzing toevoegen](./media/role-assignments-portal/add-role-assignment.png)

1. Selecteer in de vervolgkeuzelijst **Rol** de rol **Eigenaar**.

1. Selecteer een gebruiker in de lijst **Selecteren**. Als u de gebruiker niet in de lijst ziet staan, kunt u tekst typen in het vak **Selecteren** om in de map te zoeken naar weergavenamen en e-mailadressen.

1. Klik op **Opslaan** om de rol toe te wijzen.

   Na enkele ogenblikken wordt de rol van eigenaar op abonnementsniveau toegewezen aan de gebruiker.

## <a name="remove-a-role-assignment"></a>Roltoewijzing verwijderen

Als u in RBAC de toegang wilt intrekken voor een rol, verwijdert u de roltoewijzing. Volg deze stappen om een roltoewijzing te verwijderen.

1. Open **toegangs beheer (IAM)** in een bereik, zoals de beheer groep, het abonnement, de resource groep of de resource, waar u de toegang wilt verwijderen.

1. Klik op het tabblad **Roltoewijzingen** om alle roltoewijzingen voor dit abonnement weer te geven.

1. Schakel in de lijst met roltoewijzingen het selectievakje in naast de beveiligings-principal met de roltoewijzing die u wilt verwijderen.

   ![Bericht bij verwijderen van roltoewijzing](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Klik op **Verwijderen**.

   ![Bericht bij verwijderen van roltoewijzing](./media/role-assignments-portal/remove-role-assignment.png)

1. Klik op **Ja** om te bevestigen dat u de roltoewijzing inderdaad wilt verwijderen.

    Overgenomen toewijzingen kunnen niet worden verwijderd. Als u een overgenomen roltoewijzing wilt verwijderen, moet u dit te doen voor het bereik waarvoor de roltoewijzing is gemaakt. In de kolom **bereik** vindt u naast **(overgenomen)** een koppeling waarmee u naar het bereik gaat waar deze rol is toegewezen. Ga naar het bereik dat hier wordt weergegeven om de roltoewijzing te verwijderen.

   ![Bericht bij verwijderen van roltoewijzing](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Volgende stappen

- [Roltoewijzingen weer geven met behulp van Azure RBAC en de Azure Portal](role-assignments-list-portal.md)
- [Zelf studie: een gebruiker toegang verlenen tot Azure-resources met RBAC en de Azure Portal](quickstart-assign-role-user-portal.md)
- [Problemen met RBAC voor Azure-resources oplossen](troubleshooting.md)
- [Uw resources organiseren met Azure-beheergroepen](../governance/management-groups/overview.md)
