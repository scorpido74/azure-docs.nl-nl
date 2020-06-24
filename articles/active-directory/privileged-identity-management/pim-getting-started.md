---
title: Beginnen met het gebruik van PIM-Azure Active Directory | Microsoft Docs
description: Meer informatie over het inschakelen en aan de slag met Azure AD Privileged Identity Management (PIM) in de Azure Portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: how-to
ms.workload: identity
ms.date: 04/23/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: cad6c41b465e14b11d4fa7b8e7fa6037d49e8eae
ms.sourcegitcommit: 24f31287b6a526e23ff5b5469113522d1ccd4467
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84742906"
---
# <a name="start-using-privileged-identity-management"></a>Aan de slag met Privileged Identity Management

In dit artikel wordt beschreven hoe u Privileged Identity Management (PIM) inschakelt en aan de slag gaat met het gebruik ervan.

Gebruik Privileged Identity Management (PIM) om toegang te beheren, te controleren en te controleren binnen uw Azure Active Directory Azure AD-organisatie. Met PIM kunt u zo nodig en just-in-time toegang bieden tot Azure-resources, Azure AD-resources en andere micro soft-onlineservices, zoals Office 365 of Microsoft Intune.

## <a name="prerequisites"></a>Vereisten

Als u Privileged Identity Management wilt gebruiken, hebt u een van de volgende licenties nodig:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Zie [licentie vereisten voor het gebruik van privileged Identity Management](subscription-requirements.md)voor meer informatie.

## <a name="prepare-pim-for-azure-ad-roles"></a>PIM voorbereiden voor Azure AD-rollen

Zodra u Privileged Identity Management hebt ingeschakeld voor uw directory, kunt u Privileged Identity Management voorbereiden om Azure AD-rollen te beheren.

Hier volgen de taken die wij u aanraden voor het voorbereiden van Azure AD-rollen, in volg orde:

1. [Instellingen voor Azure AD-functies configureren](pim-how-to-change-default-settings.md).
1. [Geef in aanmerking komende toewijzingen](pim-how-to-add-role-to-user.md)op.
1. [Sta gebruikers in staat om hun Azure AD-rol just-in-time te activeren](pim-how-to-activate-role.md).

## <a name="prepare-pim-for-azure-roles"></a>PIM voorbereiden voor Azure-rollen

Zodra u Privileged Identity Management hebt ingeschakeld voor uw directory, kunt u Privileged Identity Management voorbereiden voor het beheren van Azure-rollen voor toegang tot Azure-resources voor een abonnement.

Hier volgen de taken die wij u aanraden om Azure-rollen voor te bereiden, in de volgende volg orde:

1. [Azure-resources detecteren](pim-resource-roles-discover-resources.md)
1. [Instellingen voor Azure-functies configureren](pim-resource-roles-configure-role-settings.md).
1. [Geef in aanmerking komende toewijzingen](pim-resource-roles-assign-roles.md)op.
1. [Sta in aanmerking komende gebruikers toe hun Azure-rollen just-in-time te activeren](pim-resource-roles-activate-your-roles.md).

## <a name="navigate-to-your-tasks"></a>Navigeer naar uw taken

Zodra Privileged Identity Management is ingesteld, kunt u uw weg ontdekken.

![Navigatie venster in Privileged Identity Management taken weer geven en opties beheren](./media/pim-getting-started/pim-quickstart-tasks.png)

| Taak + beheren | Beschrijving |
| --- | --- |
| **Mijn rollen**  | Geeft een lijst weer met in aanmerking komende en actieve rollen die aan u zijn toegewezen. Hier kunt u alle in aanmerking komende toegewezen rollen activeren. |
| **Mijn aanvragen** | Hiermee worden de in behandeling zijnde aanvragen voor het activeren van in aanmerking komende roltoewijzingen weer gegeven. |
| **Aanvragen goedkeuren** | Geeft een lijst weer van aanvragen voor het activeren van in aanmerking komende rollen door gebruikers in uw directory die u hebt ingesteld om goed te keuren. |
| **Toegang beoordelen** | Een lijst met actieve toegangs beoordelingen die u hebt toegewezen aan volt ooien, of u nu de toegang bekijkt voor uzelf of iemand anders. |
| **Azure AD-rollen** | Geeft een dash board en instellingen weer voor bevoegde beheerdersrol voor het beheren van toewijzingen van Azure AD-rollen. Dit dashboard is uitgeschakeld voor iedereen die geen beheerder met een bevoorrechte rol is. Deze gebruikers hebben toegang tot een speciaal dashboard met de titel Mijn weergave. In het dash board van mijn weer gave wordt alleen informatie weer gegeven over de gebruiker die toegang heeft tot het dash board, niet de hele organisatie. |
| **Azure-resources** | Geeft een dash board en instellingen weer voor beheerders met verhoogde rollen voor het beheren van toewijzingen van Azure-resource rollen. Dit dashboard is uitgeschakeld voor iedereen die geen beheerder met een bevoorrechte rol is. Deze gebruikers hebben toegang tot een speciaal dashboard met de titel Mijn weergave. In het dash board van mijn weer gave wordt alleen informatie weer gegeven over de gebruiker die toegang heeft tot het dash board, niet de hele organisatie. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Een PIM-tegel toevoegen aan het dash board

Voeg een PIM-tegel toe aan uw Azure Portal dash board om het openen van Privileged Identity Management gemakkelijker te maken.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Selecteer **alle services** en zoek de **Azure AD privileged Identity Management** -service.

    ![Azure AD Privileged Identity Management in alle services](./media/pim-getting-started/pim-all-services-find.png)

1. Selecteer de Privileged Identity Management Snelstartgids.

1. Schakel de **Blade vastmaken aan dash board** in om de blade privileged Identity Management Snelstartgids aan het dash board vast te maken.

    ![Pictogram punaise om Privileged Identity Management Blade aan het dash board vast te maken](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    In het Azure-dash board ziet u een tegel zoals deze:

    ![Tegel Privileged Identity Management Snelstartgids op het dash board](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-rollen toewijzen in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Toegang tot Azure-resources beheren in Privileged Identity Management](pim-resource-roles-discover-resources.md)
