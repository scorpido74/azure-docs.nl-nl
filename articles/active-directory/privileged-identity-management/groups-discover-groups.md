---
title: Functie-toewijs bare groepen beheren als geprivilegieerde toegangs groepen-Azure AD | Microsoft Docs
description: Toestemming voor onboardable-toewijs bare groepen om te beheren als geprivilegieerde toegangs groepen in Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 08/17/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: a707c66f2562fe442c58ce6292e51c4e67a20ae2
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869525"
---
# <a name="manage-privileged-access-groups-preview-in-privileged-identity-management"></a>Geprivilegieerde toegangs groepen (preview) beheren in Privileged Identity Management

In Azure Active Directory (Azure AD) kunt u ingebouwde rollen van Azure AD toewijzen aan Cloud groepen om te vereenvoudigen hoe u roltoewijzingen beheert. Als u Azure AD-rollen wilt beveiligen en de toegang wilt beveiligen, kunt u Privileged Identity Management (PIM) nu gebruiken voor het beheren van just-in-time-toegang voor leden of eigen aren van deze groepen. Voor het beheren van een functie-toewijs bare groep voor Azure AD als een bevoegde toegangs groep in Privileged Identity Management, moet u deze onder beheer in PIM plaatsen.

## <a name="identify-groups-to-manage"></a>Te beheren groepen identificeren

U kunt een door een functie toewijs bare groep maken in azure AD, zoals wordt beschreven in [een functie groep maken die kan worden toegewezen in azure Active Directory](../users-groups-roles/roles-groups-create-eligible.md). U bent een eigenaar van de groep om deze onder beheer te brengen in Privileged Identity Management.

1. [Meld u aan bij Azure AD](https://aad.portal.azure.com) met privileged Role Administrator Permissions.
1. Selecteer **groepen** en selecteer vervolgens de groep die u wilt beheren. U kunt de lijst zoeken of filteren.

    ![een door een functie toewijs bare groep zoeken om te beheren in PIM](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. Open de groep en selecteer **bevoorrechte toegang (preview)**.

    ![De Privileged Identity Management-ervaring openen](./media/groups-discover-groups/groups-discover-groups.png)

1. Als uw groepen nog niet onder beheer zijn gebracht in PIM, selecteert u **bevoorrechte toegang inschakelen** om toestemming te geven voor beheer. Alleen de globale beheerder of eigenaar van een groep kan deze toestemming geven.

    ![toestemming voor het beheren van de groep in Privileged Identity Management indien nodig](./media/groups-discover-groups/consent-page.png)

1. Begin met het beheren van toewijzingen in PIM.

    ![Toewijzingen in Privileged Identity Management beheren](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> Zodra een privileged Access-groep wordt beheerd, kan deze niet buiten het beheer worden gehaald. Hiermee voor komt u dat een andere beheerder Privileged Identity Management-instellingen kan verwijderen.

## <a name="next-steps"></a>Volgende stappen

- [De toewijzingen van groepen met uitgebreide toegang configureren in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Geprivilegieerde toegangs groepen toewijzen in Privileged Identity Management](pim-resource-roles-assign-roles.md)
