---
title: Gebruikers toevoegen of verwijderen-Azure Active Directory | Microsoft Docs
description: Instructies voor het toevoegen van nieuwe gebruikers of het verwijderen van bestaande gebruikers met behulp van Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e98b09d8b07c625613e3be149e64ac8f06adc089
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73805548"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Gebruikers toevoegen of verwijderen met Azure Active Directory
Voeg nieuwe gebruikers toe of verwijder bestaande gebruikers uit uw Azure Active Directory-organisatie (Azure AD). U moet een gebruikers beheerder of globale beheerder zijn om gebruikers toe te voegen of te verwijderen. 

## <a name="add-a-new-user"></a>Een nieuwe gebruiker toevoegen
U kunt een nieuwe gebruiker maken met behulp van de Azure Active Directory Portal.

### <a name="to-add-a-new-user"></a>Een nieuwe gebruiker toevoegen
1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) als een gebruikers beheerder voor de organisatie.

2. Selecteer **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **nieuwe gebruiker**.

    ![Gebruikers: alle gebruikers pagina met een nieuwe gebruiker gemarkeerd](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. Op de pagina **Gebruiker** vult u de vereiste gegevens in.

    ![Nieuwe gebruiker toevoegen, gebruikers pagina met gebruikers gegevens](media/add-users-azure-active-directory/new-user-user-blade.png)

   - **Naam (vereist).** De voor-en achternaam van de nieuwe gebruiker. Bijvoorbeeld Mary Parker.

   - **Gebruikers naam (vereist).** De gebruikers naam van de nieuwe gebruiker. Bijvoorbeeld mary@contoso.com.
    
       Het domein deel van de gebruikers naam moet ofwel de oorspronkelijke standaard domeinnaam, <_domein_naam >. onmicrosoft. com of een aangepaste domein naam, zoals contoso.com, gebruiken. Zie [een aangepaste domein naam toevoegen aan Azure Active Directory](add-custom-domain.md)voor meer informatie over het maken van een aangepaste domein naam.

   - **Uplinkpoortprofiel.** U kunt desgewenst meer informatie over de gebruiker toevoegen. U kunt ook op een later tijdstip gebruikers gegevens toevoegen. Zie [informatie over het toevoegen of wijzigen van gebruikers profielen](active-directory-users-profile-azure-portal.md)voor meer informatie over het toevoegen van gebruikers gegevens.

   - **Groepen.** U kunt de gebruiker eventueel toevoegen aan een of meer bestaande groepen. U kunt de gebruiker ook op een later tijdstip toevoegen aan groepen. Zie [een basis groep maken en leden toevoegen](active-directory-groups-create-azure-portal.md)voor meer informatie over het toevoegen van gebruikers aan groepen.

   - **Directory-rol.** Desgewenst kunt u de gebruiker toevoegen aan een Azure AD-beheerdersrol. U kunt de gebruiker toewijzen als globale beheerder of een of meer van de beperkte beheerders rollen in azure AD. Zie [rollen toewijzen aan gebruikers](active-directory-users-assign-role-azure-portal.md)voor meer informatie over het toewijzen van rollen.

4. Kopieer het automatisch gegenereerde wacht woord dat is opgegeven in het vak **wacht woord** . U moet dit wacht woord aan de gebruiker geven voor het eerste aanmeldings proces.

5. Selecteer **Maken**.

    De gebruiker wordt gemaakt en toegevoegd aan uw Azure AD-Tenant.

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Een nieuwe gebruiker toevoegen in een hybride omgeving
Als u een omgeving met Azure Active Directory (Cloud) en Windows Server-Active Directory (on-premises) hebt, kunt u nieuwe gebruikers toevoegen door de bestaande gegevens van het gebruikers account te synchroniseren. Zie [uw on-premises Directory's integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)voor meer informatie over hybride omgevingen en gebruikers.

## <a name="delete-a-user"></a>Een gebruiker verwijderen
U kunt een bestaande gebruiker verwijderen via Azure Active Directory Portal.

### <a name="to-delete-a-user"></a>Een gebruiker verwijderen
1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met behulp van een gebruikers beheerders account voor de organisatie.

2. Selecteer **Azure Active Directory**, selecteer **gebruikers**, zoek naar en selecteer de gebruiker die u wilt verwijderen uit uw Azure AD-Tenant. Bijvoorbeeld _Mary Parker_.

3. Selecteer **gebruiker verwijderen**.

    ![Gebruikers-pagina alle gebruikers met gemarkeerde gebruiker verwijderen](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    De gebruiker wordt verwijderd en wordt niet meer weer gegeven op de pagina **gebruikers-alle gebruikers** . De gebruiker kan de volgende 30 dagen worden weer gegeven op de pagina **Verwijderde gebruikers** en kan worden hersteld tijdens die tijd. Zie [een onlangs verwijderde gebruiker herstellen of permanent verwijderen](active-directory-users-restore.md)voor meer informatie over het herstellen van een gebruiker. Wanneer een gebruiker wordt verwijderd, worden alle licenties die door de gebruiker worden gebruikt, beschikbaar gesteld voor andere gebruikers die moeten worden gebruikt.

    >[!Note]
    >U moet Windows Server Active Directory gebruiken om de identiteit, contact gegevens of taak gegevens bij te werken voor gebruikers waarvan de bron van de autoriteit Windows Server Active Directory is. Nadat u de update hebt voltooid, moet u wachten tot de volgende synchronisatie cyclus is voltooid voordat u de wijzigingen ziet.

## <a name="next-steps"></a>Volgende stappen

Nadat u uw gebruikers hebt toegevoegd, kunt u de volgende basis processen uitvoeren:

- [Profiel gegevens toevoegen of wijzigen](active-directory-users-profile-azure-portal.md)

- [Rollen toewijzen aan gebruikers](active-directory-users-assign-role-azure-portal.md)

- [Een basis groep maken en leden toevoegen](active-directory-groups-create-azure-portal.md)

- [Werken met dynamische groepen en gebruikers](../users-groups-roles/groups-create-rule.md)

U kunt ook andere gebruikers beheer taken uitvoeren, zoals het [toevoegen van gast gebruikers vanuit een andere map](../b2b/what-is-b2b.md) of [het herstellen van een verwijderde gebruiker](active-directory-users-restore.md). Zie [Azure Active Directory-documentatie voor gebruikers beheer](../users-groups-roles/index.yml)voor meer informatie over andere beschik bare acties.
