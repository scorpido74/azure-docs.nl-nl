---
title: Gebruikers bulksgewijs verwijderen (preview) in de Azure Active Directory-Portal | Microsoft Docs
description: Gebruikers bulksgewijs verwijderen in het Azure-beheer centrum in Azure Active Directory
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
ms.openlocfilehash: 6c2204c572ca1f74f8060d0b6176df69359fe69a
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901709"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>Gebruikers bulksgewijs verwijderen (preview) in Azure Active Directory

Azure Active Directory (Azure AD) ondersteunt bulk bewerkingen voor het maken en verwijderen van gebruikers, het bulksgewijs uitnodigen voor gasten en biedt ondersteuning voor het downloaden van lijsten met gebruikers, groepen en groeps leden.

## <a name="to-bulk-delete-users"></a>Gebruikers bulksgewijs verwijderen

1. Meld u aan bij uw Azure AD-organisatie met een account dat een gebruikers beheerder in de organisatie is.
1. Selecteer in azure AD de optie **gebruikers** > **bulksgewijs verwijderen**.
1. Selecteer op de pagina **bulksgewijs verwijderen gebruiker** **downloaden** om een geldig CSV-bestand met gebruikers eigenschappen te ontvangen en voeg vervolgens de gebruikers toe die u wilt verwijderen.
1. Wanneer u klaar bent met het bewerken van het CSV-bestand of als u een van uw eigen kant-en-klaar bent om te uploaden, selecteert u het bestand onder **uw CSV-bestand uploaden** dat moet worden gevalideerd.

   ![Selecteer een lokaal CSV-bestand waarin de gebruikers die u wilt verwijderen, worden vermeld](./media/users-bulk-delete/bulk-delete.png)

1. Wanneer de bestands inhoud is gevalideerd, moet u eventuele fouten corrigeren voordat de taak wordt verzonden.
1. Wanneer de validatie van uw bestand wordt door gegeven, selecteert u **verzenden** om de Azure batch-taak te starten waarmee de gebruikers worden verwijderd. Als er fouten zijn, kunt u het bestand met resultaten downloaden en weer geven op de pagina resultaten van bulk bewerking. Het bestand bevat de reden voor elke fout.

## <a name="check-status"></a>Status controleren

U kunt de status van al uw bulk aanvragen in behandeling bekijken op de pagina **resultaten van bulk bewerking (preview)** .

   ![Controleer de upload status op de pagina resultaten van bulk bewerking](./media/users-bulk-delete/bulk-center.png)

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
