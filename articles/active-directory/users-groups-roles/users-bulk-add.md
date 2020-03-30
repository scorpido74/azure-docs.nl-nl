---
title: Bulkgebruikers maken (voorbeeld) in de Azure Active Directory-portal | Microsoft Documenten
description: Gebruikers in bulk toevoegen in het Azure AD-beheercentrum in Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/30/2019
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: a10dfffa69652ee2b75053c04b97f6492c46811e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72174321"
---
# <a name="bulk-create-users-preview-in-azure-active-directory"></a>Bulkmaken gebruikers (preview) in Azure Active Directory

Azure Active Directory (Azure AD) ondersteunt bewerkingen voor het maken en verwijderen van bulkgebruikers, bulkuitnodigingen voor gasten en ondersteunt het downloaden van lijsten met gebruikers, groepen en groepsleden.

## <a name="required-permissions"></a>Vereiste machtigingen

Als u gebruikers in de beheerportal wilt maken, moet u zijn aangemeld als globale beheerder of gebruikersbeheerder.

## <a name="to-create-users-in-bulk"></a>Gebruikers in bulk maken

1. [Meld u aan bij uw Azure AD-organisatie](https://aad.portal.azure.com) met een account dat een gebruikersbeheerder in de organisatie is.
1. Selecteer in Azure AD de optie **Gebruikersbulk** > **maken**.
1. Selecteer op de **gebruikerspagina Bulk maken** de optie **Downloaden** om een CSV-bestand (valid comma-separated values) met gebruikerseigenschappen te ontvangen en voeg vervolgens gebruikers toe die u wilt maken.

   ![Selecteer een lokaal CSV-bestand waarin u de gebruikers aanvermeldt die u wilt toevoegen](./media/users-bulk-add/upload-button.png)

1. Open het CSV-bestand en voeg een regel toe voor elke gebruiker die u wilt maken. De enige vereiste waarden zijn **Naam,** **Gebruikersnaam,** **Initieel wachtwoord** en **Aanmelding blokkeren (Ja/Nee).** Sla het bestand op.

   ![Het CSV-bestand bevat namen en namen van de gebruikers om](./media/users-bulk-add/add-csv-file.png)

1. Blader op de pagina **Bulk-gebruiker (Voorbeeld)** onder Uw CSV-bestand uploaden naar het bestand. Wanneer u het bestand selecteert en op **Verzenden**klikt, wordt de validatie van het CSV-bestand gestart.
1. Nadat de inhoud van het bestand is gevalideerd, wordt **bestand geüpload**. Als er fouten zijn, moet u deze oplossen voordat u de taak indienen.
1. Wanneer uw bestand de validatie doorstaat, selecteert u **Verzenden** om de Azure-bulkbewerking te starten waarmee de nieuwe gebruikers worden geïmporteerd.
1. Wanneer de importbewerking is voltooid, ziet u een melding van de taakstatus van de bulkbewerking.

Als er fouten zijn, u het resultatenbestand downloaden en bekijken op de pagina **Bulk-bewerkingsresultaten.** Het bestand bevat de reden voor elke fout.

## <a name="check-status"></a>Status controleren

U de status van al uw in behandeling zijnde bulkaanvragen bekijken op de pagina **Bulk operation results (preview).**

   ![Uploadstatus controleren op de pagina Resultaten van bulkbewerkingen](./media/users-bulk-add/bulk-center.png)

Vervolgens u controleren of de gebruikers die u hebt gemaakt, in de Azure AD-organisatie bestaan in de Azure-portal of met PowerShell.

## <a name="verify-users-in-the-azure-portal"></a>Gebruikers in de Azure-portal verifiëren

1. [Meld u aan bij het Azure AD-beheercentrum](https://aad.portal.azure.com) met een account dat een gebruikersbeheerder in de organisatie is.
1. Selecteer Azure Active **Directory**in het navigatiedeelvenster .
1. Onder **Beheren**, selecteer **Gebruikers**.
1. Selecteer **onder Weergeven** **alle gebruikers** en controleer of de gebruikers die u hebt gemaakt worden weergegeven.

### <a name="verify-users-with-powershell"></a>Gebruikers verifiëren met PowerShell

Voer de volgende opdracht uit:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

U moet zien dat de gebruikers die u hebt gemaakt, worden weergegeven.

## <a name="bulk-import-service-limits"></a>Limieten voor bulkimport

Elke bulkactiviteit om gebruikers te maken, kan maximaal een uur duren. Dit maakt bulkcreatie van ten minste 50.000 gebruikers mogelijk.

## <a name="next-steps"></a>Volgende stappen

- [Bulk verwijderen gebruikers](users-bulk-delete.md)
- [Lijst met gebruikers downloaden](users-bulk-download.md)
- [Bulkherstelgebruikers](users-bulk-restore.md)
