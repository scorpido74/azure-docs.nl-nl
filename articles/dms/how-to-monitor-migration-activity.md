---
title: Migratieactiviteit bewaken - Azure Database Migration Service
description: Meer informatie over het gebruik van de Azure Database Migration Service om migratieactiviteit te controleren.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 31b49cdd9e0e5569981b2a0b0c6efcab7239e019
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648509"
---
# <a name="monitor-migration-activity-using-the-azure-database-migration-service"></a>Migratieactiviteit bewaken met de Azure Database Migration Service
In dit artikel leert u hoe u de voortgang van een migratie controleren op zowel databaseniveau als tabelniveau.

## <a name="monitor-at-the-database-level"></a>Controleren op databaseniveau
Als u de activiteit op databaseniveau wilt controleren, bekijkt u het blad op databaseniveau:

![Blad op databaseniveau](media/how-to-monitor-migration-activity/dms-database-level-blade.png)

> [!NOTE]
> Als u de databasehyperlink selecteert, ziet u de lijst met tabellen en de voortgang van de migratie.

In de volgende tabel worden de velden op het blad op databaseniveau weergegeven en worden de verschillende statuswaarden beschreven die aan elk blad zijn gekoppeld.

<table id='overview' class='overview'>
  <thead>
    <tr>
      <th class="x-hidden-focus"><strong>Veldnaam</strong></th>
      <th><strong>Veldsubstatus</strong></th>
      <th><strong>Beschrijving</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3" class="ActivityStatus"><strong>Activiteitsstatus</strong></td>
      <td>In uitvoering</td>
      <td>Migratieactiviteit wordt uitgevoerd.</td>
    </tr>
    <tr>
      <td>Geslaagd</td>
      <td>Migratieactiviteit slaagde zonder problemen.</td>
    </tr>
    <tr>
      <td>Fout</td>
      <td>Migratie is mislukt. Selecteer de koppeling Foutgegevens weergeven onder migratiegegevens voor het volledige foutbericht.</td>
    </tr>
    <tr>
      <td rowspan="4" class="Status"><strong>Status</strong></td>
      <td>Initialiseren</td>
      <td>DMS is bezig met het opzetten van de migratiepijplijn.</td>
    </tr>
    <tr>
      <td>In uitvoering</td>
      <td>DMS-pijplijn wordt uitgevoerd en voert migratie uit.</td>
    </tr>
    <tr>
      <td>Voltooien</td>
      <td>Migratie voltooid.</td>
    </tr>
    <tr>
      <td>Mislukt</td>
      <td>Migratie is mislukt. Klik op migratiegegevens om migratiefouten te zien.</td>
    </tr>
    <tr>
      <td rowspan="5" class="migration-details"><strong>Details van de migratie</strong></td>
      <td>De migratiepijplijn starten</td>
      <td>DMS is bezig met het opzetten van de migratiepijplijn.</td>
    </tr>
    <tr>
      <td>Volledige gegevensbelasting in uitvoering</td>
      <td>DMS voert de initiële belasting uit.</td>
    </tr>
    <tr>
      <td>Klaar voor cutover</td>
      <td>Nadat de eerste belasting is voltooid, markeert DMS de database als klaar voor cutover. De gebruiker moet controleren of de gegevens de continue synchronisatie hebben ingehaald.</td>
    </tr>
    <tr>
      <td>Alle wijzigingen toegepast</td>
      <td>De initiële belasting en continue synchronisatie zijn voltooid. Deze status treedt ook op nadat de database is gecutover met succes.</td>
    </tr>
    <tr>
      <td>Foutgegevens bekijken</td>
      <td>Klik op de link om foutgegevens weer te geven.</td>
    </tr>
    <tr>
      <td rowspan="1" class="duration"><strong>Duur</strong></td>
      <td>N.v.t.</td>
      <td>Totale tijd van migratieactiviteit die wordt geïnitialiseerd tot migratie voltooid of migratie defect.</td>
    </tr>
     </tbody>
</table>

