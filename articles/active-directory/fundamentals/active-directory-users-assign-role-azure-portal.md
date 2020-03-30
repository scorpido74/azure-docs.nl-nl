---
title: Maprollen toewijzen aan gebruikers - Azure Active Directory | Microsoft Documenten
description: Instructies voor het toewijzen van beheerders- en niet-beheerdersrollen aan gebruikers met Azure Active Directory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422905"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Beheerders- en niet-beheerdersrollen toewijzen aan gebruikers met Azure Active Directory
Als een gebruiker in uw organisatie toestemming nodig heeft om Azure Active Directory-resources (Azure AD) te beheren, moet u de gebruiker een geschikte rol toewijzen in Azure AD, op basis van de acties die de gebruiker toestemming nodig heeft om uit te voeren.

Zie [Beheerdersrollen toewijzen in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)voor meer informatie over de beschikbare rollen. Zie Nieuwe gebruikers toevoegen [aan Azure Active Directory](add-users-azure-active-directory.md)voor meer informatie over het toevoegen van gebruikers.

## <a name="assign-roles"></a>Rollen toewijzen
Een veelgebruikte manier om Azure AD-rollen aan een gebruiker toe te wijzen, is op de **maprolpagina** voor een gebruiker.

U ook rollen toewijzen met Behulp van Privileged Identity Management (PIM). Zie [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management)voor meer gedetailleerde informatie over het gebruik van PIM.

### <a name="to-assign-a-role-to-a-user"></a>Een rol toewijzen aan een gebruiker
1. Ga naar de [Azure-portal](https://portal.azure.com/) en meld u aan met een globale beheerdersaccount voor de map. 

2. Zoek naar **Azure Active Directory** en selecteer deze optie.

      ![Azure-portal zoeken naar Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)


3. Selecteer **Gebruikers**.

4. Zoek naar en selecteer de gebruiker die de roltoewijzing krijgt. Bijvoorbeeld, _Alain Charon_.

      ![Pagina Alle gebruikers - selecteer de gebruiker](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Selecteer op de pagina **Alain Charon - Profiel** de optie Toegewezen **rollen**.

    De **rolpagina Alain Charon - Directory** wordt weergegeven.

6. Selecteer **Toewijzing toevoegen,** selecteer de rol die u aan Alain wilt toewijzen (bijvoorbeeld _Toepassingsbeheerder)_ en kies **Selecteer Vervolgens Selecteren**.

    ![Pagina Toegewezen rollen - met de geselecteerde rol](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    De rol van de toepassingsbeheerder wordt toegewezen aan Alain Charon en wordt weergegeven op de rolpagina **Alain Charon - Directory.**

## <a name="remove-a-role-assignment"></a>Roltoewijzing verwijderen
Als u de roltoewijzing van een gebruiker moet verwijderen, u dat ook doen via de rolpagina **Alain Charon - Directory.**

### <a name="to-remove-a-role-assignment-from-a-user"></a>Een roltoewijzing van een gebruiker verwijderen

1. Selecteer **Azure Active Directory**, selecteer **Gebruikers**en zoek en selecteer de gebruiker die de roltoewijzing verwijderd. Bijvoorbeeld, _Alain Charon_.

2. Selecteer **Toegewezen rollen,** selecteer **Toepassingsbeheerder**en selecteer **Toewijzing verwijderen**.

    ![Pagina Toegewezen rollen, met de geselecteerde rol en de optie Verwijderen](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    De rol van de toepassingsbeheerder wordt verwijderd uit Alain Charon en wordt niet meer weergegeven op de rolpagina **Alain Charon - Directory.**

## <a name="next-steps"></a>Volgende stappen
- [Gebruikers toevoegen of verwijderen](add-users-azure-active-directory.md)

- [Profielgegevens toevoegen of wijzigen](active-directory-users-profile-azure-portal.md)

- [Gastgebruikers uit een andere directory toevoegen](../b2b/what-is-b2b.md)

U ook andere gebruikersbeheertaken uitvoeren, zoals het toewijzen van gemachtigden, het gebruik van beleid en het delen van gebruikersaccounts. Zie [Azure Active Directory-gebruikersbeheerdocumentatie](../users-groups-roles/index.yml)voor meer informatie over andere beschikbare acties.


