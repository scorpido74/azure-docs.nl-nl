---
title: Aanvragen voor Azure-resource rollen in PIM-Azure Active Directory goed keuren of weigeren | Microsoft Docs
description: Meer informatie over het goed keuren of weigeren van aanvragen voor Azure-resource rollen in Azure AD Privileged Identity Management (PIM).
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
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b39434f8763e44a126f74ac9a19596e4413ae9c
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804267"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>Aanvragen voor Azure-resource rollen in PIM goed keuren of weigeren

Met Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kunt u rollen configureren voor het vereisen van goed keuring voor activering en een of meer gebruikers of groepen kiezen als gedelegeerde goed keurders. Gedelegeerde goed keurders hebben 24 uur nodig om aanvragen goed te keuren. Als een aanvraag niet binnen 24 uur wordt goedgekeurd, moet de in aanmerking komende gebruiker een nieuwe aanvraag opnieuw indienen. Het venster goedkeurings tijd 24 uur kan niet worden geconfigureerd.

Volg de stappen in dit artikel voor het goed keuren of weigeren van aanvragen voor Azure-resource rollen.

## <a name="view-pending-requests"></a>De aanvragen in de wachtrij weergeven

Als gedelegeerde fiatteur ontvangt u een e-mail melding wanneer een Azure resource Role-aanvraag in afwachting is van uw goed keuring. U kunt deze aanvragen in behandeling bekijken in PIM.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Open **Azure AD privileged Identity Management**.

1. Klik op **aanvragen goed keuren**.

    ![Aanvragen goed keuren-Azure-bronnen pagina waarin de aanvraag voor beoordeling wordt weer gegeven](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    In de sectie **aanvragen voor functie activeringen** ziet u een lijst met aanvragen die wachten op uw goed keuring.

## <a name="approve-requests"></a>Aanvragen goedkeuren

1. Zoek en klik op de aanvraag die u wilt goed keuren. Het deel venster goed keuren of weigeren wordt weer gegeven.

    ![Verzoeken goed keuren: deel venster goed keuren of weigeren met details en motivering](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Typ een reden in het vak **motivering** .

1. Klik op **goed keuren**.

    Er wordt een melding weer gegeven met uw goed keuring.

    ![Melding goed keuren met een aanvraag is goedgekeurd](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Aanvragen weigeren

1. Zoek en klik op de aanvraag die u wilt weigeren. Het deel venster goed keuren of weigeren wordt weer gegeven.

    ![Verzoeken goed keuren: deel venster goed keuren of weigeren met details en motivering](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Typ een reden in het vak **motivering** .

1. Klik op **weigeren**.

    Er wordt een melding weer gegeven met uw weigering.

## <a name="workflow-notifications"></a>Workflowmeldingen

Hier vindt u informatie over werk stroom meldingen:

- Alle leden van de lijst goed keurder worden per e-mail op de hoogte gesteld wanneer een aanvraag voor een rol in afwachting is van de beoordeling. E-mail meldingen bevatten een rechtstreekse koppeling naar de aanvraag, waarbij de goed keurder kan goed keuren of weigeren.
- Aanvragen worden omgezet door het eerste lid van de lijst die goed keuren of weigert.
- Wanneer een goed keurder reageert op de aanvraag, worden alle leden van de lijst goed keurder op de hoogte gesteld van de actie.
- Resource beheerders worden gewaarschuwd wanneer een goedgekeurd lid actief wordt in hun rol.

>[!Note]
>Een resource beheerder die meent dat een erkend lid niet actief mag zijn, kan de toewijzing van de actieve rol in PIM verwijderen. Hoewel resource beheerders geen meldingen ontvangen over aanvragen in behandeling, tenzij ze lid zijn van de lijst goed keurder, kunnen ze openstaande aanvragen van alle gebruikers weer geven en annuleren door in behandeling zijnde aanvragen in PIM te bekijken. 

## <a name="next-steps"></a>Volgende stappen

- [Azure-resource rollen in PIM uitbreiden of vernieuwen](pim-resource-roles-renew-extend.md)
- [E-mail meldingen in PIM](pim-email-notifications.md)
- [Aanvragen voor Azure AD-rollen in PIM goed keuren of weigeren](azure-ad-pim-approval-workflow.md)
