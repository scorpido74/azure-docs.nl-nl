---
title: Bulksgewijs importeren om gebruikers toe te voegen (preview) in de Azure Active Directory-Portal | Microsoft Docs
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
ms.openlocfilehash: a22052117448cdb71bbc16e1df7899e6d3eb764e
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901488"
---
# <a name="bulk-import-users-preview-in-azure-active-directory"></a>Gebruikers bulksgewijs importeren (preview) in Azure Active Directory

Azure Active Directory (Azure AD) ondersteunt bulk bewerkingen voor het maken en verwijderen van gebruikers, het bulksgewijs uitnodigen voor gasten en biedt ondersteuning voor het downloaden van lijsten met gebruikers, groepen en groeps leden.

## <a name="bulk-import-service-limits"></a>Service limieten voor bulk import

Elke bulk activiteit voor het maken van gebruikers kan Maxi maal één uur worden uitgevoerd. Dit maakt het bulksgewijs maken van ten minste 50.000 gebruikers mogelijk.

## <a name="required-permissions"></a>Vereiste machtigingen

Als u meerdere gebruikers wilt maken in de beheer Portal, moet u zijn aangemeld als globale beheerder of gebruikers beheerder.

## <a name="to-bulk-import-users"></a>Gebruikers bulksgewijs importeren

1. [Meld u aan bij uw Azure AD-organisatie](https://aad.portal.azure.com) met een account dat een gebruikers beheerder in de organisatie is.
1. Selecteer in azure AD de optie **gebruikers** > **bulksgewijs maken**.
1. Selecteer op de pagina **bulk maken gebruiker** **downloaden** om een geldig CSV-bestand (Comma-Separated Values) van gebruikers eigenschappen te ontvangen en voeg vervolgens de nieuwe gebruikers toe.
1. Wanneer u klaar bent met het bewerken van het CSV-bestand of als u een van uw eigen kant-en-klaar bent om te uploaden, selecteert u het bestand onder **uw CSV-bestand uploaden** dat moet worden gevalideerd.

   ![Selecteer een lokaal CSV-bestand waarin de gebruikers worden vermeld die u wilt toevoegen](./media/users-bulk-add/upload-button.png)

1. Wanneer de bestands inhoud is gevalideerd, moet u eventuele fouten oplossen voordat u de upload taak kunt starten.
1. Wanneer de validatie van uw bestand wordt door gegeven, selecteert u **verzenden** om de Azure batch-taak te starten die de nieuwe gebruikers gegevens toevoegt. Als er fouten zijn, kunt u het bestand met resultaten downloaden en weer geven op de pagina resultaten van bulk bewerking. Het bestand bevat de reden voor elke fout.

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

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers bulksgewijs verwijderen](users-bulk-delete.md)
- [Lijst met gebruikers downloaden](users-bulk-download.md)
- [Gebruikers bulksgewijs herstellen](users-bulk-restore.md)
