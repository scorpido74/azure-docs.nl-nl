---
title: Azure-bronnen ontdekken die moeten worden beheren in PIM - Azure AD | Microsoft Documenten
description: Meer informatie over het ontdekken van Azure-resources die u beheren in Azure AD Privileged Identity Management (PIM).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87da43100c7494937ddc842e0f903ba3a360959e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022897"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>Azure-bronnen ontdekken die moeten worden beheerd in Privileged Identity Management

Met Azure Active Directory (Azure AD) Privileged Identity Management (PIM) u de beveiliging van uw Azure-resources verbeteren. Dit is handig voor organisaties die al Privileged Identity Management gebruiken om Azure AD-rollen te beschermen, en voor beheergroep- en abonnementseigenaren die productieresources willen beveiligen.

Wanneer u voor het eerst Privileged Identity Management instelt voor Azure-resources, moet u de resources ontdekken en selecteren die u wilt beveiligen met Privileged Identity Management. Er is geen limiet aan het aantal resources dat u beheren met Privileged Identity Management. We raden u echter aan om te beginnen met uw meest kritieke (productie)middelen.

## <a name="discover-resources"></a>Resources doorzoeken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Azure **AD Privileged Identity Management openen**.

1. Selecteer **Azure-resources**.

    Als dit de eerste keer is dat u Privileged Identity Management voor Azure-bronnen gebruikt, ziet u een pagina **Resources ontdekken.**

    ![Deelvenster Resources ontdekken zonder resources die worden vermeld voor de eerste ervaring](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Als een andere beheerder in uw organisatie azure-resources al beheert in Privileged Identity Management, ziet u een lijst met de resources die momenteel worden beheerd.

    ![Bronnenvenster met bronnen zoeken met bronnen die momenteel worden beheerd](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Selecteer **Resources ontdekken** om de detectie-ervaring te starten.

    ![Detectievenster met bronnen die kunnen worden beheerd, zoals abonnementen en beheergroepen](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. Gebruik op de pagina **Detectie** **het filter resourcestatus** en **Resourcetype selecteren** om de beheergroepen of abonnementen te filteren waartoe u schrijftoestemming hebt gegeven. Het is waarschijnlijk het gemakkelijkst om te beginnen met **Alle** in eerste instantie.

    U alleen zoeken naar beheergroep- of abonnementsbronnen die u wilt beheren met behulp van Privileged Identity Management. Wanneer u een beheergroep of een abonnement beheert in Privileged Identity Management, u ook de onderliggende bronnen beheren.

1. Schakel het selectievakje in naast onbeheerde resources die u wilt beheren.

1. Selecteer **Resources beheren** om de geselecteerde resources te beheren.

    > [!NOTE]
    > Zodra een beheergroep of abonnement is beheerd, kan deze niet meer worden beheerd. Dit voorkomt dat een andere resourcebeheerder instellingen voor bevoegde identiteitsbeheer verwijdert.

    ![Deelvenster Detectie met een resource geselecteerd en de optie Resource beheren gemarkeerd](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Als u een bericht ziet om de onboarding van de geselecteerde resource voor beheer te bevestigen, selecteert u **Ja**.

    ![Bericht dat bevestigt aan boord van de geselecteerde bronnen voor beheer](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure-bronrolinstellingen configureren in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Azure-bronrollen toewijzen in Privileged Identity Management](pim-resource-roles-assign-roles.md)
