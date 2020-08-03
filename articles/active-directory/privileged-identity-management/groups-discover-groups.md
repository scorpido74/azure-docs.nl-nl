---
title: Functie-toewijs bare groepen detecteren om te beheren in PIM-Azure AD | Microsoft Docs
description: Meer informatie over het detecteren van door rollen toewijs bare groepen die u kunt beheren als geprivilegieerde toegangs groepen in Privileged Identity Management (PIM).
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
ms.date: 07/27/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b66a41f0f1aa48433dac77fca56d5e2877989a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506002"
---
# <a name="discover-privileged-access-groups-preview-to-manage-in-privileged-identity-management"></a>Geprivilegieerde toegangs groepen (preview) detecteren om te beheren in Privileged Identity Management

In Azure Active Directory (Azure AD) kunt u ingebouwde rollen van Azure AD toewijzen aan Cloud groepen om het beheer van roltoewijzingen te vereenvoudigen. U kunt Privileged Identity Management (PIM) nu gebruiken om de geschiktheid voor lidmaatschap of eigendom van deze groepen toe te wijzen, om Azure AD-rollen te beveiligen en de toegang te beveiligen. Voordat u een door de functie toewijs bare groep van Azure AD kunt beheren als een bevoegde toegangs groep in Privileged Identity Management, moet u deze ontdekken en onder beheer brengen in PIM.

## <a name="discover-resources"></a>Resources ontdekken

1. [Meld u aan bij Azure AD](https://aad.portal.azure.com) met machtigingen voor de rol Administrator voor geprivilegieerd.
1. Een door een functie toewijs bare groep maken in azure AD. U moet een eigenaar van de groep zijn om deze te detecteren en te beheren met Privileged Identity Management.
1. Open **privileged Identity Management**.
1. Selecteer **bevoorrechte toegang (preview)**.

    ![De opdracht groepen detecteren voor de eerste keer-ervaring](./media/groups-discover-groups/groups-discover-groups.png)

1. Selecteer **groepen detecteren**.
1. Zoek op groeps naam.
1. Selecteer uw groep en selecteer **groepen beheren** om deze onder PIM-beheer te brengen.

    ![Groepen detecteren zonder resources die worden weer gegeven voor de eerste keer-ervaring](./media/groups-discover-groups/groups-bring-under-management.png)

    > [!NOTE]
    > Zodra een privileged Access-groep wordt beheerd, kan deze niet buiten het beheer worden gehaald. Zo voor komt u dat een andere resource beheerder Privileged Identity Management-instellingen kan verwijderen.

1. Als er een bericht wordt weer gegeven waarin u de onboarding van de geselecteerde resource voor beheer bevestigt, selecteert u **Ja**.

## <a name="next-steps"></a>Volgende stappen

- [De toewijzingen van groepen met uitgebreide toegang configureren in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Geprivilegieerde toegangs groepen toewijzen in Privileged Identity Management](pim-resource-roles-assign-roles.md)
