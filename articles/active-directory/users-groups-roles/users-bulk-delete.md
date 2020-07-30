---
title: Gebruikers bulksgewijs verwijderen in de Azure Active Directory Portal | Microsoft Docs
description: Gebruikers bulksgewijs verwijderen in het Azure-beheer centrum in Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82304052d6ed0c0169c24c19c58d53548b4479cc
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423589"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Gebruikers bulksgewijs verwijderen in Azure Active Directory

Met Azure Active Directory-Portal (Azure AD) kunt u een groot aantal leden verwijderen uit een groep met behulp van een bestand met door komma's gescheiden waarden (CSV) om gebruikers bulksgewijs te verwijderen.

## <a name="understand-the-csv-template"></a>De CSV-sjabloon begrijpen

Down load en vul de CSV-sjabloon in om Azure AD-gebruikers bulksgewijs te kunnen verwijderen. De CSV-sjabloon die u downloadt, kan eruitzien als in dit voor beeld:

![Werk blad voor upload-en aanroep-outs waarin het doel en de waarden voor elke rij en kolom worden uitgelegd](./media/users-bulk-delete/understand-template.png)

### <a name="csv-template-structure"></a>Structuur van CSV-sjabloon

De rijen in een gedownloade CSV-sjabloon zijn als volgt:

- **Versie nummer**: de eerste rij met het versie nummer moet worden opgenomen in de upload CSV.
- **Kolom koppen**: de indeling van de kolom koppen is de &lt; *item naam* &gt; [propertyname] &lt; *vereist of leeg* &gt; . Bijvoorbeeld `User name [userPrincipalName] Required`. Sommige oudere versies van de sjabloon hebben mogelijk een kleine variatie.
- **Voor beelden van rij**: we hebben in de sjabloon een rij met voor beelden van geldige waarden voor elke kolom opgenomen. U moet de voor beelden van de rij verwijderen en vervangen door uw eigen vermeldingen.

### <a name="additional-guidance"></a>Aanvullende richtlijnen

- De eerste twee rijen van de upload sjabloon mogen niet worden verwijderd of gewijzigd, of de upload kan niet worden verwerkt.
- De vereiste kolommen worden eerst weer gegeven.
- Het is niet raadzaam om nieuwe kolommen toe te voegen aan de sjabloon. Alle extra kolommen die u toevoegt, worden genegeerd en worden niet verwerkt.
- U wordt aangeraden de meest recente versie van de CSV-sjabloon zo vaak mogelijk te downloaden.

## <a name="to-bulk-delete-users"></a>Gebruikers bulksgewijs verwijderen

1. [Meld u aan bij uw Azure AD-organisatie](https://aad.portal.azure.com) met een account dat een gebruikers beheerder in de organisatie is.
1. Selecteer in azure AD de optie **gebruikers**  >  **bulksgewijs verwijderen**.
1. Selecteer op de pagina **bulksgewijs verwijderen gebruiker** **downloaden** om een geldig CSV-bestand met gebruikers eigenschappen te ontvangen.

   ![Selecteer een lokaal CSV-bestand waarin de gebruikers die u wilt verwijderen, worden vermeld](./media/users-bulk-delete/bulk-delete.png)

1. Open het CSV-bestand en voeg een regel toe voor elke gebruiker die u wilt verwijderen. De enige vereiste waarde is **User Principal name**. Sla het bestand op.

   ![Het CSV-bestand bevat de namen en Id's van de gebruikers die u wilt verwijderen](./media/users-bulk-delete/delete-csv-file.png)

1. Blader op de pagina **bulksgewijs verwijderen** , onder **uw CSV-bestand uploaden**, naar het bestand. Wanneer u het bestand selecteert en op verzenden klikt, wordt de validatie van het CSV-bestand gestart.
1. Wanneer de bestands inhoud is gevalideerd, ziet u dat het **bestand is geüpload**. Als er fouten zijn, moet u deze oplossen voordat u de taak kunt indienen.
1. Wanneer de validatie van uw bestand wordt door gegeven, selecteert u **verzenden** om de Azure bulk bewerking te starten waarmee de gebruikers worden verwijderd.
1. Wanneer de verwijderings bewerking is voltooid, ziet u een melding dat de bulk bewerking is geslaagd.

Als er fouten zijn, kunt u het bestand met resultaten downloaden en weer geven op de pagina **resultaten van bulk bewerking** . Het bestand bevat de reden voor elke fout.

## <a name="check-status"></a>Status controleren

U kunt de status van al uw bulk aanvragen in behandeling bekijken op de pagina **resultaten van bulk bewerking** .

   [![Controleer de verwijderings status op de pagina resultaten van bulk bewerking.](media/users-bulk-delete/bulk-center.png)](media/users-bulk-delete/bulk-center.png#lightbox)

Vervolgens kunt u controleren of de gebruikers die u hebt verwijderd, bestaan in de Azure AD-organisatie in de Azure Portal of met behulp van Power shell.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Verwijderde gebruikers verifiëren in de Azure Portal

1. Meld u aan bij de Azure Portal met een account dat een gebruikers beheerder in de organisatie is.
1. Selecteer **Azure Active Directory**in het navigatie deel venster.
1. Onder **Beheren**, selecteer **Gebruikers**.
1. Onder **weer geven**selecteert u alleen **alle gebruikers** en controleert u of de gebruikers die u hebt verwijderd, niet meer worden weer gegeven.

### <a name="verify-deleted-users-with-powershell"></a>Verwijderde gebruikers controleren met Power shell

Voer de volgende opdracht uit:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Controleer of de gebruikers die u hebt verwijderd, niet meer worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers bulksgewijs toevoegen](users-bulk-add.md)
- [Lijst met gebruikers downloaden](users-bulk-download.md)
- [Gebruikers bulksgewijs herstellen](users-bulk-restore.md)
