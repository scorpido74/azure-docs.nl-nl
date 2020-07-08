---
title: Gebruikers bulksgewijs maken in de Azure Active Directory Portal | Microsoft Docs
description: Gebruikers bulksgewijs toevoegen in het Azure AD-beheer centrum in Azure Active Directory
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
ms.openlocfilehash: 6a2c9500ecefed02b28c066de80137d8f0882fd5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84731500"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>Gebruikers bulksgewijs maken in Azure Active Directory

Azure Active Directory (Azure AD) ondersteunt bulk bewerkingen voor het maken en verwijderen van gebruikers en ondersteunt het downloaden van lijsten van gebruikers. U hoeft alleen maar een door komma's gescheiden sjabloon (CSV) in te vullen, die u kunt downloaden via de Azure AD-Portal.

## <a name="required-permissions"></a>Vereiste machtigingen

Als u meerdere gebruikers wilt maken in de beheer Portal, moet u zijn aangemeld als globale beheerder of gebruikers beheerder.

## <a name="understand-the-csv-template"></a>De CSV-sjabloon begrijpen

Down load en vul het CSV-sjabloon bulksgewijs uploaden in om Azure AD-gebruikers bulksgewijs te kunnen maken. De CSV-sjabloon die u downloadt, kan eruitzien als in dit voor beeld:

![Werk blad voor upload-en aanroep-outs waarin het doel en de waarden voor elke rij en kolom worden uitgelegd](./media/users-bulk-add/create-template-example.png)

### <a name="csv-template-structure"></a>Structuur van CSV-sjabloon

De rijen in een gedownloade CSV-sjabloon zijn als volgt:

- **Versie nummer**: de eerste rij met het versie nummer moet worden opgenomen in de upload CSV.
- **Kolom koppen**: de indeling van de kolom koppen is de &lt; *item naam* &gt; [propertyname] &lt; *vereist of leeg* &gt; . Bijvoorbeeld `Name [displayName] Required`. Sommige oudere versies van de sjabloon hebben mogelijk een kleine variatie.
- **Voor beelden van rij**: we hebben in de sjabloon een rij met voor beelden van geldige waarden voor elke kolom opgenomen. U moet de voor beelden van de rij verwijderen en vervangen door uw eigen vermeldingen.

### <a name="additional-guidance"></a>Aanvullende richtlijnen

- De eerste twee rijen van de upload sjabloon mogen niet worden verwijderd of gewijzigd, of de upload kan niet worden verwerkt.
- De vereiste kolommen worden eerst weer gegeven.
- Het is niet raadzaam om nieuwe kolommen toe te voegen aan de sjabloon. Alle extra kolommen die u toevoegt, worden genegeerd en worden niet verwerkt.
- U wordt aangeraden de meest recente versie van de CSV-sjabloon zo vaak mogelijk te downloaden.

## <a name="to-create-users-in-bulk"></a>Gebruikers bulksgewijs maken

1. [Meld u aan bij uw Azure AD-organisatie](https://aad.portal.azure.com) met een account dat een gebruikers beheerder in de organisatie is.
1. Selecteer in azure AD de optie **gebruikers**  >  **bulksgewijs maken**.
1. Selecteer op de pagina **bulk maken gebruiker** **downloaden** om een geldig CSV-bestand (Comma-Separated Values) van gebruikers eigenschappen te ontvangen en voeg vervolgens gebruikers toevoegen toe die u wilt maken.

   ![Selecteer een lokaal CSV-bestand waarin de gebruikers worden vermeld die u wilt toevoegen](./media/users-bulk-add/upload-button.png)

1. Open het CSV-bestand en voeg een regel toe voor elke gebruiker die u wilt maken. De enige vereiste waarden zijn **naam**, **UPN (User Principal name**), **eerste wacht woord** en **aanmeld gegevens blok keren (Ja/Nee)**. Sla het bestand op.

   [![](media/users-bulk-add/add-csv-file.png "The CSV file contains names and IDs of the users to create")](media/users-bulk-add/add-csv-file.png#lightbox)

1. Blader op de pagina **bulk maken gebruiker** onder uw CSV-bestand uploaden naar het bestand. Wanneer u het bestand selecteert en op **verzenden**klikt, wordt de validatie van het CSV-bestand gestart.
1. Nadat de bestands inhoud is gevalideerd, ziet u dat het **bestand is geüpload**. Als er fouten zijn, moet u deze oplossen voordat u de taak kunt indienen.
1. Wanneer de validatie van uw bestand wordt door gegeven, selecteert u **verzenden** om de Azure bulk bewerking te starten waarmee de nieuwe gebruikers worden geïmporteerd.
1. Wanneer de import bewerking is voltooid, ziet u een melding van de taak status van de bulk bewerking.

Als er fouten zijn, kunt u het bestand met resultaten downloaden en weer geven op de pagina **resultaten van bulk bewerking** . Het bestand bevat de reden voor elke fout. Het verzenden van bestanden moet overeenkomen met de opgegeven sjabloon en de exacte kolom namen bevatten.

## <a name="check-status"></a>Status controleren

U kunt de status van al uw bulk aanvragen in behandeling bekijken op de pagina **resultaten van bulk bewerking** .

   [![](media/users-bulk-add/bulk-center.png "Check create status in the Bulk Operations Results page")](media/users-bulk-add/bulk-center.png#lightbox)

Vervolgens kunt u controleren of de gebruikers die u hebt gemaakt in de Azure AD-organisatie zijn opgenomen in de Azure Portal of met behulp van Power shell.

## <a name="verify-users-in-the-azure-portal"></a>Gebruikers in het Azure Portal controleren

1. [Meld u aan bij het Azure AD-beheer centrum](https://aad.portal.azure.com) met een account dat een gebruikers beheerder in de organisatie is.
1. Selecteer **Azure Active Directory**in het navigatie deel venster.
1. Selecteer onder **Beheren** de optie **Gebruikers**.
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
