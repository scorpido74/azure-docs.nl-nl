---
title: Azure-resources ontdekken om te beheren in Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Meer informatie over hoe u Azure-resources kunt detecteren om te beheren in Azure AD Privileged Identity Management (PIM).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7e401426e1c9448caaae28648abce481f426d38
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895691"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>Azure-resources ontdekken om te beheren in Privileged Identity Management

Met Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kunt u de beveiliging van uw Azure-resources verbeteren. Dit is nuttig voor organisaties die al gebruikmaken van Privileged Identity Management voor het beveiligen van Azure AD-rollen, en voor de beheer groep en de eigenaar van het abonnement die productie resources willen beveiligen.

Wanneer u Privileged Identity Management voor het eerst instelt voor Azure-resources, moet u de resources ontdekken en selecteren die u wilt beveiligen met Privileged Identity Management. Er is geen limiet voor het aantal resources dat u kunt beheren met Privileged Identity Management. We raden u echter aan om te beginnen met uw meest kritieke resources (productie).

## <a name="discover-resources"></a>Bronnen detecteren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **Azure-resources**.

    Als dit de eerste keer is dat u Privileged Identity Management voor Azure-resources gebruikt, ziet u een pagina **bronnen detecteren** .

    ![Het deel venster Resources detecteren zonder resources die worden weer gegeven voor de eerste keer-ervaring](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Als een andere beheerder in uw organisatie al Azure-resources in Privileged Identity Management beheert, ziet u een lijst met de resources die momenteel worden beheerd.

    ![Het deel venster Resources detecteren bronnen weer geven die momenteel worden beheerd](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Selecteer **bronnen detecteren** om de detectie-ervaring te starten.

    ![In het deel venster zoeken worden bronnen weer gegeven die kunnen worden beheerd, zoals abonnementen en beheer groepen](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. Gebruik op de pagina **detectie** de **resource status filter** en **Selecteer resource type** om de beheer groepen of abonnementen te filteren waarvoor u schrijf machtigingen hebt. Het is waarschijnlijk het gemakkelijkst om eerst **Alles** te beginnen.

    U kunt alleen zoeken naar en selecteren van beheer groep of abonnements bronnen om te beheren met Privileged Identity Management. Wanneer u een beheer groep of een abonnement beheert in Privileged Identity Management, kunt u ook de bijbehorende onderliggende resources beheren.

1. Schakel het selectie vakje in naast de onbeheerde resources die u wilt beheren.

1. Selecteer **resource beheren** om te beginnen met het beheren van de geselecteerde resources.

    > [!NOTE]
    > Zodra een beheer groep of-abonnement wordt beheerd, kan deze niet onbeheerd worden. Zo voor komt u dat een andere resource beheerder Privileged Identity Management-instellingen kan verwijderen.

    ![Paneel voor detectie waarvoor een resource is geselecteerd en de optie resource beheren gemarkeerd](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Als er een bericht wordt weer gegeven waarin u de onboarding van de geselecteerde resource voor beheer bevestigt, selecteert u **Ja**.

    ![Bericht dat er wordt bevestigd dat de geselecteerde resources voor beheer worden vrijgegeven](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Volgende stappen

- [Instellingen voor Azure-resource-rollen configureren in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Azure-resource rollen toewijzen in Privileged Identity Management](pim-resource-roles-assign-roles.md)
