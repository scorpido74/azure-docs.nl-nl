---
title: Directory rollen toewijzen aan gebruikers-Azure Active Directory | Microsoft Docs
description: Instructies voor het toewijzen van beheerders-en niet-beheerders rollen aan gebruikers met Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87b063a4d51d5d5d1e3d7949be3754ccbe74acca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85604111"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Beheerders-en niet-beheerders rollen toewijzen aan gebruikers met Azure Active Directory
Als een gebruiker in uw organisatie toestemming nodig heeft om Azure Active Directory-resources (Azure AD) te beheren, moet u de gebruiker een geschikte rol toewijzen in azure AD, op basis van de acties die de gebruiker nodig heeft om uit te voeren.

Zie [beheerders rollen toewijzen in azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)voor meer informatie over de beschik bare rollen. Zie [nieuwe gebruikers toevoegen aan Azure Active Directory](add-users-azure-active-directory.md)voor meer informatie over het toevoegen van gebruikers.

## <a name="assign-roles"></a>Rollen toewijzen
Een gemeen schappelijke manier om Azure AD-rollen aan een gebruiker toe te wijzen, is op de pagina **Directory-functie** voor een gebruiker.

U kunt ook rollen toewijzen met behulp van Privileged Identity Management (PIM). Zie [privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management)voor meer gedetailleerde informatie over het gebruik van Pim.

### <a name="to-assign-a-role-to-a-user"></a>Een rol toewijzen aan een gebruiker
1. Ga naar de [Azure Portal](https://portal.azure.com/) en meld u aan met een algemeen beheerders account voor de Directory. 

2. Zoek naar **Azure Active Directory** en selecteer deze optie.

      ![Azure Portal zoeken naar Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)


3. Selecteer **gebruikers**.

4. Zoek en selecteer de gebruiker die de roltoewijzing ophaalt. Bijvoorbeeld _Alain Charon_.

      ![Pagina alle gebruikers: Selecteer de gebruiker](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Selecteer **toegewezen rollen**op de pagina **Alain Charon-profiel** .

    De pagina **Alain Charon-Directory Role** wordt weer gegeven.

6. Selecteer **toewijzing toevoegen**, selecteer de rol die u wilt toewijzen aan Alain (bijvoorbeeld _toepassings beheerder_) en kies vervolgens **selecteren**.

    ![Pagina toegewezen functies: de geselecteerde rol wordt weer gegeven](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    De rol toepassings beheerder is toegewezen aan Alain Charon en deze wordt weer gegeven op de pagina **Alain Charon-Directory** .

## <a name="remove-a-role-assignment"></a>Roltoewijzing verwijderen
Als u de roltoewijzing van een gebruiker wilt verwijderen, kunt u dit ook doen op de pagina **Alain Charon-Directory Role** .

### <a name="to-remove-a-role-assignment-from-a-user"></a>Een roltoewijzing van een gebruiker verwijderen

1. Selecteer **Azure Active Directory**, selecteer **gebruikers**, zoek naar en selecteer de gebruiker die de roltoewijzing heeft verwijderd. Bijvoorbeeld _Alain Charon_.

2. Selecteer **toegewezen rollen**, selecteer **toepassings beheerder**en selecteer vervolgens **toewijzing verwijderen**.

    ![Pagina toegewezen functies, waarin de geselecteerde rol en de optie verwijderen worden weer gegeven](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    De rol toepassings beheerder wordt verwijderd uit Alain Charon en wordt niet meer weer gegeven op de pagina **Alain Charon-Directory** .

## <a name="next-steps"></a>Volgende stappen
- [Gebruikers toevoegen of verwijderen](add-users-azure-active-directory.md)

- [Profiel gegevens toevoegen of wijzigen](active-directory-users-profile-azure-portal.md)

- [Gastgebruikers uit een andere directory toevoegen](../b2b/what-is-b2b.md)

U kunt ook andere gebruikers beheer taken uitvoeren, zoals het toewijzen van gemachtigden, het gebruiken van beleid en het delen van gebruikers accounts. Zie [Azure Active Directory-documentatie voor gebruikers beheer](../users-groups-roles/index.yml)voor meer informatie over andere beschik bare acties.


