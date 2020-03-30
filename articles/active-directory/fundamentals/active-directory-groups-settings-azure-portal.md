---
title: Uw groepsgegevens bewerken - Azure Active Directory | Microsoft Documenten
description: Instructies voor het bewerken van de gegevens van uw groep met Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc06abca08b2522ac57552e85f7c1bac3ef854af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68561872"
---
# <a name="edit-your-group-information-using-azure-active-directory"></a>Uw groepsgegevens bewerken met Azure Active Directory

Met Azure Ad (Azure AD) u de instellingen van een groep bewerken, inclusief het bijwerken van de naam, beschrijving of het lidmaatschapstype.

## <a name="to-edit-your-group-settings"></a>Uw groepsinstellingen bewerken
1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met het account van een globale administrator voor de map.

2. Selecteer **Azure Active Directory**en selecteer **Groepen**.

    De pagina **Groepen - Alle groepen** wordt weergegeven met al uw actieve groepen.

3. Typ in de pagina **Groepen - Alle groepen** zoveel mogelijk van de groepsnaam in het vak **Zoeken.** Voor de toepassing van dit artikel, zijn we op zoek naar de **MDM beleid - West** groep.

    De zoekresultaten worden weergegeven onder het vak **Zoeken** en worden bijgewerkt terwijl u meer tekens typt.

    ![Pagina Alle groepen, met zoektekst in het vak Zoeken](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. Selecteer het **MDM-beleid van**de groep - West en selecteer **eigenschappen** in het gebied **Beheren.**

    ![Pagina Groepsoverzicht, met de optie Lid en informatie gemarkeerd](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. Werk de algemene **instellingen** gegevens zo nodig bij, waaronder:

    ![Eigenschappeninstellingen voor een groep](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **Groepsnaam.** Bewerk de bestaande groepsnaam.
    
    - **Groepsbeschrijving.** Bewerk de bestaande groepsbeschrijving.

    - **Groepstype.** U het type groep niet wijzigen nadat deze is gemaakt. Als u het **groepstype**wilt wijzigen, moet u de groep verwijderen en een nieuwe groep maken.
    
    - **Type lidmaatschap.** Wijzig het lidmaatschapstype. Zie [Een basisgroep maken en leden toevoegen met de Azure Active Directory-portal](active-directory-groups-create-azure-portal.md)voor meer informatie over de verschillende beschikbare lidmaatschapstypen.
    
    - **Object-ID.** U de object-id niet wijzigen, maar u deze wel kopiëren om te gebruiken in uw PowerShell-opdrachten voor de groep. Zie [Azure Active Directory-cmdlets voor](../users-groups-roles/groups-settings-v2-cmdlets.md)meer informatie over het gebruik van PowerShell-cmdlets voor het configureren van groepsinstellingen.

## <a name="next-steps"></a>Volgende stappen
Deze artikelen bevatten aanvullende informatie over Azure Active Directory.

- [Groepen en leden weergeven](active-directory-groups-view-azure-portal.md)

- [Een basisgroep maken en leden toevoegen](active-directory-groups-create-azure-portal.md)

- [Leden toevoegen of verwijderen uit een groep](active-directory-groups-members-azure-portal.md)

- [Dynamische regels voor gebruikers in een groep beheren](../users-groups-roles/groups-create-rule.md)

- [Lidmaatschappen van een groep beheren](active-directory-groups-membership-azure-portal.md)

- [Toegang tot resources met behulp van groepen beheren](active-directory-manage-groups.md)

- [Een Azure-abonnement aan Azure Active Directory koppelen of toevoegen](active-directory-how-subscriptions-associated-directory.md)
