---
title: Azure-resource rollen toewijzen in PIM-Azure Active Directory | Microsoft Docs
description: Meer informatie over het toewijzen van Azure-resource rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5919c9ab53761649c55e67ceacec0409174e9120
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617034"
---
# <a name="assign-azure-resource-roles-in-pim"></a>Azure-resource rollen toewijzen in PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kan de ingebouwde Azure-resource rollen beheren, evenals aangepaste rollen, inclusief (maar niet beperkt tot):

- Eigenaar
- Administrator voor gebruikerstoegang
- Inzender
- Beveiligingsbeheerder
- Beveiligings beheer en meer

> [!NOTE]
> Gebruikers of leden van een groep die zijn toegewezen aan de rol van eigenaar of gebruikers toegang, en globale beheerders die het beheer van abonnementen in azure AD inschakelen, zijn resource beheerders. Deze beheerders kunnen rollen toewijzen, rolinstellingen configureren en toegang controleren met behulp van PIM voor Azure-resources. Dat wil zeggen dat het account niet over de rechten beschikt om PIM voor resources te beheren als de gebruiker geen rol voor resource beheerder heeft. Bekijk de lijst met [ingebouwde rollen voor Azure-resources](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Een rol toewijzen

Volg deze stappen om een gebruiker in aanmerking te laten komen voor een Azure-resource functie.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) met een gebruiker die lid is van de beheerdersrol [privileged Role](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

    Zie [toegang verlenen aan andere beheerders](pim-how-to-give-access-to-pim.md)voor het beheren van Pim voor informatie over het verlenen van andere beheerders toegang tot het beheer van Pim.

1. Open **Azure AD privileged Identity Management**.

    Als u nog geen PIM hebt gestart in de Azure Portal, gaat u naar [Start met behulp van Pim](pim-getting-started.md).

1. Klik op **Azure-resources**.

1. Gebruik het **resource filter** om de lijst met beheerde resources te filteren.

    ![Lijst met Azure-resources die u wilt beheren](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Klik op de resource die u wilt beheren, zoals een abonnement of beheer groep.

1. Klik onder beheren op **functies** om de lijst met rollen voor Azure-resources weer te geven.

    ![Azure-resources rollen](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Klik op **lid toevoegen** om het deel venster nieuwe toewijzing te openen.

1. Klik op **Selecteer een rol** om het deel venster een rol selecteren te openen.

    ![Deel venster nieuwe toewijzing](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Klik op een rol die u wilt toewijzen en klik vervolgens op **selecteren**.

    Het deel venster een lid of groep selecteren wordt geopend.

1. Klik op een lid of groep die u aan de rol wilt toewijzen en klik vervolgens op **selecteren**.

    ![Een lid of groeps deel venster selecteren](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    Het deel venster lidmaatschaps instellingen wordt geopend.

1. Selecteer in **aanmerking komend** of **actief**in de lijst **toewijzings type** .

    ![Deel venster instellingen voor lidmaatschappen](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    PIM voor Azure-resources biedt twee verschillende toewijzings typen:

    - Voor **in aanmerking komende** toewijzingen moet het lid van de rol een actie uitvoeren om de rol te gebruiken. Acties kunnen bijvoorbeeld een meervoudige verificatiecontrole, het opgeven van een zakelijke reden of het vragen om toestemming bij aangewezen fiatteurs zijn.

    - Voor **actieve** toewijzingen hoeft het lid geen actie te ondernemen om de rol te gebruiken. Aan leden die als actief zijn toegewezen, zijn de bevoegdheden altijd toegewezen aan de rol.

1. Als de toewijzing permanent moet zijn (permanent in aanmerking komend of permanent toegewezen), schakelt u het selectie vakje **permanent** in.

    Afhankelijk van de rolinstellingen, wordt het selectie vakje mogelijk niet weer gegeven of kan het niet worden gewijzigd.

1. Als u een specifieke duur voor de toewijzing wilt opgeven, schakelt u het selectie vakje uit en wijzigt u de begin-en/of eind datum en-tijd vakken.

    ![Instellingen voor lidmaatschappen-datum en tijd](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Klik op **Gereed** als u klaar bent.

    ![Nieuwe toewijzing-toevoegen](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Als u de nieuwe roltoewijzing wilt maken, klikt u op **toevoegen**. Er wordt een melding van de status weer gegeven.

    ![Nieuwe toewijzing-melding](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Een bestaande roltoewijzing bijwerken of verwijderen

Volg deze stappen om een bestaande roltoewijzing bij te werken of te verwijderen.

1. Open **Azure AD privileged Identity Management**.

1. Klik op **Azure-resources**.

1. Klik op de resource die u wilt beheren, zoals een abonnement of beheer groep.

1. Klik onder beheren op **functies** om de lijst met rollen voor Azure-resources weer te geven.

    ![Azure-resource rollen: rol selecteren](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Klik op de rol die u wilt bijwerken of verwijderen.

1. Zoek naar de roltoewijzing op de tabbladen **in aanmerking komende rollen** of **actieve rollen** .

    ![Roltoewijzing bijwerken of verwijderen](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Klik op **bijwerken** of **verwijderen** om de roltoewijzing bij te werken of te verwijderen.

    Zie [Azure-resource rollen uitbreiden of vernieuwen in PIM](pim-resource-roles-renew-extend.md)voor meer informatie over het uitbreiden van een roltoewijzing.

## <a name="next-steps"></a>Volgende stappen

- [Azure-resource rollen in PIM uitbreiden of vernieuwen](pim-resource-roles-renew-extend.md)
- [Instellingen voor Azure-resource rollen configureren in PIM](pim-resource-roles-configure-role-settings.md)
- [Azure AD-rollen toewijzen in PIM](pim-how-to-add-role-to-user.md)
