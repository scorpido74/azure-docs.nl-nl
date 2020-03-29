---
title: Roltoewijzingen voor externe gebruikers toevoegen of verwijderen met RBAC en de Azure-portal
description: Meer informatie over het verlenen van toegang tot Azure-bronnen voor gebruikers buiten een organisatie met Behulp van RBAC (Azure Role Based Access Control).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: 1c440b85f792ac5bb1336f4d20f930aafc38ad7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245640"
---
# <a name="add-or-remove-role-assignments-for-external-guest-users-using-azure-rbac-and-the-azure-portal"></a>Roltoewijzingen toevoegen of verwijderen voor externe gastgebruikers met Azure RBAC en de Azure-portal

[Azure role-based access control (RBAC)](overview.md) maakt een beter beveiligingsbeheer mogelijk voor grote organisaties en voor kleine en middelgrote bedrijven die werken met externe medewerkers, leveranciers of freelancers die toegang nodig hebben tot specifieke bronnen in uw omgeving, maar niet noodzakelijkerwijs tot de hele infrastructuur of factureringsgerelateerde scopes. U de mogelijkheden in [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md) gebruiken om samen te werken met externe gastgebruikers en u RBAC gebruiken om alleen de machtigingen te verlenen die gastgebruikers in uw omgeving nodig hebben.

## <a name="prerequisites"></a>Vereisten

Als u roltoewijzingen wilt toevoegen of verwijderen, moet u het als ander beschikken:

