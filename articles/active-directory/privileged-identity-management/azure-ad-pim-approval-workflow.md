---
title: Aanvragen voor Azure AD-rollen goedkeuren of weigeren in PIM - Azure AD | Microsoft Documenten
description: Meer informatie over het goedkeuren of weigeren van aanvragen voor Azure AD-rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73ccfd1ad270072989e9b575fda538b94fd8927c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049018"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Aanvragen voor Azure AD-rollen in Privileged Identity Management goedkeuren of weigeren

Met Azure Active Directory (Azure AD) Privileged Identity Management (PIM) u rollen configureren om goedkeuring voor activering nodig te hebben en een of meerdere gebruikers of groepen als gedelegeerde fiatteurs kiezen. Gedelegeerde fiatteurs hebben 24 uur de tijd om aanvragen goed te keuren. Als een aanvraag niet binnen 24 uur wordt goedgekeurd, moet de in aanmerking komende gebruiker opnieuw een nieuw verzoek indienen. Het tijdvenster voor 24 uur goedkeuring is niet configureerbaar.

## <a name="determine-your-version-of-pim"></a>Bepaal uw versie van PIM

Vanaf november 2019 wordt het gedeelte Azure AD-rollen van Privileged Identity Management bijgewerkt naar een nieuwe versie die overeenkomt met de ervaringen voor Azure-rollen. Dit zorgt voor extra functies en [wijzigingen in de bestaande API.](azure-ad-roles-features.md#api-changes) Terwijl de nieuwe versie wordt uitgerold, zijn welke procedures u in dit artikel volgt, afhankelijk van de versie van Privileged Identity Management die u momenteel hebt. Volg de stappen in deze sectie om te bepalen welke versie van Privileged Identity Management u hebt. Nadat u uw versie van Privileged Identity Management hebt weten, u de procedures in dit artikel selecteren die overeenkomen met die versie.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) met een gebruiker die zich in de rol [Van Privileged role administrator](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) bevindt.
1. Azure **AD Privileged Identity Management openen**. Als u boven aan de overzichtspagina een banner hebt staan, volgt u de instructies op het tabblad **Nieuwe versie** van dit artikel. Volg anders de instructies op het tabblad **Vorige versie.**

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Volg de stappen in dit artikel om aanvragen voor Azure AD-rollen goed te keuren of te weigeren.

# <a name="new-version"></a>[Nieuwe versie](#tab/new)

## <a name="view-pending-requests"></a>Aanvragen in behandeling weergeven

Als gedelegeerd goedkeurder ontvangt u een e-mailmelding wanneer een Azure AD-rolaanvraag in afwachting van uw goedkeuring is. U deze aanvragen in behandeling bekijken in Privileged Identity Management.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Azure **AD Privileged Identity Management openen**.

1. Selecteer **Aanvragen goedkeuren**.

    ![Aanvragen goedkeuren - pagina met verzoek om Azure AD-rollen te controleren](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    In de sectie **Aanvragen voor functieactiveringen** ziet u een lijst met aanvragen in afwachting van uw goedkeuring.

## <a name="approve-requests"></a>Aanvragen goedkeuren

1. Zoek en selecteer het verzoek dat u wilt goedkeuren. Er wordt een pagina goedkeuren of weigeren weergegeven.

    ![Aanvragen goedkeuren - deelvenster goedkeuren of weigeren met details en vak Verantwoording](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. Voer in het vak **Rechtvaardiging** de zakelijke motivering in.

1. Selecteer **Goedkeuren**. U ontvangt een Azure-melding van uw goedkeuring.

    ![Goedkeuren van kennisgeving waarin het verzoek wordt goedgekeurd](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png))

## <a name="deny-requests"></a>Aanvragen weigeren

1. Zoek en selecteer het verzoek dat u wilt weigeren. Er wordt een pagina goedkeuren of weigeren weergegeven.

    ![Aanvragen goedkeuren - deelvenster goedkeuren of weigeren met details en vak Verantwoording](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Voer in het vak **Rechtvaardiging** de zakelijke motivering in.

1. Selecteer **Weigeren**. Er verschijnt een melding met uw weigering.

## <a name="workflow-notifications"></a>Werkstroommeldingen

Hier vindt u informatie over werkstroommeldingen:

- Fiatteurs worden per e-mail op de hoogte gebracht wanneer een verzoek om een rol in afwachting is van hun beoordeling. E-mailmeldingen bevatten een directe link naar het verzoek, waar de goedkeurder kan goedkeuren of weigeren.
- Aanvragen worden opgelost door de eerste goedkeurder die het goedkeurt of weigert.
- Wanneer een goedkeurder op het verzoek reageert, worden alle fiatteurs op de hoogte gebracht van de actie.
- Globale beheerders en bevoorrechte rolbeheerders worden op de hoogte gebracht wanneer een goedgekeurde gebruiker actief wordt in zijn rol.

>[!NOTE]
>Een globale beheerder of bevoorrechte rolbeheerder die van mening is dat een goedgekeurde gebruiker niet actief mag zijn, kan de actieve roltoewijzing in Privileged Identity Management verwijderen. Hoewel beheerders niet op de hoogte worden gesteld van in behandeling zijnde aanvragen, tenzij ze een fiatteur zijn, kunnen ze alle in behandeling zijnde aanvragen voor alle gebruikers bekijken en annuleren door in behandeling zijnde aanvragen in Privileged Identity Management weer te geven.

# <a name="previous-version"></a>[Vorige versie](#tab/previous)

## <a name="view-pending-requests"></a>Aanvragen in behandeling weergeven

Als gedelegeerd goedkeurder ontvangt u een e-mailmelding wanneer een Azure AD-rolaanvraag in afwachting van uw goedkeuring is. U deze aanvragen in behandeling bekijken in Privileged Identity Management.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Azure **AD Privileged Identity Management openen**.

1. Klik op **Azure AD-rollen**.

1. Klik **op Aanvragen goedkeuren**.

    ![Azure AD-rollen - Aanvragen goedkeuren](./media/azure-ad-pim-approval-workflow/approve-requests.png)

    U ziet een lijst met aanvragen in afwachting van uw goedkeuring.

## <a name="approve-requests"></a>Aanvragen goedkeuren

1. Selecteer de aanvragen die u wilt goedkeuren en klik op **Goedkeuren** om het deelvenster Geselecteerde aanvragen goedkeuren te openen.

    ![Lijst met aanvragen goedkeuren met optie Goedkeuren gemarkeerd](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. Typ een reden in het vak **Reden goedkeuren.**

    ![Deelvenster Geselecteerde aanvragen goedkeuren met een reden voor goedkeuren](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Klik **op Goedkeuren**.

    Het statussymbool wordt bijgewerkt met uw goedkeuring.

    ![Deelvenster Geselecteerde aanvragen goedkeuren nadat de knop Goedkeuren is ingedrukt](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Aanvragen weigeren

1. Selecteer de aanvragen die u wilt weigeren en klik vervolgens op **Weigeren** om het deelvenster Geselecteerde aanvragen weigeren te openen.

    ![Lijst met aanvragen goedkeuren met optie Weigeren gemarkeerd](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Typ in het vak **Reden weigeren** een reden.

    ![Deelvenster Geselecteerde aanvragen weigeren met een reden voor weigering](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Selecteer **Weigeren**.

    Het statussymbool wordt bijgewerkt met uw weigering.

---

## <a name="next-steps"></a>Volgende stappen

- [E-mailmeldingen in privileged identity management](pim-email-notifications.md)
- [Aanvragen voor Azure-bronrollen in Privileged Identity Management goedkeuren of weigeren](pim-resource-roles-approval-workflow.md)
