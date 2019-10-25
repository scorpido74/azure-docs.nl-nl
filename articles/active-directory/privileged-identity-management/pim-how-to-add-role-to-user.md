---
title: Azure AD-rollen toewijzen in Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Meer informatie over het toewijzen van Azure AD-rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad03e69df4ca03d58bba98b912ade354f90042e
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809212"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Azure AD-rollen toewijzen in Privileged Identity Management

Met Azure Active Directory (Azure AD) kan een globale beheerder **permanente** Azure AD-beheerdersrol maken. Deze roltoewijzingen kunnen worden gemaakt met behulp van de [Azure Portal](../users-groups-roles/directory-assign-admin-roles.md) of met behulp van [Power shell-opdrachten](/powershell/module/azuread#directory_roles).

Met de service Azure AD Privileged Identity Management (PIM) kunnen beheerders van beschermde rollen ook permanente toewijzingen voor beheerdersrol maken. Daarnaast kunnen beheerders van geprivilegieerde rollen gebruikers die **in aanmerking komen** voor Azure AD-beheerders rollen. Een in aanmerking komende beheerder kan de rol activeren wanneer hij deze nodig heeft en vervolgens de machtigingen verloopt zodra ze zijn voltooid.

## <a name="make-a-user-eligible-for-a-role"></a>Een gebruiker geschikt maken voor een rol

Volg deze stappen om een gebruiker in aanmerking te laten komen voor een Azure AD-beheerdersrol.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) met een gebruiker die lid is van de beheerdersrol [privileged Role](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

    Voor informatie over het verlenen van een andere beheerder toegang tot het beheren van Privileged Identity Management raadpleegt u [toegang verlenen aan andere beheerders om privileged Identity Management te beheren](pim-how-to-give-access-to-pim.md).

1. Open **Azure AD privileged Identity Management**.

    Als u nog geen Privileged Identity Management in de Azure Portal hebt gestart, gaat u naar aan de slag [met privileged Identity Management](pim-getting-started.md).

1. Selecteer **Azure AD-rollen**.

1. Selecteer **functies** of **leden**.

    ![De menu opties voor Azure AD-rollen met rollen en leden gemarkeerd](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Selecteer **lid toevoegen** om beheerde leden toevoegen te openen.

1. Selecteer **een rol selecteren**, selecteer een rol die u wilt beheren en selecteer vervolgens **selecteren**.

    ![Een rollen paneel met Azure AD-rollen selecteren](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Selecteer **leden selecteren**, selecteer de gebruikers die u aan de rol wilt toewijzen en selecteer vervolgens **selecteren**.

    ![Deel venster leden selecteren waar u een gebruiker kunt selecteren](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Selecteer in beheerde leden toevoegen de optie **OK** om de gebruiker aan de rol toe te voegen.

1. Selecteer in de lijst met rollen de rol die u zojuist hebt toegewezen om de lijst met leden weer te geven.

     Wanneer de rol wordt toegewezen, wordt de gebruiker die u hebt geselecteerd, weer gegeven in de lijst met leden die in **aanmerking komt** voor de rol.

    ![Leden van een rol worden weer gegeven samen met hun activerings status](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Nu de gebruiker in aanmerking komt voor de rol, laat hij ze weten dat ze deze kunnen activeren volgens de instructies in [mijn Azure AD-rollen activeren in privileged Identity Management](pim-how-to-activate-role.md).

    In aanmerking komende beheerders worden tijdens de activering gevraagd om zich te registreren voor Azure Multi-Factor Authentication (MFA). Als een gebruiker zich niet kan registreren voor MFA of een Microsoft-account (meestal @outlook.com) gebruikt, moet u deze in al hun rollen blijvend maken.

## <a name="make-a-role-assignment-permanent"></a>Een roltoewijzing permanent maken

Standaard zijn nieuwe gebruikers alleen in aanmerking voor een Azure AD-beheerdersrol. Volg deze stappen als u een roltoewijzing permanent wilt maken.

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **Azure AD-rollen**.

1. Selecteer **leden**.

    ![Azure AD-rollen: leden lijst met de status van de functie en de activering](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selecteer een **in aanmerking komende** rol die u permanent wilt maken.

1. Selecteer **meer** en selecteer vervolgens **machtiging maken**.

    ![Deel venster met een gebruiker die in aanmerking komt voor een rol met de meer menu opties open](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    De rol wordt nu als **permanent**weer gegeven.

    ![Leden lijst met de status van de functie en de activering die nu permanent is](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Een gebruiker uit een rol verwijderen

U kunt gebruikers verwijderen uit roltoewijzingen, maar zorg ervoor dat er altijd ten minste één gebruiker is die een permanente globale beheerder is. Als u niet zeker weet welke gebruikers nog steeds hun roltoewijzingen nodig hebben, kunt u [een toegangs beoordeling starten voor de rol](pim-how-to-start-security-review.md).

Volg deze stappen om een specifieke gebruiker te verwijderen uit een Azure AD-beheerdersrol.

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **Azure AD-rollen**.

1. Selecteer **leden**.

    ![Azure AD-rollen: leden lijst met de statistieken voor rollen en activering](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selecteer de roltoewijzing die u wilt verwijderen.

1. Selecteer **meer** en vervolgens **verwijderen**.

    ![Deel venster met een gebruiker die een permanente rol heeft met de meer menu opties open](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Wanneer u wordt gevraagd om de actie te bevestigen, selecteert u **Ja**.

    ![Bericht waarin u wordt gevraagd of u het lid wilt verwijderen uit de rol](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    De roltoewijzing wordt verwijderd.

## <a name="authorization-error-when-assigning-roles"></a>Autorisatie fout bij het toewijzen van rollen

Scenario: als actieve eigenaar of beheerder van de gebruikers toegang voor een Azure-resource, kunt u uw resource in Privileged Identity Management zien, maar kunt u geen acties uitvoeren zoals het maken van een in aanmerking komende toewijzing of het weer geven van een lijst met roltoewijzingen uit de pagina overzicht van resources. Een van deze acties resulteert in een autorisatie fout.

Voor het toewijzen van rollen moet de MS-PIM-service-principal worden toegewezen aan de [rol van beheerder voor gebruikers toegang](../../role-based-access-control/built-in-roles.md#user-access-administrator) in toegangs beheer op basis van rollen voor toegang tot Azure-bronnen (in plaats van Azure AD-beheer rollen). In plaats van te wachten totdat MS-PIM is toegewezen aan de rol beheerder van gebruikers toegang, kunt u deze hand matig toewijzen.

Met de volgende stappen wordt de rol gebruikers toegang beheerder toegewezen aan de MS-PIM-service-principal voor een abonnement.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een globale beheerder in uw Azure AD-organisatie.

1. Kies **alle services** en vervolgens **abonnementen**.

1. Kies uw abonnement.

1. Kies **Toegangsbeheer (IAM)** .

1. Kies roltoewijzingen om de huidige lijst met **roltoewijzingen in het** abonnements bereik weer te geven.

   ![Blade toegangs beheer (IAM) voor een abonnement](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Controleer of de **MS-PIM** -Service-Principal de rol **beheerder van gebruikers toegang** toegewezen heeft gekregen.

1. Als dat niet het geval is, kiest u **roltoewijzing toevoegen** om het deel venster **roltoewijzing toevoegen** te openen.

1. Selecteer in de vervolg keuzelijst **rol** de rol beheerder voor **gebruikers toegang** .

1. Zoek en selecteer de **MS-PIM-service-** Principal in de **selectie** lijst.

   ![Deel venster roltoewijzing toevoegen-machtigingen voor MS-PIM-Service-Principal toevoegen](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Kies **Opslaan** om de rol toe te wijzen.

   Na enkele ogen blikken is de MS-PIM-Service-Principal toegewezen aan de rol van beheerder voor gebruikers toegang op het abonnements bereik.

   ![Blade toegangs beheer (IAM) met de toewijzing van de rol beheerder van gebruikers toegang voor MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

## <a name="next-steps"></a>Volgende stappen

- [Instellingen voor Azure AD-beheerdersrol configureren in Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Azure-resource rollen toewijzen in Privileged Identity Management](pim-resource-roles-assign-roles.md)
