---
title: Aanvragen voor Azure AD-rollen in PIM-Azure Active Directory goed keuren of weigeren | Microsoft Docs
description: Meer informatie over het goed keuren of weigeren van aanvragen voor Azure AD-rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3245f7343a48d3e54795c14dcb23b836c8d9d988
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756430"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Aanvragen voor Azure AD-rollen in Privileged Identity Management goed keuren of weigeren

Met Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kunt u rollen configureren voor het vereisen van goed keuring voor activering en een of meer gebruikers of groepen kiezen als gedelegeerde goed keurders. Gedelegeerde goed keurders hebben 24 uur nodig om aanvragen goed te keuren. Als een aanvraag niet binnen 24 uur wordt goedgekeurd, moet de in aanmerking komende gebruiker een nieuwe aanvraag opnieuw indienen. Het venster goedkeurings tijd 24 uur kan niet worden geconfigureerd.

Volg de stappen in dit artikel voor het goed keuren of weigeren van aanvragen voor Azure AD-rollen.

## <a name="view-pending-requests"></a>In behandeling zijnde aanvragen weer geven

Als gedelegeerde fiatteur ontvangt u een e-mail melding wanneer een Azure AD-toepassingsrol in afwachting is van uw goed keuring. U kunt deze aanvragen in behandeling bekijken in Privileged Identity Management.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Open **Azure AD privileged Identity Management**.

1. Klik op **Azure AD-rollen**.

1. Klik op **aanvragen goed keuren**.

    ![Azure AD-rollen: aanvragen goed keuren](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    U ziet een lijst met aanvragen die in afwachting zijn van uw goed keuring.

## <a name="approve-requests"></a>Aanvragen goedkeuren

1. Selecteer de aanvragen die u wilt goed keuren en klik vervolgens op **goed keuren** om het deel venster geselecteerde aanvragen goed keuren te openen.

    ![Lijst met goedgekeurde aanvragen met goedkeurings optie gemarkeerd](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. Typ een reden in het vak **reden goed keuring** .

    ![Deel venster geselecteerde aanvragen goed keuren met een goedkeurings reden](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Klik op **goed keuren**.

    Het status symbool wordt bijgewerkt met uw goed keuring.

    ![Deel venster geselecteerde aanvragen goed keuren na goed keuren op knop](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Aanvragen weigeren

1. Selecteer de aanvragen die u wilt weigeren en klik vervolgens op **weigeren** om het deel venster geselecteerde aanvragen weigeren te openen.

    ![Lijst met aanvragen goed keuren met de optie weigeren gemarkeerd](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Typ een reden in het vak **reden van weigering** .

    ![Deel venster geselecteerde aanvragen weigeren met een reden voor weigeren](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Klik op **weigeren**.

    Het status symbool wordt bijgewerkt met de weigering.

## <a name="next-steps"></a>Volgende stappen

- [E-mail meldingen in Privileged Identity Management](pim-email-notifications.md)
- [Aanvragen voor Azure-resource rollen in Privileged Identity Management goed keuren of weigeren](pim-resource-roles-approval-workflow.md)
