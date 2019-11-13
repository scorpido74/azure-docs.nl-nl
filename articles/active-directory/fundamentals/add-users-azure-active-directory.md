---
title: Toevoegen of verwijderen van gebruikers - Azure Active Directory | Microsoft Docs
description: Instructies over hoe u nieuwe gebruikers toevoegen of verwijderen van bestaande gebruikers met Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b01eb7730290fbf7340fc0a6d8cac8157498f64a
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013620"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Toevoegen of verwijderen van gebruikers met Azure Active Directory

Voeg nieuwe gebruikers toe of verwijder bestaande gebruikers uit uw Azure Active Directory-organisatie (Azure AD). U moet een gebruikers beheerder of globale beheerder zijn om gebruikers toe te voegen of te verwijderen.

## <a name="add-a-new-user"></a>Een nieuwe gebruiker toevoegen

U kunt een nieuwe gebruiker met behulp van de Azure Active Directory-portal maken.

### <a name="to-add-a-new-user"></a>Een nieuwe gebruiker toe te voegen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) als een gebruikers beheerder voor de organisatie.

2. Selecteer **Azure Active Directory**, selecteer **gebruikers**, en selecteer vervolgens **nieuwe gebruiker**.

    ![Gebruikers - pagina voor alle gebruikers met de nieuwe gebruiker is gemarkeerd](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. Selecteer op de pagina **nieuwe gebruiker** de optie **gebruiker maken** en voeg vervolgens de gegevens van de gebruiker toe.

    ![Nieuwe gebruiker, met gebruikersgegevens op de pagina gebruiker toevoegen](media/add-users-azure-active-directory/new-user-user-blade.png)

   - **Naam (vereist)** : de voor-en achternaam van de nieuwe gebruiker. Bijvoorbeeld Chris Green.

   - **Gebruikers naam (vereist)** : de gebruikers naam van de nieuwe gebruiker. Bijvoorbeeld chris@contoso.com.

     Het domein deel van de gebruikers naam moet ofwel de oorspronkelijke standaard domeinnaam, <_domein_naam >. onmicrosoft. com of een aangepaste domein naam in uw Azure AD-organisatie, zoals contoso.com, gebruiken. U kunt kiezen uit de lijst met beschik bare domeinen. U kunt de lijst ook filteren door een deel van de domein naam te typen. Zie voor meer informatie over het maken van een aangepaste domeinnaam [een aangepaste domeinnaam toevoegen aan Azure Active Directory](add-custom-domain.md).

   - **Groepen**: u kunt de gebruiker toevoegen aan een of meer bestaande groepen of u kunt dit later doen. Zie voor meer informatie over het toevoegen van gebruikers aan groepen [hoe u een basisgroep maken en leden toevoegen](active-directory-groups-create-azure-portal.md).

   - **Directory-rol**: als u Azure AD-beheerders machtigingen voor de gebruiker nodig hebt, kunt u deze toevoegen aan een Azure AD-rol. U kunt de gebruiker toewijzen als globale beheerder of een of meer van de beperkte beheerders rollen in azure AD. Zie voor meer informatie over het toewijzen van rollen [rollen toewijzen aan gebruikers](active-directory-users-assign-role-azure-portal.md).

   - **Taak info**: u kunt hier meer informatie over de gebruiker toevoegen of dit later doen. Zie voor meer informatie over het toevoegen van gebruikersgegevens [toevoegen of wijzigen van de informatie uit gebruikersprofielen](active-directory-users-profile-azure-portal.md).

4. Kopieer de automatisch gegenereerde wachtwoord opgegeven in de **wachtwoord** vak. U kunt ervoor kiezen om het automatisch gegenereerde wacht woord te gebruiken dat is opgegeven in het vak wacht woord of een aangepast wacht woord te maken. U moet dit wachtwoord geven aan de gebruiker voor de initiële aanmeldingsproces.

5. Selecteer **Maken**.

De gebruiker wordt gemaakt en toegevoegd aan uw Azure AD-organisatie.

## <a name="add-a-new-guest-user"></a>Een nieuwe gast gebruiker toevoegen

U kunt ook een nieuwe gast gebruiker uitnodigen om samen te werken met uw organisatie door **gebruikers uitnodigen** te selecteren op de pagina **nieuwe gebruiker** . Als de instellingen voor externe samen werking van uw organisatie zodanig zijn geconfigureerd dat u gasten kunt uitnodigen, wordt de gebruiker een uitnodiging per e-mail verzonden om te beginnen met samen werking. Zie [B2B-gebruikers uitnodigen voor Azure Active Directory](../b2b/add-users-administrator.md) voor meer informatie over het uitnodigen van B2B-samenwerkings gebruikers

## <a name="add-a-consumer-user"></a>Een consument gebruiker toevoegen

Er zijn mogelijk scenario's waarin u hand matig consumenten accounts wilt maken in de map Azure Active Directory B2C (Azure AD B2C). Zie voor meer informatie over het maken van consumenten accounts [consumenten gebruikers maken en verwijderen in azure AD B2C](../../active-directory-b2c/manage-users-portal.md).

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Voeg een nieuwe gebruiker in een hybride omgeving

Als u een omgeving met zowel Azure Active Directory (cloud) en Windows Server Active Directory (on-premises) hebt, kunt u nieuwe gebruikers toevoegen door te synchroniseren van de bestaande gegevens van de gebruiker-account. Zie voor meer informatie over hybride omgevingen en gebruikers [uw on-premises directory's integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Een gebruiker verwijderen

U kunt een bestaande gebruiker met behulp van Azure Active Directory-portal verwijderen.

### <a name="to-delete-a-user"></a>Een gebruiker verwijderen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met behulp van een gebruikers beheerders account voor de organisatie.

1. Selecteer **Azure Active Directory**, selecteer **gebruikers**, en vervolgens zoekt en selecteert u de gebruiker die u wilt verwijderen uit uw Azure AD-tenant. Bijvoorbeeld, _Mary Parker_.

1. Selecteer **gebruiker verwijderen**.

    ![Gebruikers - pagina voor alle gebruikers met gebruiker verwijderen gemarkeerd](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    De gebruiker wordt verwijderd en niet meer wordt weergegeven op de **gebruikers: alle gebruikers** pagina. De gebruiker kan worden weergegeven in de **verwijderde gebruikers** pagina voor de komende 30 dagen en gedurende deze tijd kunnen worden hersteld. Zie voor meer informatie over het herstellen van een gebruiker [herstellen of permanent verwijderen van een onlangs verwijderde gebruiker](active-directory-users-restore.md). Wanneer een gebruiker wordt verwijderd, worden alle licenties die door de gebruiker worden gebruikt, beschikbaar gesteld voor andere gebruikers die moeten worden gebruikt.

    >[!Note]
    >U moet Windows Server Active Directory gebruiken om bij te werken van de identiteit, contactgegevens of taakgegevens voor waarvan u de bron van de instantie Windows Server Active Directory is-gebruikers. Nadat u de update hebt voltooid, moet u de volgende synchronisatiecyclus uitvoeren voordat u de wijzigingen ziet wachten.

## <a name="next-steps"></a>Volgende stappen

Nadat u uw gebruikers hebt toegevoegd, kunt u de volgende basis-processen uitvoeren:

- [Toevoegen of wijzigen van de profielgegevens](active-directory-users-profile-azure-portal.md)

- [Rollen toewijzen aan gebruikers](active-directory-users-assign-role-azure-portal.md)

- [Een basisgroep maken en leden toevoegen](active-directory-groups-create-azure-portal.md)

- [Werken met dynamische groepen en gebruikers](../users-groups-roles/groups-create-rule.md)

U kunt ook andere gebruikers beheer taken uitvoeren, zoals het [toevoegen van gast gebruikers vanuit een andere Azure AD-organisatie](../b2b/what-is-b2b.md) of [het herstellen van een verwijderde gebruiker](active-directory-users-restore.md). Zie voor meer informatie over andere beschikbare acties [Azure Active Directory management gebruikersdocumentatie](../users-groups-roles/index.yml).
