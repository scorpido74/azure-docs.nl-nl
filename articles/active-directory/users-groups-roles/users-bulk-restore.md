---
title: Verwijderde gebruikers (voorbeeld) in bulkherstellen in de Azure Active Directory-portal | Microsoft Documenten
description: Verwijderde gebruikers in bulk herstellen in het Azure AD-beheercentrum in Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: d392ae97a8325dd4a56acd807ebfb2b951216eae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72174263"
---
# <a name="bulk-restore-deleted-users-preview-in-azure-active-directory"></a>Verwijderde gebruikers (preview) in Bulk herstellen in Azure Active Directory

Azure Active Directory (Azure AD) ondersteunt bewerkingen voor het maken en verwijderen van bulkgebruikers, bulkuitnodigingen voor gasten en ondersteunt het downloaden van lijsten met gebruikers, groepen en groepsleden.

## <a name="to-bulk-restore-users"></a>Bulkgebruikers herstellen

1. [Meld u aan bij uw Azure AD-organisatie](https://aad.portal.azure.com) met een account dat een gebruikersbeheerder is in de Azure AD-organisatie.
1. Selecteer **in** > Azure AD**Gebruikers verwijderd**.
1. Selecteer **op de** pagina Verwijderde gebruikers de optie **Bulkherstel** om een geldig CSV-bestand met eigenschappen van de gebruikers te uploaden om te herstellen.

   ![De opdracht Bulkherstel selecteren op de pagina Verwijderde gebruikers](./media/users-bulk-restore/bulk-restore.png)

1. Open het CSV-bestand en voeg een regel toe voor elke gebruiker die u wilt herstellen. De enige vereiste waarde is **ObjectID**. Sla het bestand op.

   ![Selecteer een lokaal CSV-bestand waarin u de gebruikers aanvermeldt die u wilt toevoegen](./media/users-bulk-restore/upload-button.png)

1. Blader op de pagina **Bulk restore (Preview)** onder **Uw csv-bestand uploaden**naar het bestand. Wanneer u het bestand selecteert en op **Verzenden**klikt, wordt de validatie van het CSV-bestand gestart.
1. Wanneer de inhoud van het bestand is gevalideerd, wordt **bestand geüpload**. Als er fouten zijn, moet u deze oplossen voordat u de taak indienen.
1. Wanneer uw bestand de validatie doorstaat, selecteert u **Verzenden** om de Azure-bulkbewerking te starten waarmee de gebruikers worden hersteld.
1. Wanneer de herstelbewerking is voltooid, ziet u een melding dat de bulkbewerking is geslaagd.

Als er fouten zijn, u het resultatenbestand downloaden en bekijken op de pagina **Bulk-bewerkingsresultaten.** Het bestand bevat de reden voor elke fout.

## <a name="check-status"></a>Status controleren

U de status van al uw in behandeling zijnde bulkaanvragen bekijken op de pagina **Bulk operation results (preview).**

   ![Uploadstatus controleren op de pagina Resultaten van bulkbewerkingen](./media/users-bulk-restore/bulk-center.png)

Vervolgens u controleren of de gebruikers die u hebt hersteld, in de Azure AD-organisatie bestaan in de Azure-portal of met PowerShell.

## <a name="view-restored-users-in-the-azure-portal"></a>Herstelde gebruikers weergeven in de Azure-portal

1. [Meld u aan bij het Azure AD-beheercentrum](https://aad.portal.azure.com) met een account dat een gebruikersbeheerder in de organisatie is.
1. Selecteer Azure Active **Directory**in het navigatiedeelvenster .
1. Onder **Beheren**, selecteer **Gebruikers**.
1. Selecteer **onder Weergeven** **alle gebruikers** en controleer of de gebruikers die u hebt hersteld, worden weergegeven.

### <a name="view-users-with-powershell"></a>Gebruikers weergeven met PowerShell

Voer de volgende opdracht uit:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

U moet zien dat de gebruikers die u hebt hersteld, worden weergegeven.

## <a name="next-steps"></a>Volgende stappen

- [Bulkimportgebruikers](users-bulk-add.md)
- [Bulk verwijderen gebruikers](users-bulk-delete.md)
- [Lijst met gebruikers downloaden](users-bulk-download.md)
