---
title: Azure-bronrollen toewijzen in Privileged Identity Management - Azure Active Directory | Microsoft Documenten
description: Meer informatie over het toewijzen van Azure-bronrollen in Azure AD Privileged Identity Management (PIM).
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
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34051a31c6ccf69356f330d7c5ecb009f760857a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266557"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Azure-bronrollen toewijzen in Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kan de ingebouwde Azure-bronrollen beheren, evenals aangepaste rollen, waaronder (maar niet beperkt tot):

- Eigenaar
- Beheerder van gebruikerstoegang
- Inzender
- Beveiligingsbeheerder
- Beveiligingsmanager

> [!NOTE]
> Gebruikers of leden van een groep die is toegewezen aan de abonnementsrollen Voor eigenaren of gebruikerstoegangsbeheerders en Azure AD Global-beheerders die abonnementsbeheer in Azure AD inschakelen, hebben standaard beheerders van resources. Deze beheerders kunnen rollen toewijzen, rolinstellingen configureren en toegang controleren met privileged identity management voor Azure-bronnen. Een gebruiker kan privileged identity management voor resources niet beheren zonder toestemming voor resourcebeheerders. Bekijk de lijst met [ingebouwde rollen voor Azure-resources.](../../role-based-access-control/built-in-roles.md)

## <a name="assign-a-role"></a>Een rol toewijzen

Volg deze stappen om een gebruiker in aanmerking te laten komen voor een Azure-bronrol.

1. Meld u aan bij [azure portal](https://portal.azure.com/) met een gebruiker die lid is van de rol privileged [role administrator.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

    Zie Toegang verlenen aan andere beheerders om privileged [identity management te beheren](pim-how-to-give-access-to-pim.md)voor informatie over het verlenen van toegang aan een andere beheerder.

1. Azure **AD Privileged Identity Management openen**.

1. Selecteer **Azure-resources**.

1. Gebruik het **filter Resource** om de lijst met beheerde resources te filteren.

    ![Lijst met Azure-bronnen die u wilt beheren](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Selecteer de resource die u wilt beheren, zoals een abonnement of beheergroep.

1. Selecteer Onder Beheren de optie **Rollen** om de lijst met rollen voor Azure-resources weer te geven.

    ![Azure-bronnenrollen](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Selecteer **Lid toevoegen** om het deelvenster Nieuwe toewijzing te openen.

1. Selecteer **Selecteer Een rol selecteren** om het rolvenster Selecteren te openen.

    ![Nieuw toewijzingsvenster](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Selecteer een rol die u wilt toewijzen en klik op **Selecteren**.

    Het deelvenster Een lid of groepsvenster selecteren wordt geopend.

1. Selecteer een lid of groep die u aan de rol wilt toewijzen en klik op **Selecteren**.

    ![Een lid of groepsvenster selecteren](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    Het deelvenster Lidmaatschapsinstellingen wordt geopend.

1. Selecteer in de lijst **Toewijzingstype** de optie **In aanmerking komen** of **Actief**.

    ![Deelvenster Lidmaatschappeninstellingen](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Privileged Identity Management voor Azure-resources biedt twee verschillende toewijzingstypen:

    - **In aanmerking komende** toewijzingen vereisen dat het lid van de rol een actie uitvoert om de rol te gebruiken. Acties kunnen bijvoorbeeld een meervoudige verificatiecontrole, het opgeven van een zakelijke reden of het vragen om toestemming bij aangewezen fiatteurs zijn.

    - **Actieve** toewijzingen vereisen niet dat het lid actie uitvoert om de rol te gebruiken. Leden die als actief zijn toegewezen, hebben te allen tijde de bevoegdheden toegewezen aan de rol.

1. Als de toewijzing permanent moet zijn (permanent in aanmerking komend of permanent toegewezen), schakelt u het selectievakje **Permanent** in.

    Afhankelijk van de rolinstellingen wordt het selectievakje mogelijk niet of niet-aanpasbaar weergegeven.

1. Als u een specifieke toewijzingsduur wilt opgeven, schakelt u het selectievakje uit en wijzigt u de begin- en/of einddatum en -tijdvakken.

    ![Lidmaatschapinstellingen - datum en tijd](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Als u klaar bent, selecteert u **Gereed**.

    ![Nieuwe toewijzing - Toevoegen](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Als u de nieuwe roltoewijzing wilt maken, selecteert u **Toevoegen**. Er wordt een melding van de status weergegeven.

    ![Nieuwe toewijzing - Melding](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Een bestaande roltoewijzing bijwerken of verwijderen

Volg deze stappen om een bestaande roltoewijzing bij te werken of te verwijderen.

1. Azure **AD Privileged Identity Management openen**.

1. Selecteer **Azure-resources**.

1. Selecteer de resource die u wilt beheren, zoals een abonnement of beheergroep.

1. Selecteer Onder Beheren de optie **Rollen** om de lijst met rollen voor Azure-resources weer te geven.

    ![Azure-bronrollen - Rol selecteren](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Selecteer de rol die u wilt bijwerken of verwijderen.

1. Zoek de roltoewijzing op de tabbladen **In aanmerking komende rollen** of Actieve **rollen.**

    ![Roltoewijzing bijwerken of verwijderen](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Selecteer **Bijwerken** of **Verwijderen** om de roltoewijzing bij te werken of te verwijderen.

    Zie [Azure-bronrollen uitbreiden of vernieuwen in Privileged Identity Management](pim-resource-roles-renew-extend.md)voor informatie over het uitbreiden van een roltoewijzing.

## <a name="next-steps"></a>Volgende stappen

- [Azure-bronrollen uitbreiden of vernieuwen in Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Azure-bronrolinstellingen configureren in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Azure AD-rollen toewijzen in Privileged Identity Management](pim-how-to-add-role-to-user.md)