## <a name="monitor-at-table-level--quick-summary"></a>Monitor op tabelniveau – Snel overzicht
Als u de activiteit op tafelniveau wilt controleren, bekijkt u het blad op tafelniveau. Het bovenste gedeelte van het blad toont het gedetailleerde aantal rijen gemigreerd in volledige belasting en incrementele updates. 

Het onderste gedeelte van het blad bevat de tabellen en toont een snel overzicht van de voortgang van de migratie.

![Mes op tafelniveau - snelle samenvatting](media/how-to-monitor-migration-activity/dms-table-level-blade-summary.png)

In de volgende tabel worden de velden beschreven die worden weergegeven in de details op tabelniveau.

| Veldnaam        | Beschrijving       |
| ------------- | ------------- |
| **Volledige belasting voltooid**      | Aantal tabellen voltooid volledige gegevensbelasting. |
| **Volledige lading in de wachtrij**      | Aantal tabellen dat in de wachtrij staat voor volledige belasting.      |
| **Volledige belasting laden** | Aantal tabellen is mislukt.      |
| **Incrementele updates**      | Aantal cdc-updates (change data capture) in rijen die op doel worden toegepast. |
| **Incrementele inzetstukken**      | Aantal CDC-inserts in rijen toegepast op doel.      |
| **Incrementele verwijderingen** | Aantal CDC-verwijderingen in rijen die op doel zijn toegepast.      |
| **Wijzigingen in behandeling**      | Aantal CDC in rijen die nog wachten om toegepast te worden op doel. |
| **Toegepaste wijzigingen**      | Totaal van CDC-updates, inserts en verwijderingen in rijen die op doel zijn toegepast.      |
| **Tabellen in foutstatus** | Aantal tabellen dat tijdens de migratie in de status 'fout' staat. Enkele voorbeelden die tabellen in de foutstatus kunnen gaan, zijn wanneer er duplicaten in het doel zijn geïdentificeerd of gegevens niet compatibel zijn met laden in de doeltabel.      |

## <a name="monitor-at-table-level--detailed-summary"></a>Monitor op tabelniveau – Gedetailleerd overzicht
Er zijn twee tabbladen die de voortgang van de migratie weergeven in Volledige belasting en Incrementele gegevenssynchronisatie.
    
![Tabblad Volledige belasting](media/how-to-monitor-migration-activity/dms-full-load-tab.png)

![Tabblad Incrementele gegevenssynchronisatie](media/how-to-monitor-migration-activity/dms-incremental-data-sync-tab.png)

In de volgende tabel worden de velden beschreven die worden weergegeven in de migratievoortgang op tabelniveau.

| Veldnaam        | Beschrijving       |
| ------------- | ------------- |
| **Status - Synchroniseren**      | Continue synchronisatie wordt uitgevoerd. |
| **Invoegen**      | Aantal CDC-inserts in rijen toegepast op doel.      |
| **Update** | Aantal CDC-updates in rijen toegepast op doel.      |
| **Verwijderen**      | Aantal CDC-verwijderingen in rijen die op doel zijn toegepast. |
| **Totaal toegepast**      | Totaal van CDC-updates, inserts en verwijderingen in rijen die op doel zijn toegepast. |
| **Gegevensfouten** | Het aantal gegevensfouten is in deze tabel opgetreden. Enkele voorbeelden van de fouten zijn *511: Kan geen rij van grootte %d maken die groter is dan de toegestane maximale rijgrootte van %d, 8114: Fout die gegevenstype %ls omzet naar %ls.*  De klant moet vanuit dms_apply_exceptions tabel in Azure-doel vragen om de foutgegevens te bekijken.    |

> [!NOTE]
> CDC-waarden van Invoegen, Bijwerken en Verwijderen en Totaal toegepast kunnen afnemen wanneer de database wordt gecutoverd of migratie opnieuw wordt gestart.

## <a name="next-steps"></a>Volgende stappen
- Bekijk de migratierichtlijnen in de [Migratiehandleiding voor Microsoft-database](https://datamigration.microsoft.com/).