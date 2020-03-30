---
title: Aanvragen voor Azure-bronrollen goedkeuren in PIM - Azure AD | Microsoft Documenten
description: Meer informatie over het goedkeuren of weigeren van aanvragen voor Azure-bronrollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e779f633efccf7b594c193e165a584d22b1d653b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021976"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>Aanvragen voor Azure-bronrollen in Privileged Identity Management goedkeuren of weigeren

Met Privileged Identity Management (PIM) in Azure Active Directory (Azure AD) u rollen configureren om goedkeuring voor activering nodig te hebben en gebruikers of groepen uit uw Azure AD-organisatie kiezen als gedelegeerde fiatteurs. We raden u aan twee of meer fiatteurs voor elke rol te selecteren om de werkbelasting voor de bevoegde rolbeheerder te verminderen. Gedelegeerde fiatteurs hebben 24 uur de tijd om aanvragen goed te keuren. Als een aanvraag niet binnen 24 uur wordt goedgekeurd, moet de in aanmerking komende gebruiker opnieuw een nieuw verzoek indienen. Het tijdvenster voor 24 uur goedkeuring is niet configureerbaar.

Volg de stappen in dit artikel om aanvragen voor Azure-bronrollen goed te keuren of te weigeren.

## <a name="view-pending-requests"></a>Aanvragen in behandeling weergeven

Als gedelegeerd goedkeurder ontvangt u een e-mailmelding wanneer een Azure-bronrolaanvraag in afwachting van uw goedkeuring is. U deze aanvragen in behandeling bekijken in Privileged Identity Management.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Azure **AD Privileged Identity Management openen**.

1. Selecteer **Aanvragen goedkeuren**.

    ![Aanvragen goedkeuren - pagina met Azure-bronnen met verzoek om te controleren](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    In de sectie **Aanvragen voor functieactiveringen** ziet u een lijst met aanvragen in afwachting van uw goedkeuring.

## <a name="approve-requests"></a>Aanvragen goedkeuren

1. Zoek en selecteer het verzoek dat u wilt goedkeuren. Er wordt een pagina goedkeuren of weigeren weergegeven.

    ![Aanvragen goedkeuren - deelvenster goedkeuren of weigeren met details en vak Verantwoording](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Voer in het vak **Rechtvaardiging** de zakelijke motivering in.

1. Selecteer **Goedkeuren**. U ontvangt een Azure-melding van uw goedkeuring.

    ![Goedkeuren van kennisgeving waarin het verzoek wordt goedgekeurd](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

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
- Resourcebeheerders worden op de hoogte gesteld wanneer een goedgekeurde gebruiker actief wordt in hun rol.

>[!Note]
>Een resourcebeheerder die van mening is dat een goedgekeurde gebruiker niet actief mag zijn, kan de actieve roltoewijzing in Privileged Identity Management verwijderen. Hoewel resourcebeheerders niet op de hoogte worden gesteld van in behandeling zijnde aanvragen, tenzij ze een fiatteur zijn, kunnen ze aanvragen in behandeling voor alle gebruikers bekijken en annuleren door in behandeling zijnde aanvragen weer te geven in Privileged Identity Management.

## <a name="next-steps"></a>Volgende stappen

- [Azure-bronrollen uitbreiden of vernieuwen in Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [E-mailmeldingen in privileged identity management](pim-email-notifications.md)
- [Aanvragen voor Azure AD-rollen in Privileged Identity Management goedkeuren of weigeren](azure-ad-pim-approval-workflow.md)
