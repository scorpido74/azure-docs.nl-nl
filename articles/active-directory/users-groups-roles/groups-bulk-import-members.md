---
title: Uploaden van bulkimporteren om leden aan een groep toe te voegen - Azure Active Directory | Microsoft Documenten
description: Voeg groepleden in bulk toe in het Azure Active Directory-beheercentrum.
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
ms.openlocfilehash: 15960caa55274f06159263c1af4a6c8280e83f4e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533467"
---
# <a name="bulk-import-group-members-in-azure-active-directory"></a>Groepsleden voor bulkimporteren in Azure Active Directory

Met Azure Active Directory (Azure AD)-portal u een groot aantal leden aan een groep toevoegen met behulp van een CSV-bestand (comma-separated values) aan groepsleden voor bulkimport.

## <a name="to-bulk-import-group-members"></a>Groepsleden voor bulkimporteren

1. Meld u aan bij [de Azure-portal](https://portal.azure.com) met een gebruikersbeheerdersaccount in de organisatie. Groepseigenaren kunnen ook leden van groepen waarvan ze eigenaar zijn bulk importeren.
1. Selecteer in Azure AD **Alle** > **groepen groepen**.
1. Open de groep waaraan u leden toevoegt en selecteer vervolgens **Leden**.
1. Selecteer **op** de pagina Leden de optie **Leden importeren**.
1. Selecteer op de pagina **Bulkgroepleden** downloaden de optie **Downloaden** om de CSV-bestandssjabloon met vereiste groepslideigenschappen op te halen.

    ![De opdracht Leden importeren staat op de profielpagina voor de groep](./media/groups-bulk-import-members/import-panel.png)

1. Open het CSV-bestand en voeg een regel toe voor elk groepslid dat u in de groep wilt importeren (vereiste waarden zijn **lidobject-id** of **gebruikersnaam).** Sla het bestand op.

   ![Het CSV-bestand bevat namen en iD's die de leden kunnen importeren](./media/groups-bulk-import-members/csv-file.png)

1. Blader op de pagina **Groepleden bulk importeren** onder **Uw csv-bestand uploaden**naar het bestand. Wanneer u het bestand selecteert, wordt de validatie van het CSV-bestand gestart.
1. Wanneer de inhoud van het bestand is gevalideerd, wordt op de pagina voor bulkimport **bestand geüpload.** Als er fouten zijn, moet u deze oplossen voordat u de taak indienen.
1. Wanneer uw bestand de validatie doorstaat, selecteert u **Verzenden** om de Azure-bulkbewerking te starten waarmee de groepsleden in de groep worden geïmporteerd.
1. Wanneer de importbewerking is voltooid, ziet u een melding dat de bulkbewerking is geslaagd.

## <a name="check-import-status"></a>Importstatus controleren

U de status van al uw in behandeling zijnde bulkaanvragen bekijken op de pagina **Bulk-bewerkingsresultaten.**

[![](media/groups-bulk-import-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-import-members/bulk-center.png#lightbox)

Selecteer de waarden onder de kolommen **# Succes**, **# Mislukt**of **Totaalaantal aanvragen** voor meer informatie over elk regelitem binnen de bulkbewerking. Als er fouten zijn opgetreden, worden de redenen voor de fout weergegeven.

## <a name="bulk-import-service-limits"></a>Limieten voor bulkimport

Elke bulkactiviteit om een lijst met groepsleden te importeren, kan maximaal een uur duren. Hierdoor kan een lijst van ten minste 40.000 leden worden ingevoerd.

## <a name="next-steps"></a>Volgende stappen

- [Groepsleden in bulk verwijderen](groups-bulk-remove-members.md)
- [Leden van een groep downloaden](groups-bulk-download-members.md)
- [Een lijst met alle groepen downloaden](groups-bulk-download.md)
