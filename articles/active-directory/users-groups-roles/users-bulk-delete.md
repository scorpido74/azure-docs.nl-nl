---
title: Bulkgebruikers verwijderen in de Azure Active Directory-portal | Microsoft Documenten
description: Gebruikers in bulk verwijderen in het Azure-beheercentrum in Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: beb8b4f35dc5f02e59cced05a6bcfc235d42f996
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532804"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Bulkgebruikers verwijderen in Azure Active Directory

Met Azure Active Directory(Azure AD)-portal u een groot aantal leden in een groep verwijderen met behulp van een CSV-bestand (comma-separated values) om gebruikers in bulk te verwijderen.

## <a name="to-bulk-delete-users"></a>Gebruikers in bulk verwijderen

1. [Meld u aan bij uw Azure AD-organisatie](https://aad.portal.azure.com) met een account dat een gebruikersbeheerder in de organisatie is.
1. Selecteer in Azure AD De optie **Gebruikersbulk** > **verwijderen**.
1. Selecteer op de **gebruikerspagina Bulk verwijderen** de optie **Downloaden** om een geldig CSV-bestand met gebruikerseigenschappen te ontvangen.

   ![Selecteer een lokaal CSV-bestand waarin u de gebruikers aanvermeldt die u wilt verwijderen](./media/users-bulk-delete/bulk-delete.png)

1. Open het CSV-bestand en voeg een regel toe voor elke gebruiker die u wilt verwijderen. De enige vereiste waarde is **De hoofdnaam van de gebruiker**. Sla het bestand op.

   ![Het CSV-bestand bevat namen en namen van de gebruikers om te verwijderen](./media/users-bulk-delete/delete-csv-file.png)

1. Blader op de **gebruikerspagina Bulk verwijderen** onder **Uw csv-bestand uploaden**naar het bestand. Wanneer u het bestand selecteert en op Verzenden klikt, wordt de validatie van het CSV-bestand gestart.
1. Wanneer de inhoud van het bestand is gevalideerd, wordt **bestand geüpload**. Als er fouten zijn, moet u deze oplossen voordat u de taak indienen.
1. Wanneer uw bestand de validatie doorstaat, selecteert u **Verzenden** om de Azure-bulkbewerking te starten die de gebruikers verwijdert.
1. Wanneer de verwijderingsbewerking is voltooid, ziet u een melding dat de bulkbewerking is geslaagd.

Als er fouten zijn, u het resultatenbestand downloaden en bekijken op de pagina **Bulk-bewerkingsresultaten.** Het bestand bevat de reden voor elke fout.

## <a name="check-status"></a>Status controleren

U de status van al uw in behandeling zijnde bulkaanvragen bekijken op de pagina **Bulk-bewerkingsresultaten.**

   [![](media/users-bulk-delete/bulk-center.png "Check delete status in the Bulk Operations Results page")](media/users-bulk-delete/bulk-center.png#lightbox)

Vervolgens u controleren of de gebruikers die u hebt verwijderd, in de Azure AD-organisatie bestaan in de Azure-portal of met PowerShell.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Verwijderde gebruikers in de Azure-portal verifiëren

1. Meld u aan bij de Azure-portal met een account dat een gebruikersbeheerder in de organisatie is.
1. Selecteer Azure Active **Directory**in het navigatiedeelvenster .
1. Onder **Beheren**, selecteer **Gebruikers**.
1. Selecteer **onder Weergeven**alleen **alle gebruikers** en controleer of de gebruikers die u hebt verwijderd niet langer worden vermeld.

### <a name="verify-deleted-users-with-powershell"></a>Verwijderde gebruikers verifiëren met PowerShell

Voer de volgende opdracht uit:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Controleer of de gebruikers die u hebt verwijderd, niet langer worden vermeld.

## <a name="next-steps"></a>Volgende stappen

- [Bulkgebruikers toevoegen](users-bulk-add.md)
- [Lijst met gebruikers downloaden](users-bulk-download.md)
- [Bulkherstelgebruikers](users-bulk-restore.md)
