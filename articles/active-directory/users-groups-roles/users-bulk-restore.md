---
title: Het bulksgewijs herstellen van verwijderde gebruikers (preview) in de Azure Active Directory Portal | Microsoft Docs
description: Verwijderde gebruikers bulksgewijs herstellen in het Azure AD-beheer centrum in Azure Active Directory
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
ms.openlocfilehash: f53ade09c5e2e7db0499122526a1de482af9378f
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901618"
---
# <a name="bulk-restore-deleted-users-preview-in-azure-active-directory"></a>Het bulksgewijs herstellen van verwijderde gebruikers (preview) in Azure Active Directory

Azure Active Directory (Azure AD) ondersteunt bulk bewerkingen voor het maken en verwijderen van gebruikers, het bulksgewijs uitnodigen voor gasten en biedt ondersteuning voor het downloaden van lijsten met gebruikers, groepen en groeps leden.

## <a name="to-bulk-restore-users"></a>Gebruikers bulksgewijs herstellen

1. [Meld u aan bij uw Azure AD-organisatie](https://aad.portal.azure.com) met een account dat een gebruikers beheerder in de organisatie is.
1. Selecteer in azure AD de optie **gebruikers** > **verwijderd**.
1. Op de pagina **Verwijderde gebruikers** selecteert u **bulk herstel** om een geldig CSV-bestand met eigenschappen te uploaden van de gebruikers die u wilt herstellen.

   ![Selecteer de opdracht bulk herstel op de pagina verwijderde gebruikers](./media/users-bulk-restore/bulk-restore.png)

1. Wanneer u klaar bent met het bewerken van het CSV-bestand of als u een van uw eigen kant-en-klaar bent om te uploaden, selecteert u het bestand onder **uw CSV-bestand uploaden** dat moet worden gevalideerd.

   ![Selecteer een lokaal CSV-bestand waarin de gebruikers worden vermeld die u wilt toevoegen](./media/users-bulk-restore/upload-button.png)

1. Wanneer de bestands inhoud is gevalideerd, verhelpt u de bestands gegevens en verzendt u het bestand opnieuw als er fouten zijn opgetreden. Als u een geldig bestand verzendt, wordt de taak voor het uploaden van gegevens automatisch gestart.
1. Nadat het CSV-bestand is gevalideerd, selecteert u **verzenden** om de Azure batch-taak te starten die de gebruikers herstelt. Als er fouten zijn, kunt u het bestand met resultaten downloaden en weer geven op de pagina resultaten van bulk bewerking. Het bestand bevat de reden voor elke fout.

## <a name="check-status"></a>Status controleren

U kunt de status van al uw bulk aanvragen in behandeling bekijken op de pagina **resultaten van bulk bewerking (preview)** .

   ![Controleer de upload status op de pagina resultaten van bulk bewerking](./media/users-bulk-restore/bulk-center.png)

Vervolgens kunt u controleren of de gebruikers die u hebt hersteld, bestaan in de Azure AD-organisatie in de Azure Portal of met behulp van Power shell.

## <a name="view-restored-users-in-the-azure-portal"></a>Herstelde gebruikers weer geven in de Azure Portal

1. [Meld u aan bij het Azure AD-beheer centrum](https://aad.portal.azure.com) met een account dat een gebruikers beheerder in de organisatie is.
1. Selecteer **Azure Active Directory**in het navigatie deel venster.
1. Onder **Beheren**, selecteer **Gebruikers**.
1. Selecteer onder **weer geven** **alle gebruikers** en controleer of de gebruikers die u hebt hersteld, worden weer gegeven.

### <a name="view-users-with-powershell"></a>Gebruikers weer geven met Power shell

Voer de volgende opdracht uit:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

U ziet dat de gebruikers die u hebt hersteld, worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers bulksgewijs importeren](users-bulk-add.md)
- [Gebruikers bulksgewijs verwijderen](users-bulk-delete.md)
- [Lijst met gebruikers downloaden](users-bulk-download.md)
