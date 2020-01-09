---
title: 'Directory-rollen toewijzen aan gebruikers: Azure Active Directory | Microsoft Docs'
description: Instructies over het toewijzen van beheerder-en niet-beheerder voor gebruikers met Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2df52969ea79e5d1af132aa82c2ec1ceedb92b82
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422905"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>De beheerder en niet-beheerder rollen toewijzen aan gebruikers met Azure Active Directory
Als een gebruiker in uw organisatie heeft toestemming nodig voor Azure Active Directory (Azure AD)-resources beheren, moet u de gebruiker een juiste rol in Azure AD, op basis van de acties die de gebruiker moet toestemming om uit te voeren.

Zie voor meer informatie over de beschikbare rollen [beheerdersrollen toewijzen in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Zie voor meer informatie over het toevoegen van gebruikers [nieuwe gebruikers toevoegen aan Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Rollen toewijzen
Een veelgebruikte manier voor Azure AD-rollen toewijzen aan een gebruiker zich op de **maprol** pagina voor een gebruiker.

U kunt ook rollen met behulp van Privileged Identity Management (PIM) toewijzen. Zie voor meer informatie over hoe u PIM gaat gebruiken, [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Een rol toewijzen aan een gebruiker
1. Ga naar de [Azure Portal](https://portal.azure.com/) en meld u aan met een algemeen beheerders account voor de Directory. 

2. Zoek en selecteer **Azure Active Directory**.

      ![Azure Portal zoeken naar Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)


3. Selecteer **Gebruikers**.

4. Zoek en selecteer de gebruiker die de roltoewijzing ophaalt. Bijvoorbeeld, _Alain Charon_.

      ![Pagina alle gebruikers: Selecteer de gebruiker](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Selecteer **toegewezen rollen**op de pagina **Alain Charon-profiel** .

    De **Alain Charon - Directory-rol** pagina wordt weergegeven.

6. Selecteer **toewijzing toevoegen**, selecteer de rol die u wilt toewijzen aan Alain (bijvoorbeeld _toepassings beheerder_) en kies vervolgens **selecteren**.

    ![Pagina toegewezen functies: de geselecteerde rol wordt weer gegeven](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    De toepassing-beheerdersrol is toegewezen aan Alain Charon en deze wordt weergegeven op de **Alain Charon - Directory-rol** pagina.

## <a name="remove-a-role-assignment"></a>Roltoewijzing verwijderen
Als u de roltoewijzing verwijderen uit een gebruiker wilt, kunt u ook doen vanuit de **Alain Charon - Directory-rol** pagina.

### <a name="to-remove-a-role-assignment-from-a-user"></a>Een roltoewijzing verwijderen uit een gebruiker

1. Selecteer **Azure Active Directory**, selecteer **gebruikers**, en vervolgens zoekt en selecteert u de gebruiker aan de roltoewijzing is verwijderd. Bijvoorbeeld, _Alain Charon_.

2. Selecteer **toegewezen rollen**, selecteer **toepassings beheerder**en selecteer vervolgens **toewijzing verwijderen**.

    ![Pagina toegewezen functies, waarin de geselecteerde rol en de optie verwijderen worden weer gegeven](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    De beheerdersrol van de toepassing wordt verwijderd uit Alain Charon en deze niet meer wordt weergegeven op de **Alain Charon - Directory-rol** pagina.

## <a name="next-steps"></a>Volgende stappen
- [Toevoegen of verwijderen van gebruikers](add-users-azure-active-directory.md)

- [Toevoegen of wijzigen van de profielgegevens](active-directory-users-profile-azure-portal.md)

- [Gastgebruikers uit een andere directory toevoegen](../b2b/what-is-b2b.md)

Of u andere gebruiker-beheertaken, zoals het toewijzen van gemachtigden, met behulp van beleid en het delen van gebruikersaccounts kan uitvoeren. Zie voor meer informatie over andere beschikbare acties [Azure Active Directory management gebruikersdocumentatie](../users-groups-roles/index.yml).


