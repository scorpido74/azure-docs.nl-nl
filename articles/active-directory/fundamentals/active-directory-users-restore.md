---
title: Onlangs verwijderde gebruiker herstellen of permanent verwijderen-Azure AD
description: Informatie over het weergeven van gebruikers die kunnen worden hersteld, een verwijderde gebruiker herstellen of permanent verwijderen van een gebruiker met Azure Active Directory.
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422864"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Herstellen of verwijderen van een onlangs verwijderde gebruiker met behulp van Azure Active Directory
Nadat u een gebruiker verwijdert, wordt het account de status onderbroken blijft gedurende 30 dagen. Tijdens deze periode 30-daagse kan account van de gebruiker worden hersteld, samen met de eigenschappen ervan. Als dit venster 30 dagen is verstreken, de gebruiker wordt automatisch en definitief verwijderd.

U kunt weergeven van uw gebruikers te herstellen, een verwijderde gebruiker herstellen of permanent verwijderen van een gebruiker met behulp van Azure Active Directory (Azure AD) in Azure portal.

>[!Important]
>U noch klantondersteuning van Microsoft kunt herstellen van een gebruiker is definitief verwijderd.

## <a name="required-permissions"></a>Vereiste machtigingen
U moet een van de volgende rollen te herstellen en gebruikers permanent verwijderen.

- Globale beheerder

- Laag1-ondersteuning voor partner

- Laag2-ondersteuning voor partner

- Gebruikers beheerder

## <a name="view-your-restorable-users"></a>Uw die kunnen worden hersteld gebruikers weergeven
Hier ziet u alle gebruikers die minder dan 30 dagen geleden zijn verwijderd. Deze gebruikers kunnen worden hersteld.

### <a name="to-view-your-restorable-users"></a>Om terug te zetten gebruikers weer te geven
1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met een algemeen beheerders account voor de organisatie.

2. Selecteer **Azure Active Directory**, selecteer **gebruikers**, en selecteer vervolgens **verwijderde gebruikers**.

    Bekijk de lijst met gebruikers die beschikbaar zijn voor het herstellen.

    ![Gebruikers - pagina van de verwijderde gebruikers, aan gebruikers die nog steeds kunnen worden hersteld.](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Een onlangs verwijderde gebruiker herstellen

Wanneer een gebruikers account wordt verwijderd uit de organisatie, bevindt het account zich in een onderbroken staat en blijven alle gerelateerde organisatie gegevens bewaard. Wanneer u een gebruiker herstelt, wordt deze informatie over de organisatie ook hersteld.

> [!Note]
> Zodra een gebruiker is hersteld, worden de licenties die zijn toegewezen aan de gebruiker op het moment van verwijderen ook hersteld, zelfs als er geen stoelen beschikbaar zijn voor deze licenties. Als u vervolgens meer licenties gebruikt dan u hebt aangeschaft, is uw organisatie mogelijk tijdelijk niet in overeenstemming voor het gebruik van licenties.

### <a name="to-restore-a-user"></a>Een gebruiker moet herstellen
1. Op de **gebruikers - verwijderde gebruikers** pagina, zoek en selecteer een van de beschikbare gebruikers. Bijvoorbeeld, _Mary Parker_.

2. Selecteer **terugzetten gebruiker**.

    ![Gebruikers - pagina van de verwijderde gebruikers, met de optie gebruiker herstellen gemarkeerd](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>Een gebruiker permanent verwijderen
U kunt een gebruiker permanent verwijderen uit uw organisatie zonder de 30 dagen te wachten voor automatisch verwijderen. Een gebruiker is definitief verwijderd kan niet worden hersteld door u, als een andere beheerder, of door de klantondersteuning van Microsoft.

>[!Note]
>Als u een gebruiker permanent per ongeluk hebt verwijdert, hebt u een nieuwe gebruiker maken en de vorige gegevens handmatig invoeren. Zie voor meer informatie over het maken van een nieuwe gebruiker [toevoegen of verwijderen gebruikers](add-users-azure-active-directory.md).

### <a name="to-permanently-delete-a-user"></a>Een gebruiker permanent verwijderen

1. Op de **gebruikers - verwijderde gebruikers** pagina, zoek en selecteer een van de beschikbare gebruikers. Bijvoorbeeld, _Rae Huff_.

2. Selecteer **definitief verwijderen**.

    ![Gebruikers - pagina van de verwijderde gebruikers, met de optie gebruiker herstellen gemarkeerd](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>Volgende stappen
Nadat u hebt hersteld of verwijderd van uw gebruikers, kunt u de volgende basis-processen uitvoeren:

- [Toevoegen of verwijderen van gebruikers](add-users-azure-active-directory.md)

- [Rollen toewijzen aan gebruikers](active-directory-users-assign-role-azure-portal.md)

- [Toevoegen of wijzigen van de profielgegevens](active-directory-users-profile-azure-portal.md)

- [Gast gebruikers toevoegen vanuit een andere organisatie](../b2b/what-is-b2b.md)

Voor meer informatie over andere beschik bare taken voor gebruikers beheer, de [documentatie voor Azure AD-gebruikers beheer](../users-groups-roles/index.yml).
