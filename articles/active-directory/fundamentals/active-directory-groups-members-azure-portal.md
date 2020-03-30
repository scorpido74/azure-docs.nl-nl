---
title: Groepsleden toevoegen of verwijderen - Azure Active Directory | Microsoft Documenten
description: Instructies voor het toevoegen of verwijderen van leden uit een groep met Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1c83c57be63ae9e2a4d4113accaefe8a2c2b525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68561959"
---
# <a name="add-or-remove-group-members-using-azure-active-directory"></a>Groepsleden toevoegen of verwijderen met Azure Active Directory
Met Azure Active Directory u groepsleden blijven toevoegen en verwijderen.

## <a name="to-add-group-members"></a>Groepsleden toevoegen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met het account van een globale administrator voor de map.

2. Selecteer **Azure Active Directory**en selecteer **Groepen**.

3. Zoek in de pagina **Groepen - Alle groepen** naar de groep waaraan u het lid wilt toevoegen. Gebruik in dit geval onze eerder gemaakte groep, **MDM-beleid - West**.

    ![Pagina Groepen-Alle groepen, groepsnaam gemarkeerd](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. Selecteer op de pagina **Overzicht van MDM-beleid - West** de optie **Leden** onder **Beheren**.

    ![MDM-beleid - Overzichtspagina West, met de optie Leden gemarkeerd](media/active-directory-groups-members-azure-portal/group-overview-blade.png)

5. Selecteer **Leden toevoegen**en zoek en selecteer elk van de leden die u aan de groep wilt toevoegen en kies Selecteer Vervolgens **Selecteren**.

    Je krijgt een bericht waarin staat dat de leden zijn toegevoegd.

    ![Ledenpagina toevoegen, met gezocht naar lid weergegeven](media/active-directory-groups-members-azure-portal/update-members.png)

6. Vernieuw het scherm om alle ledennamen aan de groep te zien.

## <a name="to-remove-group-members"></a>Groepsleden verwijderen

1. Zoek in de pagina **Groepen - Alle groepen** naar de groep waaruit u het lid wilt verwijderen. Opnieuw zullen we gebruiken, **MDM beleid - West**.

2. Selecteer **Leden** in het gebied **Beheren,** zoek naar en selecteer de naam van het lid dat u wilt verwijderen en selecteer **Vervolgens Verwijderen**.

    ![Pagina Ledeninfo, met de optie Verwijderen](media/active-directory-groups-members-azure-portal/remove-members-from-group.png)

## <a name="next-steps"></a>Volgende stappen

- [Groepen en leden weergeven](active-directory-groups-view-azure-portal.md)

- [Instellingen van uw groep bewerken](active-directory-groups-settings-azure-portal.md)

- [Toegang tot resources met behulp van groepen beheren](active-directory-manage-groups.md)

- [Dynamische regels voor gebruikers in een groep beheren](../users-groups-roles/groups-create-rule.md)

- [Een Azure-abonnement aan Azure Active Directory koppelen of toevoegen](active-directory-how-subscriptions-associated-directory.md)
