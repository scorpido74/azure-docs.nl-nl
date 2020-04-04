---
title: Upgrade naar de nieuwste generatie
description: Upgrade Azure Synapse Analytics SQL-pool naar de nieuwste generatie Azure-hardware- en opslagarchitectuur.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 3299aa8ed85cff5c29d043d30aac08db45ffe5d4
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632261"
---
# <a name="optimize-performance-by-upgrading-azure-synapse-analytics-sql-pool"></a>Prestaties optimaliseren door Azure Synapse Analytics SQL-pool te upgraden

Upgrade SQL-pool naar de nieuwste generatie Azure-hardware- en opslagarchitectuur.

## <a name="why-upgrade"></a>Waarom upgraden?

U nu naadloos upgraden naar de SQL-groep Compute Optimized Gen2-laag in de Azure-portal voor [ondersteunde regio's.](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) Als uw regio geen zelfupgrade ondersteunt, u upgraden naar een ondersteunde regio of wachten tot de zelfupgrade beschikbaar is in uw regio. Upgrade nu om te profiteren van de nieuwste generatie Azure-hardware en verbeterde opslagarchitectuur, waaronder snellere prestaties, hogere schaalbaarheid en onbeperkte kolomopslag.

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

> [!IMPORTANT]
> Deze upgrade is van toepassing op Compute Optimized Gen1-tier SQL-groepen in [ondersteunde regio's.](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)

## <a name="before-you-begin"></a>Voordat u begint

1. Controleer of uw [regio](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) wordt ondersteund voor GEN1- naar GEN2-migratie. Let op de automatische migratiedatums. Als u conflicten met het geautomatiseerde proces wilt voorkomen, plant u uw handmatige migratie vóór de begindatum van het geautomatiseerde proces.
2. Als u zich in een regio bevindt die nog niet wordt ondersteund, blijft u controleren of uw regio wordt toegevoegd of [wordt deze bijgewerkt met herstel](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal) naar een ondersteunde regio.
3. Als uw regio wordt ondersteund, [kunt u upgraden via de Azure-portal](#upgrade-in-a-supported-region-using-the-azure-portal)
4. **Selecteer het voorgestelde prestatieniveau** voor SQL-pool op basis van uw huidige prestatieniveau op de Compute Optimized Gen1-laag met de onderstaande toewijzing:

   | Geoptimaliseerde Gen1-laag berekenen | Geoptimaliseerde Gen2-laag berekenen |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c            |
   |            DW600            |           DW500c            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

> [!NOTE]
> Voorgestelde prestatieniveaus zijn geen directe conversie. We raden u bijvoorbeeld aan om van DW600 naar DW500c te gaan.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Upgraden in een ondersteund gebied met de Azure-portal

- Migratie van Gen1 naar Gen2 via de Azure-portal is permanent. Er is geen proces om terug te keren naar Gen1.
- SQL-groep moet worden uitgevoerd om te migreren naar Gen2

### <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Meld u aan bij [Azure Portal](https://portal.azure.com/).
- Ervoor zorgen dat SQL-pool wordt uitgevoerd - het moet zijn om te migreren naar Gen2

### <a name="powershell-upgrade-commands"></a>PowerShell-upgradeopdrachten

1. Als de te upgraden SQL-groep met Compute Optimized Gen1-laag wordt onderbroken, [hervat u de SQL-pool](pause-and-resume-compute-portal.md).

2. Wees voorbereid op een paar minuten downtime.

3. Identificeer codeverwijzingen naar compute optimized Gen1-prestatieniveaus en wijzig deze naar hun gelijkwaardige Compute Optimized Gen2-prestatieniveau. Hieronder staan twee voorbeelden van waar u codereferenties moet bijwerken voordat u een upgrade uitvoert:

   Originele Gen1 PowerShell, opdracht:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Gewijzigd in:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE]
   > -RequestedServiceObjectiveName "DW300" wordt gewijzigd in - RequestedServiceObjectiveName "DW300**c**"
   >

   Oorspronkelijke Gen1 T-SQL, opdracht:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   Gewijzigd in:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ;
   ```

   > [!NOTE]
   > SERVICE_OBJECTIVE = 'DW300' wordt gewijzigd in SERVICE_OBJECTIVE = 'DW300**c**'

## <a name="start-the-upgrade"></a>De upgrade starten

1. Ga naar de Compute Optimized Gen1 SQL-groep in de Azure-portal. Als de te upgraden SQL-groep met Compute Optimized Gen1-laag wordt onderbroken, [hervat u de SQL-pool](pause-and-resume-compute-portal.md).
2. Selecteer **Upgrade naar Gen2-kaart** ![onder het tabblad Taken: Upgrade_1](./media/upgrade-to-latest-generation/upgrade-to-gen2-1.png)

   > [!NOTE]
   > Als u de **upgrade naar Gen2-kaart** niet onder het tabblad Taken ziet, is uw abonnementstype beperkt in het huidige gebied.
   > [Stuur een ondersteuningsticket](sql-data-warehouse-get-started-create-support-ticket.md) in om je abonnement op de witte lijst te krijgen.

3. Zorg ervoor dat uw werkbelasting is voltooid en is verlopen voordat u een upgrade uitvoert. U zult downtime ervaren voor een paar minuten voordat uw SQL-pool weer online is als een Compute Optimized Gen2-tier SQL-pool. **Selecteer Upgrade:**

   ![Upgrade_2](./media/upgrade-to-latest-generation/upgrade-to-gen2-2.png)

4. **Controleer uw upgrade** door de status in de Azure-portal te controleren:

   ![Upgrade3](./media/upgrade-to-latest-generation/upgrade-to-gen2-3.png)

   De eerste stap van het upgradeproces gaat door de schaalbewerking ("Upgraden - Offline") waarbij alle sessies worden gedood en verbindingen worden verwijderd.

   De tweede stap van het upgradeproces is gegevensmigratie ("Upgraden - Online"). Datamigratie is een online trickle background proces. Dit proces verplaatst langzaam kolomgegevens van de oude opslagarchitectuur naar de nieuwe opslagarchitectuur met behulp van een lokale SSD-cache. Gedurende deze periode is uw SQL-pool online voor het opvragen en laden. Uw gegevens zijn beschikbaar voor query's, ongeacht of deze zijn gemigreerd of niet. De gegevensmigratie gebeurt in verschillende snelheden, afhankelijk van de grootte van uw gegevens, uw prestatieniveau en het aantal segmenten van uw kolomarchief.

5. **Optionele aanbeveling:** Zodra de schalingbewerking is voltooid, u het achtergrondproces voor gegevensmigratie versnellen. U gegevensverplaatsing forceren door [Opnieuw opalterindex uit](sql-data-warehouse-tables-index.md) te voeren in alle primaire kolomarchieftabellen die u zou opvragen bij een grotere SLO- en resourceklasse. Deze bewerking is **offline** in vergelijking met het trickle-achtergrondproces, dat uren kan duren, afhankelijk van het aantal en de grootte van uw tabellen. Echter, eenmaal voltooid, zal gegevensmigratie veel sneller zijn dankzij de nieuwe verbeterde opslagarchitectuur met hoogwaardige rijgroepen.

> [!NOTE]
> Opnieuw herstellen van de index is een offlinebewerking en de tabellen zijn pas beschikbaar als de wederopbouw is voltooid.

Met de volgende query worden de vereiste opdrachten voor opnieuw opbouwen van de alter-index gegenereerd om de gegevensmigratie te versnellen:

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON ['
       + Schema_name(tbl.schema_id) + '].['
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE
                                                         WHEN (
                                                     (SELECT Count(*)
                                                      FROM   sys.partitions
                                                             part2
                                                      WHERE  part2.index_id
                                                             = idx.index_id
                                                             AND
                                                     idx.object_id =
                                                     part2.object_id)
                                                     > 1 ) THEN
              ' PARTITION = '
              + Cast(part.partition_number AS NVARCHAR(256))
              ELSE ''
                                                       END ) + '; SELECT ''[' +
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' +
              Object_name(idx.object_id) + '] ' + (
              CASE
                WHEN ( (SELECT Count(*)
                        FROM   sys.partitions
                               part2
                        WHERE
                     part2.index_id =
                     idx.index_id
                     AND idx.object_id
                         = part2.object_id) > 1 ) THEN
              ' PARTITION = '
              + Cast(part.partition_number AS NVARCHAR(256))
              + ' completed'';'
              ELSE ' completed'';'
                                                    END )
FROM   sys.indexes idx
       INNER JOIN sys.tables tbl
               ON idx.object_id = tbl.object_id
       LEFT OUTER JOIN sys.partitions part
                    ON idx.index_id = part.index_id
                       AND idx.object_id = part.object_id
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE';
```

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Upgraden vanuit een geografische Azure-regio met herstel via de Azure-portal

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Een door de gebruiker gedefinieerd herstelpunt maken met de Azure-portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

2. Navigeer naar de SQL-groep waarvoor u een herstelpunt wilt maken.

3. Selecteer boven aan de sectie Overzicht de optie **+Nieuw herstelpunt .**

    ![Nieuw herstelpunt](./media/upgrade-to-latest-generation/creating_restore_point_0.png)

4. Geef een naam op voor uw herstelpunt.

    ![Naam van herstelpunt](./media/upgrade-to-latest-generation/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Een actieve of onderbroken database herstellen met de Azure-portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Navigeer naar de SQL-groep waaruit u wilt herstellen.
3. Selecteer boven aan de sectie Overzicht de optie **Herstellen**.

    ![ Overzicht van Herstellen](./media/upgrade-to-latest-generation/restoring_0.png)

4. Selecteer **Automatische herstelpunten** of **door de gebruiker gedefinieerde herstelpunten**. Selecteer een door de **gebruiker gedefinieerd herstelpunt** of **Maak een nieuw door de gebruiker gedefinieerd herstelpunt voor**door de gebruiker gedefinieerde herstelpunten. Selecteer voor de server **Nieuw maken** en kies een server in een door Gen2 ondersteunde geografische regio.

    ![Automatische herstelpunten](./media/upgrade-to-latest-generation/restoring_1.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Herstellen vanuit een Azure-geografisch gebied met PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als u een database wilt herstellen, gebruikt u de cmdlet [Restore-AzSqlDatabase.](/powershell/module/az.sql/restore-azsqldatabase)

> [!NOTE]
> U een geo-herstel uitvoeren naar Gen2! Geef hiervoor een Gen2 ServiceObjectiveName (bijvoorbeeld DW1000**c)** op als optionele parameter.

1. Open Windows PowerShell.
2. Maak verbinding met uw Azure-account en vermeld alle abonnementen die aan uw account zijn gekoppeld.
3. Selecteer het abonnement dat de database bevat die moet worden hersteld.
4. Haal de database die u wilt herstellen.
5. Maak de herstelaanvraag voor de database en geef een Gen2 ServiceObjectiveName op.
6. Controleer de status van de geoherstelde database.

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>" -RequestedServiceObjectiveName "DW300c"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Zie [Uw database configureren na herstel](../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)als u uw database wilt configureren nadat deze is voltooid.

De herstelde database is tde-ingeschakeld als de brondatabase tde-ingeschakeld is.

Als u problemen ondervindt met uw SQL-groep, maakt u een [ondersteuningsaanvraag](sql-data-warehouse-get-started-create-support-ticket.md) en verwijst u naar 'Gen2-upgrade' als mogelijke oorzaak.

## <a name="next-steps"></a>Volgende stappen

Uw bijgewerkte SQL-pool is online. Zie [Resourceklassen voor Workload Management](resource-classes-for-workload-management.md)om te profiteren van de verbeterde architectuur.
