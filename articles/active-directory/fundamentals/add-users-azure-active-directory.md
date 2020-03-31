---
title: Gebruikers toevoegen of verwijderen - Azure Active Directory | Microsoft Documenten
description: Instructies voor het toevoegen van nieuwe gebruikers of het verwijderen van bestaande gebruikers met Azure Active Directory.
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
ms.openlocfilehash: 3d72616422934501e042375edfb10a25aa27c527
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262111"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Gebruikers toevoegen of verwijderen met Azure Active Directory

Voeg nieuwe gebruikers toe of verwijder bestaande gebruikers uit uw Azure Active Directory-organisatie (Azure AD). Als u gebruikers wilt toevoegen of verwijderen, moet u een gebruikersbeheerder of globale beheerder zijn.

## <a name="add-a-new-user"></a>Een nieuwe gebruiker toevoegen

U een nieuwe gebruiker maken met de Azure Active Directory-portal.

Voer de volgende stappen uit om een nieuwe gebruiker toe te voegen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) als gebruikersbeheerder voor de organisatie.

1. Zoeken naar en selecteer *Azure Active Directory* op elke pagina.

1. Selecteer **Gebruikers**en selecteer **Vervolgens Nieuwe gebruiker**.

    ![Een gebruiker toevoegen via Gebruikers - Alle gebruikers in Azure AD](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. Voer **op** de pagina Gebruiker gegevens in voor deze gebruiker:

   - **Naam**. Vereist. De voor- en achternaam van de nieuwe gebruiker. Bijvoorbeeld, *Mary Parker.*

   - **Gebruikersnaam**. Vereist. De gebruikersnaam van de nieuwe gebruiker. Bijvoorbeeld `mary@contoso.com`.

     Het domeingedeelte van de gebruikersnaam moet de oorspronkelijke standaarddomeinnaam, * \<uw domeinnaam>.onmicrosoft.com*of een aangepaste domeinnaam, zoals *contoso.com*. Zie [Uw aangepaste domeinnaam toevoegen met de Azure Active Directory-portal](add-custom-domain.md)voor meer informatie over het maken van een aangepaste domeinnaam.

   - **Groepen**. Optioneel u de gebruiker toevoegen aan een of meer bestaande groepen. U de gebruiker ook op een later tijdstip aan groepen toevoegen. Zie [Een basisgroep maken en leden toevoegen met Azure Active Directory](active-directory-groups-create-azure-portal.md)voor meer informatie over het toevoegen van gebruikers aan groepen.

   - **Maprol:** als u Azure AD-beheermachtigingen voor de gebruiker nodig hebt, u deze toevoegen aan een Azure AD-rol. U de gebruiker toewijzen als globale beheerder of een of meer van de beperkte beheerdersrollen in Azure AD. Zie Rollen toewijzen aan gebruikers voor meer informatie over het toewijzen van [rollen.](active-directory-users-assign-role-azure-portal.md)

   - **Functie-informatie:** U hier meer informatie over de gebruiker toevoegen of later doen. Zie [Gebruikersprofielgegevens toevoegen of wijzigen](active-directory-users-profile-azure-portal.md)voor meer informatie over het toevoegen van gebruikersgegevens.

1. Kopieer het automatisch gegenereerde wachtwoord in het vak **Wachtwoord.** U moet dit wachtwoord aan de gebruiker geven om zich voor de eerste keer aan te melden.

1. Selecteer **Maken**.

De gebruiker wordt gemaakt en toegevoegd aan uw Azure AD-organisatie.

## <a name="add-a-new-guest-user"></a>Een nieuwe gastgebruiker toevoegen

U ook een nieuwe gastgebruiker uitnodigen om met uw organisatie samen te werken door **Gebruiker uitnodigen** te selecteren op de pagina **Nieuwe gebruiker.** Als de externe samenwerkingsinstellingen van uw organisatie zodanig zijn geconfigureerd dat u gasten mag uitnodigen, ontvangt de gebruiker per e-mail een uitnodiging die hij moet accepteren om te kunnen samenwerken. Zie [B2B-gebruikers uitnodigen voor Azure Active Directory voor](../b2b/add-users-administrator.md) meer informatie over het uitnodigen van Gebruikers van B2B-samenwerking

## <a name="add-a-consumer-user"></a>Een consument toevoegen

Er zijn mogelijk scenario's waarin u handmatig consumentenaccounts wilt maken in uw Azure Active Directory B2C (Azure AD B2C). Zie [Consumentengebruikers maken en verwijderen in Azure AD B2C voor](../../active-directory-b2c/manage-users-portal.md)meer informatie over het maken van consumentenaccounts.

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Een nieuwe gebruiker toevoegen binnen een hybride omgeving

Als u een omgeving hebt met zowel Azure Active Directory (cloud) als Windows Server Active Directory (on-premises), u nieuwe gebruikers toevoegen door de bestaande gebruikersaccountgegevens te synchroniseren. Zie [Uw on-premises mappen integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)voor meer informatie over hybride omgevingen en gebruikers.

## <a name="delete-a-user"></a>Een gebruiker verwijderen

U een bestaande gebruiker verwijderen via Azure Active Directory-portal.

Voer de volgende stappen uit om een gebruiker te verwijderen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) met een gebruikersbeheerdersaccount voor de organisatie.

1. Zoeken naar en selecteer *Azure Active Directory* op elke pagina.

1. Zoek naar en selecteer de gebruiker die u uit uw Azure AD-tenant wilt verwijderen. Bijvoorbeeld, _Mary Parker._

1. Selecteer **Gebruiker verwijderen**.

    ![Gebruikers - Alle gebruikerspagina met Gebruiker verwijderen gemarkeerd](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

De gebruiker wordt verwijderd en wordt niet meer weergegeven op de pagina **Gebruikers - Alle gebruikers.** De gebruiker is de komende 30 dagen te zien op de pagina **Verwijderde gebruikers** en kan in die periode worden hersteld. Zie [Een onlangs verwijderde gebruiker herstellen of verwijderen met Azure Active Directory](active-directory-users-restore.md)voor meer informatie over het herstellen van een gebruiker.

Wanneer een gebruiker wordt verwijderd, worden alle licenties die door de gebruiker worden verbruikt, beschikbaar gesteld voor andere gebruikers.

>[!Note]
>U moet Windows Server Active Directory gebruiken om de identiteits-, contactgegevens- of taakgegevens bij te werken voor gebruikers waarvan de bron van de autoriteit Windows Server Active Directory is. Nadat u de update hebt voltooid, moet u wachten tot de volgende synchronisatiecyclus is voltooid voordat u de wijzigingen ziet.

## <a name="next-steps"></a>Volgende stappen

Nadat u uw gebruikers hebt toegevoegd, u de volgende basisprocessen doen:

- [Profielgegevens toevoegen of wijzigen](active-directory-users-profile-azure-portal.md)

- [Rollen toewijzen aan gebruikers](active-directory-users-assign-role-azure-portal.md)

- [Een basisgroep maken en leden toevoegen](active-directory-groups-create-azure-portal.md)

- [Werken met dynamische groepen en gebruikers](../users-groups-roles/groups-create-rule.md)

U ook andere gebruikersbeheertaken uitvoeren, zoals [het toevoegen van gastgebruikers uit een andere map](../b2b/what-is-b2b.md) of het herstellen van een verwijderde [gebruiker.](active-directory-users-restore.md) Zie [Azure Active Directory-gebruikersbeheerdocumentatie](../users-groups-roles/index.yml)voor meer informatie over andere beschikbare acties.
