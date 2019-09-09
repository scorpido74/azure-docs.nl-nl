---
title: Azure-resources ontdekken om te beheren in PIM-Azure Active Directory | Microsoft Docs
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
ms.date: 04/09/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef1f1712ab7474747829c8744a39e74b78777668
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804163"
---
# <a name="discover-azure-resources-to-manage-in-pim"></a>Azure-resources ontdekken voor beheer in PIM

Met Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kunt u de beveiliging van uw Azure-resources verbeteren. Dit is handig voor organisaties die al gebruikmaken van PIM voor het beveiligen van Azure AD-rollen, en voor beheer groep-en abonnements eigenaren die productie resources willen beveiligen.

Wanneer u voor het eerst PIM voor Azure-resources hebt ingesteld, moet u de resources die u wilt beveiligen met PIM, detecteren en selecteren. Er is geen limiet voor het aantal resources dat u met PIM kunt beheren. We raden u echter aan om te beginnen met uw meest kritieke resources (productie).

## <a name="discover-resources"></a>Services detecteren

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Open **Azure AD privileged Identity Management**.

1. Klik op **Azure-resources**.

    Als dit de eerste keer is dat u PIM voor Azure-resources gebruikt, ziet u een deel venster Resources ontdekken.

    ![Het deel venster Resources detecteren zonder resources die worden weer gegeven voor de eerste keer-ervaring](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Als er al een andere resource of Directory-beheerder in uw organisatie Azure-resources in PIM beheert, ziet u een lijst met de resources die momenteel worden beheerd.

    ![Het deel venster Resources detecteren bronnen weer geven die momenteel worden beheerd](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Klik op **bronnen detecteren** om de detectie ervaring te starten.

    ![In het deel venster zoeken worden bronnen weer gegeven die kunnen worden beheerd, zoals abonnementen en beheer groepen](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. Gebruik in het deel venster detectie de **resource status filter** en **Selecteer resource type** om de beheer groepen of abonnementen te filteren waarvoor u schrijf machtigingen hebt. Het is waarschijnlijk het gemakkelijkst om eerst **Alles** te beginnen.

    U kunt alleen zoeken naar en selecteren van beheer groep of abonnements bronnen om te beheren met behulp van PIM. Wanneer u een beheer groep of een abonnement in PIM beheert, kunt u ook de onderliggende resources beheren.

1. Voeg een vinkje toe naast alle niet-beheerde resources die u wilt beheren.

1. Klik op **resource beheren** om te beginnen met het beheren van de geselecteerde resources.

    > [!NOTE]
    > Zodra een beheer groep of abonnement is ingesteld op beheerd, kan het niet onbeheerd worden. Zo voor komt u dat een andere resource beheerder PIM-instellingen kan verwijderen.

    ![Paneel voor detectie waarvoor een resource is geselecteerd en de optie resource beheren gemarkeerd](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Als er een bericht wordt weer gegeven waarin u de onboarding van de geselecteerde resource voor beheer kunt bevestigen, klikt u op **Ja**.

    ![Bericht dat er wordt bevestigd dat de geselecteerde resources voor beheer worden vrijgegeven](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Volgende stappen

- [Instellingen voor Azure-resource rollen configureren in PIM](pim-resource-roles-configure-role-settings.md)
- [Azure-resource rollen toewijzen in PIM](pim-resource-roles-assign-roles.md)
