---
title: Groepleden in bulk verwijderen door een CSV-bestand te uploaden - Azure Active Directory | Microsoft Documenten
description: Groepsleden in bulkbewerkingen verwijderen in het Azure-beheercentrum.
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
ms.openlocfilehash: 1e6d0752245e3864a8ad25efd5181d5cc1eec7ae
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533246"
---
# <a name="bulk-remove-group-members-in-azure-active-directory"></a>Groepleden in Bulk verwijderen in Azure Active Directory

Met Azure Active Directory(Azure AD)-portal u een groot aantal leden uit een groep verwijderen met behulp van een CSV-bestand (comma-separated values) om groepsleden in bulk te verwijderen.

## <a name="to-bulk-remove-group-members"></a>Groepsleden in bulk verwijderen

1. Meld u aan bij [de Azure-portal](https://portal.azure.com) met een gebruikersbeheerdersaccount in de organisatie. Groepseigenaren kunnen ook leden van groepen die ze bezitten bulk verwijderen.
1. Selecteer in Azure AD **Alle** > **groepen groepen**.
1. Open de groep waaruit u leden verwijdert en selecteer **vervolgens Leden**.
1. Selecteer **op** de pagina Leden de optie **Leden verwijderen**.
1. Selecteer op de pagina **Groepleden bulk verwijderen** de optie **Downloaden** om de CSV-bestandssjabloon met vereiste groepslideigenschappen op te halen.

   ![De opdracht Leden verwijderen staat op de profielpagina voor de groep](./media/groups-bulk-remove-members/remove-panel.png)

1. Open het CSV-bestand en voeg een regel toe voor elk groepslid dat u uit de groep wilt verwijderen (vereiste waarden zijn lidobject-id of gebruikersnaam). Sla het bestand op.

   ![Het CSV-bestand bevat namen en namen die de leden kunnen verwijderen](./media/groups-bulk-remove-members/csv-file.png)

1. Blader op de pagina **Groepleden bulk verwijderen** onder **Uw csv-bestand uploaden**naar het bestand. Wanneer u het bestand selecteert, wordt de validatie van het CSV-bestand gestart.
1. Wanneer de inhoud van het bestand is gevalideerd, wordt op de pagina voor bulkimport **bestand geüpload.** Als er fouten zijn, moet u deze oplossen voordat u de taak indienen.
1. Wanneer uw bestand de validatie doorstaat, selecteert u **Verzenden** om de Azure-bulkbewerking te starten waarmee de groepsleden uit de groep worden verwijderd.
1. Wanneer de verwijderingsbewerking is voltooid, ziet u een melding dat de bulkbewerking is geslaagd.

## <a name="check-removal-status"></a>Verwijderstatus controleren

U de status van al uw in behandeling zijnde bulkaanvragen bekijken op de pagina **Bulk-bewerkingsresultaten.**

[![](media/groups-bulk-remove-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-remove-members/bulk-center.png#lightbox)

Selecteer de waarden onder de kolommen **# Succes**, **# Mislukt**of **Totaalaantal aanvragen** voor meer informatie over elk regelitem binnen de bulkbewerking. Als er fouten zijn opgetreden, worden de redenen voor de fout weergegeven.

## <a name="bulk-removal-service-limits"></a>Limieten voor het verwijderen van bulk

Elke bulkactiviteit om een lijst met groepsleden te verwijderen, kan maximaal een uur duren. Hierdoor kan een lijst van ten minste 40.000 leden worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

- [Leden van bulkimportgroepen](groups-bulk-import-members.md)
- [Leden van een groep downloaden](groups-bulk-download-members.md)
- [Een lijst met alle groepen downloaden](groups-bulk-download.md)