- `Microsoft.Authorization/roleAssignments/write`en `Microsoft.Authorization/roleAssignments/delete` machtigingen, zoals [beheerder van gebruikerstoegang](built-in-roles.md#user-access-administrator) of [eigenaar](built-in-roles.md#owner)

## <a name="when-would-you-invite-guest-users"></a>Wanneer zou u gastgebruikers uitnodigen?

Hier volgen een paar voorbeeldscenario's waarin u gastgebruikers uit nodigt voor uw organisatie en machtigingen verleent:

- Sta een externe zelfstandige leverancier toe die alleen een e-mailaccount heeft om toegang te krijgen tot uw Azure-bronnen voor een project.
- Sta een externe partner toe om bepaalde resources of een volledig abonnement te beheren.
- Sta ondersteuningstechnici toe die niet in uw organisatie zijn (zoals Microsoft-ondersteuning) om tijdelijk toegang te krijgen tot uw Azure-bron om problemen op te lossen.

## <a name="permission-differences-between-member-users-and-guest-users"></a>Machtigingsverschillen tussen lidgebruikers en gastgebruikers

Native leden van een directory (lidgebruikers) hebben andere machtigingen dan gebruikers die als B2B-samenwerkingsgast (gastgebruikers) uit een andere map worden uitgenodigd. De gebruiker van leden kan bijvoorbeeld bijna alle directorygegevens lezen, terwijl gastgebruikers beperkte directorymachtigingen hebben. Zie [Wat zijn de standaardgebruikersmachtigingen in Azure Active Directory voor](../active-directory/fundamentals/users-default-permissions.md)meer informatie over ledengebruikers en gastgebruikers?

## <a name="add-a-guest-user-to-your-directory"></a>Een gastgebruiker toevoegen aan uw map

Voer deze stappen uit om een gastgebruiker toe te voegen aan uw directory met behulp van de Azure Active Directory-pagina.

1. Zorg ervoor dat de externe samenwerkingsinstellingen van uw organisatie zo zijn geconfigureerd dat u gasten uitnodigen. Zie [Externe samenwerking inschakelen en beheren wie gasten kan uitnodigen](../active-directory/b2b/delegate-invitations.md)voor meer informatie.

1. Klik in de Azure-portal op **Azure Active Directory** > **Users** > **New guest user**.

    ![Nieuwe gastgebruikersfunctie in Azure-portal](./media/role-assignments-external-users/invite-guest-user.png)

1. Volg de stappen om een nieuwe gastgebruiker toe te voegen. Zie [Azure Active Directory B2B-samenwerkingsgebruikers toevoegen in de Azure-portal](../active-directory/b2b/add-users-administrator.md#add-guest-users-to-the-directory)voor meer informatie.

Nadat u een gastgebruiker aan de directory hebt toegevoegd, u de gastgebruiker een directe link naar een gedeelde app sturen of de gastgebruiker kan op de inwisselings-URL in de uitnodigingse-mail klikken.

![E-mail van gastgebruiker uitnodigen](./media/role-assignments-external-users/invite-email.png)

Als de gastgebruiker toegang heeft tot uw directory, moet hij of zij het uitnodigingsproces voltooien.

![Machtigingen voor het bekijken van gasten uitnodigen](./media/role-assignments-external-users/invite-review-permissions.png)

Zie Azure [Active Directory B2B-uitnodigingsuitnodiging inwisselen voor Azure Active Directory B2B.](../active-directory/b2b/redemption-experience.md)

## <a name="add-a-role-assignment-for-a-guest-user"></a>Een roltoewijzing toevoegen voor een gastgebruiker

In RBAC, om toegang te verlenen, wijst u een rol toe. Als u een roltoewijzing voor een gastgebruiker wilt toevoegen, volgt u [dezelfde stappen](role-assignments-portal.md#add-a-role-assignment) als voor een lidgebruiker, groep, serviceprincipal of beheerde identiteit. Volg deze stappen voeg een roltoewijzing toe voor een gastgebruiker op verschillende scopes.

1. Klik in de Azure-portal op **Alle services**.

1.  Selecteer de set resources waarop de toegang van toepassing is, ook wel het bereik genoemd. U bijvoorbeeld **Beheergroepen,** **Abonnementen,** **Resourcegroepen**of een resource selecteren.

1. Klik op de specifieke resource.

1. Klik op **Toegangsbeheer (IAM)**.

    In de volgende schermafbeelding ziet u een voorbeeld van het IAM-blad (Access control) voor een resourcegroep. Als u hier wijzigingen in het toegangscontrolebeheer aanbrengt, zijn deze alleen van toepassing op de brongroep.

    ![IAM-blad (Access Control) voor een resourcegroep](./media/role-assignments-external-users/access-control-resource-group.png)

1. Klik op het tabblad **Roltoewijzingen** om alle roltoewijzingen in dit bereik weer te geven.

1. Klik **op** > **Roltoewijzing toevoegen toevoegen** om het deelvenster Roltoewijzing toevoegen te openen.

    Als u niet bent gemachtigd voor het toewijzen van rollen, is de optie Roltoewijzing toevoegen uitgeschakeld.

    ![Menu Toevoegen](./media/role-assignments-external-users/add-menu.png)

1. Selecteer in de vervolgkeuzelijst **Rol** een rol, zoals **Inzender voor virtuele machines**.

1. Selecteer **in** de lijst Selecteren de gastgebruiker. Als u de gebruiker niet in de lijst ziet, u in het vak **Selecteren** typen om in de map te zoeken naar weergavenamen, e-mailadressen en object-id's.

   ![Deelvenster Roltoewijzing toevoegen](./media/role-assignments-external-users/add-role-assignment.png)

1. Klik **op Opslaan** om de rol toe te wijzen aan het geselecteerde bereik.

    ![Roltoewijzing voor virtuele machinemedewerker](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="add-a-role-assignment-for-a-guest-user-not-yet-in-your-directory"></a>Een roltoewijzing toevoegen voor een gastgebruiker die nog niet in uw map is

Als u een roltoewijzing voor een gastgebruiker wilt toevoegen, volgt u [dezelfde stappen](role-assignments-portal.md#add-a-role-assignment) als voor een lidgebruiker, groep, serviceprincipal of beheerde identiteit.

Als de gastgebruiker zich nog niet in uw map bevindt, u de gebruiker rechtstreeks uitnodigen vanuit het deelvenster Roltoewijzing toevoegen.

1. Klik in de Azure-portal op **Alle services**.

1.  Selecteer de set resources waarop de toegang van toepassing is, ook wel het bereik genoemd. U bijvoorbeeld **Beheergroepen,** **Abonnementen,** **Resourcegroepen**of een resource selecteren.

1. Klik op de specifieke resource.

1. Klik op **Toegangsbeheer (IAM)**.

1. Klik op het tabblad **Roltoewijzingen** om alle roltoewijzingen in dit bereik weer te geven.

1. Klik **op** > **Roltoewijzing toevoegen toevoegen** om het deelvenster Roltoewijzing toevoegen te openen.

    ![Menu Toevoegen](./media/role-assignments-external-users/add-menu.png)

1. Selecteer in de vervolgkeuzelijst **Rol** een rol, zoals **Inzender voor virtuele machines**.

1. Typ **in** de lijst Selecteren het e-mailadres van de persoon die u wilt uitnodigen en selecteer die persoon.

   ![Gastgebruiker uitnodigen in deelvenster Roltoewijzing toevoegen](./media/role-assignments-external-users/add-role-assignment-new-guest.png)

1. Klik **op Opslaan** om de gastgebruiker toe te voegen aan uw map, de rol toe te wijzen en een uitnodiging te verzenden.

    Na een paar ogenblikken ziet u een melding van de roltoewijzing en informatie over de uitnodiging.

    ![Functietoewijzing en uitnodigingsgebruikermelding](./media/role-assignments-external-users/invited-user-notification.png)

1. Als u de gastgebruiker handmatig wilt uitnodigen, klikt u met de rechtermuisknop en kopieert u de uitnodigingskoppeling in de melding. Klik niet op de koppeling uitnodiging omdat het uitnodigingsproces wordt gestart.

    De uitnodigingskoppeling heeft de volgende indeling:

    `https://invitations.microsoft.com/redeem/...`

1. Stuur de uitnodigingskoppeling naar de gastgebruiker om het uitnodigingsproces te voltooien.

    Zie Azure [Active Directory B2B-uitnodigingsuitnodiging inwisselen voor Azure Active Directory B2B.](../active-directory/b2b/redemption-experience.md)

## <a name="remove-a-guest-user-from-your-directory"></a>Een gastgebruiker uit uw directory verwijderen

Voordat u een gastgebruiker uit een map verwijdert, moet u eerst roltoewijzingen voor die gastgebruiker verwijderen. Volg deze stappen om een gastgebruiker uit een map te verwijderen.

1. Open **Access-besturingselement (IAM)** op een bereik, zoals beheergroep, abonnement, resourcegroep of resource, waarbij de gastgebruiker een roltoewijzing heeft.

1. Klik op het tabblad **Toewijzingen van rollen** om alle roltoewijzingen weer te geven.

1. Voeg in de lijst met roltoewijzingen een vinkje toe naast de gastgebruiker met de roltoewijzing die u wilt verwijderen.

   ![Roltoewijzing verwijderen](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. Klik op **Verwijderen**.

   ![Bericht bij verwijderen van roltoewijzing](./media/role-assignments-external-users/remove-role-assignment.png)

1. Klik op **Ja** om te bevestigen dat u de roltoewijzing inderdaad wilt verwijderen.

1. Klik op de linkernavigatiebalk op **Azure Active Directory** > **Users**.

1. Klik op de gastgebruiker die u wilt verwijderen.

1. Klik **op Verwijderen**.

   ![Gastgebruiker verwijderen](./media/role-assignments-external-users/delete-guest-user.png)

1. Klik in het verwijderbericht dat wordt weergegeven op **Ja**.

## <a name="troubleshoot"></a>Problemen oplossen

### <a name="guest-user-cannot-browse-the-directory"></a>Gastgebruiker kan niet door de map bladeren

Gastgebruikers hebben beperkte directorymachtigingen. Gastgebruikers kunnen bijvoorbeeld niet door de map bladeren en kunnen niet zoeken naar groepen of toepassingen. Zie [Wat zijn de standaardgebruikersmachtigingen in Azure Active Directory voor](../active-directory/fundamentals/users-default-permissions.md)meer informatie.

![Gastgebruiker kan niet door gebruikers bladeren in een map](./media/role-assignments-external-users/directory-no-users.png)

Als een gastgebruiker extra bevoegdheden nodig heeft in de map, u een maprol toewijzen aan de gastgebruiker. Als u echt wilt dat een gastgebruiker volledige leestoegang tot uw map heeft, u de gastgebruiker toevoegen aan de rol [DirectoryReaders](../active-directory/users-groups-roles/directory-assign-admin-roles.md) in Azure AD. Zie [Machtigingen verlenen aan gebruikers van partnerorganisaties in uw Azure Active Directory-tenant voor](../active-directory/b2b/add-guest-to-role.md)meer informatie.

![Rol Directoryreaders toewijzen](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>Gastgebruiker kan niet door gebruikers, groepen of serviceprincipals bladeren om rollen toe te wijzen

Gastgebruikers hebben beperkte directorymachtigingen. Zelfs als een gastgebruiker een [eigenaar](built-in-roles.md#owner) op een bereik is, als hij een roltoewijzing probeert toe te voegen om iemand anders toegang te verlenen, kan hij of zij niet door de lijst met gebruikers, groepen of serviceprincipals bladeren.

![Gastgebruiker kan niet door beveiligingsprincipals bladeren om rollen toe te wijzen](./media/role-assignments-external-users/directory-no-browse.png)

Als de gastgebruiker de exacte aanmeldingsnaam van iemand in de map weet, kan hij of zij toegang verlenen. Als u echt wilt dat een gastgebruiker volledige leestoegang tot uw map heeft, u de gastgebruiker toevoegen aan de rol [DirectoryReaders](../active-directory/users-groups-roles/directory-assign-admin-roles.md) in Azure AD. Zie [Machtigingen verlenen aan gebruikers van partnerorganisaties in uw Azure Active Directory-tenant voor](../active-directory/b2b/add-guest-to-role.md)meer informatie.

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>Gastgebruiker kan geen toepassingen registreren of serviceprincipals maken

Gastgebruikers hebben beperkte directorymachtigingen. Als een gastgebruiker toepassingen moet kunnen registreren of serviceprincipals moet kunnen maken, u de gastgebruiker toevoegen aan de rol [Toepassingsontwikkelaars](../active-directory/users-groups-roles/directory-assign-admin-roles.md) in Azure AD. Zie [Machtigingen verlenen aan gebruikers van partnerorganisaties in uw Azure Active Directory-tenant voor](../active-directory/b2b/add-guest-to-role.md)meer informatie.

![Gastgebruiker kan aanvragen niet registreren](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>Gastgebruiker ziet de nieuwe map niet

Als een gastgebruiker toegang heeft gekregen tot een map, maar de nieuwe map in de Azure-portal niet wordt weergegeven wanneer hij/zij probeert over te schakelen in het deelvenster **Directory + abonnement,** controleert u of de gastgebruiker het uitnodigingsproces heeft voltooid. Zie Azure [Active Directory B2B-uitnodigingsuitnodiging inwisselen voor Azure Active Directory B2B.](../active-directory/b2b/redemption-experience.md)

### <a name="guest-user-does-not-see-resources"></a>Gastgebruiker ziet geen bronnen

Als een gastgebruiker toegang heeft gekregen tot een map, maar de resources waartoe hij toegang heeft gekregen in de Azure-portal niet wordt gezien, controleert u of de gastgebruiker de juiste map heeft geselecteerd. Een gastgebruiker heeft mogelijk toegang tot meerdere mappen. Als u van directeur wilt wisselen, klikt u linksboven op **Map + abonnement**en klikt u vervolgens op de juiste map.

![Deelvenster Mappen + abonnementen in Azure-portal](./media/role-assignments-external-users/directory-subscription.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure Active Directory B2B-samenwerkingsgebruikers toevoegen aan de Azure-portal](../active-directory/b2b/add-users-administrator.md)
- [Eigenschappen van een Azure Active Directory B2B-samenwerkingsgebruiker](../active-directory/b2b/user-properties.md)
- [De elementen van de e-mail met uitnodiging voor B2B-samenwerking - Azure Active Directory](../active-directory/b2b/invitation-email-elements.md)
- [Een gastgebruiker toevoegen als medebeheerder](classic-administrators.md#add-a-guest-user-as-a-co-administrator)