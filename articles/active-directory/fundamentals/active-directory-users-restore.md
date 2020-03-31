---
title: Onlangs verwijderde gebruiker herstellen of permanent verwijderen - Azure AD
description: Herstelbare gebruikers weergeven, een verwijderde gebruiker herstellen of een gebruiker permanent verwijderen met Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 573269da1ca6b9ee09b493f4e758e78121d6c2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422864"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Een onlangs verwijderde gebruiker herstellen of verwijderen met Azure Active Directory
Nadat u een gebruiker hebt verwijderd, blijft het account gedurende 30 dagen in een opgeschorte staat. Tijdens dat 30-daagse venster kan het gebruikersaccount worden hersteld, samen met al zijn eigenschappen. Na dat 30-dagen venster passeert, wordt de gebruiker automatisch en permanent verwijderd.

U uw herstelbare gebruikers bekijken, een verwijderde gebruiker herstellen of een gebruiker permanent verwijderen met Azure Active Directory (Azure AD) in de Azure-portal.

>[!Important]
>U en Microsoft-klantenondersteuning kunnen een definitief verwijderde gebruiker niet herstellen.

## <a name="required-permissions"></a>Vereiste machtigingen
U moet een van de volgende rollen hebben om gebruikers te herstellen en permanent te verwijderen.

- Globale beheerder

- Ondersteuning voor Partner Tier1

- Ondersteuning voor Partner Tier2

- Gebruikersbeheerder

## <a name="view-your-restorable-users"></a>Bekijk uw herstelbare gebruikers
U alle gebruikers zien die minder dan 30 dagen geleden zijn verwijderd. Deze gebruikers kunnen worden hersteld.

### <a name="to-view-your-restorable-users"></a>Uw herstelbare gebruikers bekijken
1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) met een globale beheerdersaccount voor de organisatie.

2. Selecteer **Azure Active Directory,** selecteer **Gebruikers**en selecteer Vervolgens **Verwijderde gebruikers**.

    Bekijk de lijst met gebruikers die beschikbaar zijn om te herstellen.

    ![Gebruikers - Pagina Verwijderde gebruikers, met gebruikers die nog steeds kunnen worden hersteld](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Een onlangs verwijderde gebruiker herstellen

Wanneer een gebruikersaccount uit de organisatie wordt verwijderd, bevindt het account zich in een opgeschorte status en worden alle gerelateerde organisatiegegevens bewaard. Wanneer u een gebruiker herstelt, wordt deze organisatiegegevens ook hersteld.

> [!Note]
> Zodra een gebruiker is hersteld, worden licenties die op het moment van verwijdering aan de gebruiker zijn toegewezen, ook hersteld, zelfs als er geen plaatsen beschikbaar zijn voor die licenties. Als u dan meer licenties verbruikt dan u hebt gekocht, kan uw organisatie tijdelijk niet voldoen aan de regels voor licentiegebruik.

### <a name="to-restore-a-user"></a>Een gebruiker herstellen
1. Zoek op de pagina **Gebruikers - Verwijderde gebruikers** naar een van de beschikbare gebruikers en selecteer deze. Bijvoorbeeld, _Mary Parker._

2. Selecteer **Gebruiker herstellen**.

    ![Gebruikers - Pagina Verwijderde gebruikers, met de optie Gebruiker herstellen gemarkeerd](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>Een gebruiker permanent verwijderen
U een gebruiker permanent uit uw organisatie verwijderen zonder de 30 dagen te wachten op automatische verwijdering. Een permanent verwijderde gebruiker kan niet worden hersteld door u, een andere beheerder, noch door microsoft-klantenondersteuning.

>[!Note]
>Als u een gebruiker per ongeluk permanent verwijdert, moet u een nieuwe gebruiker maken en handmatig alle vorige gegevens invoeren. Zie [Gebruikers toevoegen of verwijderen](add-users-azure-active-directory.md)voor meer informatie over het maken van een nieuwe gebruiker.

### <a name="to-permanently-delete-a-user"></a>Een gebruiker permanent verwijderen

1. Zoek op de pagina **Gebruikers - Verwijderde gebruikers** naar een van de beschikbare gebruikers en selecteer deze. Bijvoorbeeld, _Rae Huff_.

2. Selecteer **Permanent verwijderen**selecteren .

    ![Gebruikers - Pagina Verwijderde gebruikers, met de optie Gebruiker herstellen gemarkeerd](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>Volgende stappen
Nadat u uw gebruikers hebt hersteld of verwijderd, u de volgende basisprocessen uitvoeren:

- [Gebruikers toevoegen of verwijderen](add-users-azure-active-directory.md)

- [Rollen toewijzen aan gebruikers](active-directory-users-assign-role-azure-portal.md)

- [Profielgegevens toevoegen of wijzigen](active-directory-users-profile-azure-portal.md)

- [Gastgebruikers van een andere organisatie toevoegen](../b2b/what-is-b2b.md)

Voor meer informatie over andere beschikbare gebruikersbeheertaken, [Azure AD-documentatie voor gebruikersbeheer](../users-groups-roles/index.yml).
