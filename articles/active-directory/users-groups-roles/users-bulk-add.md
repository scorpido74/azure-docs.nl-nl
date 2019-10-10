---
title: Bulksgewijs maken van gebruikers (preview) in de Azure Active Directory-Portal | Microsoft Docs
description: Gebruikers bulksgewijs toevoegen in het Azure AD-beheer centrum in Azure Active Directory
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
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174321"
---
# <a name="bulk-create-users-preview-in-azure-active-directory"></a>Bulksgewijs maken van gebruikers (preview) in Azure Active Directory

Azure Active Directory (Azure AD) ondersteunt bulk bewerkingen voor het maken en verwijderen van gebruikers, het bulksgewijs uitnodigen voor gasten en biedt ondersteuning voor het downloaden van lijsten met gebruikers, groepen en groeps leden.

## <a name="required-permissions"></a>Vereiste machtigingen

Als u meerdere gebruikers wilt maken in de beheer Portal, moet u zijn aangemeld als globale beheerder of gebruikers beheerder.

## <a name="to-create-users-in-bulk"></a>Gebruikers bulksgewijs maken

1. [Meld u aan bij uw Azure AD-organisatie](https://aad.portal.azure.com) met een account dat een gebruikers beheerder in de organisatie is.
1. Selecteer in azure AD de optie **gebruikers** > **bulksgewijze maken**.
1. Selecteer op de pagina **bulk maken gebruiker** **downloaden** om een geldig CSV-bestand (Comma-Separated Values) van gebruikers eigenschappen te ontvangen en voeg vervolgens gebruikers toevoegen toe die u wilt maken.

   ![Selecteer een lokaal CSV-bestand waarin de gebruikers worden vermeld die u wilt toevoegen](./media/users-bulk-add/upload-button.png)

1. Open het CSV-bestand en voeg een regel toe voor elke gebruiker die u wilt maken. De enige vereiste waarden zijn **naam**, **UPN (User Principal name**), **eerste wacht woord** en **aanmeld gegevens blok keren (Ja/Nee)** . Sla het bestand op.

   ![Het CSV-bestand bevat namen en Id's van de gebruikers die moeten worden gemaakt](./media/users-bulk-add/add-csv-file.png)

1. Blader op de pagina **bulk maken gebruiker (preview)** onder uw CSV-bestand uploaden naar het bestand. Wanneer u het bestand selecteert en op **verzenden**klikt, wordt de validatie van het CSV-bestand gestart.
1. Nadat de bestands inhoud is gevalideerd, ziet u dat het **bestand is geüpload**. Als er fouten zijn, moet u deze oplossen voordat u de taak kunt indienen.
1. Wanneer de validatie van uw bestand wordt door gegeven, selecteert u **verzenden** om de Azure bulk bewerking te starten waarmee de nieuwe gebruikers worden geïmporteerd.
1. Wanneer de import bewerking is voltooid, ziet u een melding van de taak status van de bulk bewerking.

Als er fouten zijn, kunt u het bestand met resultaten downloaden en weer geven op de pagina **resultaten van bulk bewerking** . Het bestand bevat de reden voor elke fout.

## <a name="check-status"></a>Status controleren

U kunt de status van al uw bulk aanvragen in behandeling bekijken op de pagina **resultaten van bulk bewerking (preview)** .

   ![Controleer de upload status op de pagina resultaten van bulk bewerking](./media/users-bulk-add/bulk-center.png)

Vervolgens kunt u controleren of de gebruikers die u hebt gemaakt in de Azure AD-organisatie zijn opgenomen in de Azure Portal of met behulp van Power shell.

## <a name="verify-users-in-the-azure-portal"></a>Gebruikers in het Azure Portal controleren

1. [Meld u aan bij het Azure AD-beheer centrum](https://aad.portal.azure.com) met een account dat een gebruikers beheerder in de organisatie is.
1. Selecteer **Azure Active Directory**in het navigatie deel venster.
1. Onder **Beheren**, selecteer **Gebruikers**.
1. Selecteer onder **weer geven** **alle gebruikers** en controleer of de gebruikers die u hebt gemaakt, worden weer gegeven.

### <a name="verify-users-with-powershell"></a>Gebruikers controleren met Power shell

Voer de volgende opdracht uit:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

U ziet dat de gebruikers die u hebt gemaakt, worden weer gegeven.

## <a name="bulk-import-service-limits"></a>Service limieten voor bulk import

Elke bulk activiteit voor het maken van gebruikers kan Maxi maal één uur worden uitgevoerd. Dit maakt het bulksgewijs maken van ten minste 50.000 gebruikers mogelijk.

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers bulksgewijs verwijderen](users-bulk-delete.md)
- [Lijst met gebruikers downloaden](users-bulk-download.md)
- [Gebruikers bulksgewijs herstellen](users-bulk-restore.md)
