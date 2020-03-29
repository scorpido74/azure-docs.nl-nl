---
title: Azure AD-rollen toewijzen in PIM - Azure Active Directory | Microsoft Documenten
description: Meer informatie over het toewijzen van Azure AD-rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/07/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5048cefaae10cd55091dd72f0b73a3cf9d731a35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253271"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Azure AD-rollen toewijzen in Privileged Identity Management

Met Azure Active Directory (Azure AD) kan een globale beheerder **permanente** ab-beheertoewijzingen voor Azure-beheerders maken. Deze roltoewijzingen kunnen worden gemaakt met behulp van de [Azure-portal](../users-groups-roles/directory-assign-admin-roles.md) of met [PowerShell-opdrachten](/powershell/module/azuread#directory_roles).

Met de PIM-service (Azure AD Privileged Identity Management) kunnen beheerders van bevoorrechte rollen ook permanente beheerroltoewijzingen uitvoeren. Bovendien kunnen beheerders van bevoorrechte rollen ervoor zorgen dat gebruikers **in aanmerking komen** voor Azure AD-beheerdersrollen. Een in aanmerking komende beheerder kan de rol activeren wanneer deze nodig is en vervolgens verlopen de machtigingen zodra deze zijn uitgevoerd.

## <a name="determine-your-version-of-pim"></a>Bepaal uw versie van PIM

Vanaf november 2019 wordt het gedeelte Azure AD-rollen van Privileged Identity Management bijgewerkt naar een nieuwe versie die overeenkomt met de ervaringen voor Azure-bronrollen. Dit zorgt voor extra functies en [wijzigingen in de bestaande API.](azure-ad-roles-features.md#api-changes) Terwijl de nieuwe versie wordt uitgerold, zijn welke procedures u in dit artikel volgt, afhankelijk van de versie van Privileged Identity Management die u momenteel hebt. Volg de stappen in deze sectie om te bepalen welke versie van Privileged Identity Management u hebt. Nadat u uw versie van Privileged Identity Management hebt weten, u de procedures in dit artikel selecteren die overeenkomen met die versie.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) met een gebruiker die zich in de rol [Van Privileged role administrator](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) bevindt.
1. Azure **AD Privileged Identity Management openen**. Als u boven aan de overzichtspagina een banner hebt staan, volgt u de instructies op het tabblad **Nieuwe versie** van dit artikel. Volg anders de instructies op het tabblad **Vorige versie.**

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nieuwe versie](#tab/new)

## <a name="assign-a-role"></a>Een rol toewijzen

Volg deze stappen om een gebruiker in aanmerking te laten komen voor een Azure AD-beheerrol.

1. Meld u aan bij [azure portal](https://portal.azure.com/) met een gebruiker die lid is van de rol privileged [role administrator.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

    Zie Toegang verlenen aan andere beheerders om privileged [identity management te beheren](pim-how-to-give-access-to-pim.md)voor informatie over het verlenen van toegang aan een andere beheerder.

1. Azure **AD Privileged Identity Management openen**.

1. Selecteer **Azure AD-rollen**.

1. Selecteer **Rollen** om de lijst met rollen voor Azure AD-machtigingen te bekijken.

    ![Azure AD-rollen](./media/pim-how-to-add-role-to-user/roles-list.png)

1. Selecteer **Lid toevoegen** om de pagina Nieuwe **toewijzing** te openen.

1. Selecteer **Selecteer een rol** selecteren om de pagina Een rol selecteren te openen.

    ![Nieuw toewijzingsvenster](./media/pim-how-to-add-role-to-user/select-role.png)

1. Selecteer een rol die u wilt toewijzen en klik op **Selecteren**.

1. Selecteer een lid aan wie u aan de rol wilt toewijzen en selecteer **vervolgens Selecteren**.

1. Selecteer In de lijst **Toewijzingstype** in het deelvenster **Lidmaatschapsinstellingen** de optie **In aanmerking komen** of **Actief**.

    - **In aanmerking komende** toewijzingen vereisen dat het lid van de rol een actie uitvoert om de rol te gebruiken. Acties kunnen bijvoorbeeld een meervoudige verificatiecontrole, het opgeven van een zakelijke reden of het vragen om toestemming bij aangewezen fiatteurs zijn.

    - **Actieve** toewijzingen vereisen niet dat het lid actie uitvoert om de rol te gebruiken. Leden die als actief zijn toegewezen, hebben te allen tijde de bevoegdheden toegewezen aan de rol.

1. Als de toewijzing permanent moet zijn (permanent in aanmerking komend of permanent toegewezen), schakelt u het selectievakje **Permanent** in.

    Afhankelijk van de rolinstellingen wordt het selectievakje mogelijk niet of niet-aanpasbaar weergegeven.

1. Als u een specifieke toewijzingsduur wilt opgeven, schakelt u het selectievakje uit en wijzigt u de begin- en/of einddatum en -tijdvakken. Als u klaar bent, selecteert u **Gereed**.

    ![Lidmaatschapinstellingen - datum en tijd](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. Als u de nieuwe roltoewijzing wilt maken, selecteert u **Toevoegen**. Er wordt een melding van de status weergegeven.

    ![Nieuwe toewijzing - Melding](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Een bestaande roltoewijzing bijwerken of verwijderen

Volg deze stappen om een bestaande roltoewijzing bij te werken of te verwijderen.

1. Azure **AD Privileged Identity Management openen**.

1. Selecteer **Azure AD-rollen**.

1. Selecteer **Rollen** om de lijst met rollen voor Azure AD te bekijken.

1. Selecteer de rol die u wilt bijwerken of verwijderen.

1. Zoek de roltoewijzing op de tabbladen **In aanmerking komende rollen** of Actieve **rollen.**

    ![Roltoewijzing bijwerken of verwijderen](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. Selecteer **Bijwerken** of **Verwijderen** om de roltoewijzing bij te werken of te verwijderen.

# <a name="previous-version"></a>[Vorige versie](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>Een gebruiker in aanmerking laten komen voor een rol

Volg deze stappen om een gebruiker in aanmerking te laten komen voor een Azure AD-beheerrol.

1. Selecteer **Rollen** of **leden**.

    ![Azure AD-rollen](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Selecteer **Lid toevoegen** om **beheerde leden toevoegen te openen**.

1. Selecteer **Selecteer Een rol**selecteren, selecteer een rol die u wilt beheren en selecteer Selecteer vervolgens **Selecteren**.

    ![Een rol selecteren](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Selecteer **Leden selecteren,** selecteer de gebruikers die u aan de rol wilt toewijzen en selecteer **Selecteer vervolgens Selecteren**.

    ![Een rol selecteren](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Selecteer **OK** in **Beheerde leden**toevoegen om de gebruiker aan de rol toe te voegen.

1. Selecteer in de lijst met rollen de rol die u zojuist hebt toegewezen om de lijst met leden weer te geven.

     Wanneer de rol is toegewezen, wordt de geselecteerde gebruiker weergegeven in de ledenlijst als **In aanmerking komen** voor de rol.

    ![Gebruiker die in aanmerking komt voor een rol](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Nu de gebruiker in aanmerking komt voor de rol, laat u hem weten dat deze deze kan activeren volgens de instructies in [Mijn Azure AD-rollen activeren in Privileged Identity Management.](pim-how-to-activate-role.md)

    In aanmerking komende beheerders wordt gevraagd zich tijdens de activering te registreren voor Azure Multi-Factor Authentication (MFA). Als een gebruiker zich niet kan registreren voor MFA @outlook.comof een Microsoft-account (zoals) gebruikt, moet u deze permanent maken in al zijn rollen.

## <a name="make-a-role-assignment-permanent"></a>Maak een roltoewijzing permanent

Nieuwe gebruikers komen standaard alleen *in aanmerking* voor een Azure AD-beheerrol. Volg deze stappen als u een roltoewijzing permanent wilt maken.

1. Azure **AD Privileged Identity Management openen**.

1. Selecteer **Azure AD-rollen**.

1. Selecteer **Leden**.

    ![Lijst van leden](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selecteer een **in aanmerking komende** rol die u permanent wilt maken.

1. Selecteer **Meer** en selecteer **Vervolgens Permanent maken**.

    ![Roltoewijzing permanent maken](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    De rol wordt nu vermeld als **permanent**.

    ![Lijst van leden met permanente wijziging](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Een gebruiker uit een rol verwijderen

U gebruikers verwijderen uit roltoewijzingen, maar zorg ervoor dat er altijd ten minste één gebruiker is die een permanente globale beheerder is. Als u niet zeker weet welke gebruikers hun roltoewijzingen nog nodig hebben, u [een toegangscontrole voor de rol starten.](pim-how-to-start-security-review.md)

Volg deze stappen om een specifieke gebruiker uit een Azure AD-beheerrol te verwijderen.

1. Azure **AD Privileged Identity Management openen**.

1. Selecteer **Azure AD-rollen**.

1. Selecteer **Leden**.

    ![Lijst van leden](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selecteer een roltoewijzing die u wilt verwijderen.

1. Selecteer **Meer** en selecteer **Verwijderen**.

    ![Een rol verwijderen](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Selecteer **Ja**in het bericht dat u vraagt te bevestigen.

    ![Een rol verwijderen](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    De roltoewijzing wordt verwijderd.

## <a name="authorization-error-when-assigning-roles"></a>Autorisatiefout bij het toewijzen van rollen

Als u onlangs Privileged Identity Management voor een abonnement hebt ingeschakeld en u een autorisatiefout krijgt wanneer u een gebruiker in aanmerking probeert te laten komen voor een Azure AD-beheerrol, kan dit zijn omdat de MS-PIM-serviceprincipal nog niet over de juiste machtigingen beschikt. De MS-PIM-serviceprincipal moet de functie [Gebruikerstoegangsbeheerder](../../role-based-access-control/built-in-roles.md#user-access-administrator) hebben om rollen aan anderen toe te wijzen. In plaats van te wachten tot MS-PIM de functie Gebruikerstoegangsbeheerder toegewezen krijgt, u deze handmatig toewijzen.

Volg deze stappen om de functie Gebruikerstoegangsbeheerder toe te wijzen aan de MS-PIM-serviceprincipal voor een abonnement.

1. Meld u aan bij de Azure-portal als globale beheerder.

1. Kies **Alle services** en vervolgens **Abonnementen**.

1. Kies uw abonnement.

1. Kies **Toegangsbeheer (IAM)**.

1. Kies **Roltoewijzingen** om de huidige lijst met roltoewijzingen in het abonnementsbereik weer te geven.

   ![IAM-blade (Access Control) voor een abonnement](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Controleer of de **MS-PIM-serviceprincipal** de functie **Gebruikerstoegangsbeheerder** heeft toegewezen.

1. Als dit niet het gevolg is, kiest u **Roltoewijzing toevoegen** om het deelvenster **Roltoewijzing toevoegen** te openen.

1. Selecteer **in** de vervolgkeuzelijst Rol de functie **Beheerder van gebruikerstoegang.**

1. Zoek en selecteer in de lijst **Selecteren** de **MS-PIM-serviceprincipal.**

   ![Roltoewijzingsvenster toevoegen - Machtigingen toevoegen voor MS-PIM-serviceprincipal](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Kies **Opslaan** om de rol toe te wijzen.

   Na enkele ogenblikken krijgt de MS-PIM-serviceprincipal de functie Gebruikerstoegangsbeheerder toegewezen op het abonnementsbereik.

   ![Pagina toegangsbeheer met functietoewijzing voor gebruikerstoegangsbeheerders voor de ms-PIM-serviceprincipal](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>Volgende stappen

- [Instellingen voor Azure AD-beheerfunctie configureren in Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Azure-bronrollen toewijzen in Privileged Identity Management](pim-resource-roles-assign-roles.md)