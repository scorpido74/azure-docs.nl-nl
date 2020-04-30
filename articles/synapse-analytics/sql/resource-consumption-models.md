---
title: Synapse Analytics SQL-Resource verbruik
description: Meer informatie over Synapse SQL-verbruiks modellen in azure Synapse Analytics.
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: e078893b3bbe0ef5661cd87bad62b320f78ceb5d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424858"
---
# <a name="azure-synapse-analytics-sql-resource-consumption"></a>Azure Synapse Analytics SQL-Resource verbruik

In dit artikel worden resource verbruiks modellen van Synapse SQL (preview) beschreven.

## <a name="sql-on-demand"></a>SQL on-demand

SQL on-demand is een betalen per query-service waarvoor u niet de juiste grootte moet kiezen. Het systeem wordt automatisch aangepast op basis van uw vereisten, zodat u uw infra structuur hoeft te beheren en de juiste grootte voor uw oplossing kunt kiezen.

## <a name="sql-pool---data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>SQL-pool-data warehouse units (Dwu's) en Compute data warehouse units (cDWUs)

Aanbevelingen voor het kiezen van het ideale aantal data warehouse-eenheden (Dwu's) om de prijs en prestaties te optimaliseren en hoe u het aantal eenheden kunt wijzigen.

### <a name="what-are-data-warehouse-units"></a>Wat zijn data warehouse-eenheden?

Een Synapse SQL-pool vertegenwoordigt een verzameling analytische resources die worden ingericht. Analyse bronnen worden gedefinieerd als een combi natie van CPU, geheugen en IO. Deze drie bronnen worden gebundeld in eenheden van reken schaal, genaamd data warehouse units (Dwu's). Met een DWU wordt een abstracte, genormaliseerde meting van rekenresources en prestaties aangeduid. Een wijziging in uw service niveau wijzigt het aantal Dwu's dat beschikbaar is voor het systeem, waardoor de prestaties en de kosten van uw systeem worden aangepast.

Voor betere prestaties kunt u het aantal data warehouse-eenheden verhogen. Voor minder prestaties vermindert u de Data Warehouse-eenheden. Opslagkosten en rekenkosten worden afzonderlijk gefactureerd, waardoor het wijzigen van het aantal DWU's niet van invloed is op de opslagkosten.

De prestaties voor Data Warehouse-eenheden zijn gebaseerd op de metrische gegevens van de werk belasting van deze data warehouse:

- Hoe snel een Standard data warehousing-query een groot aantal rijen kan scannen en vervolgens een complexe aggregatie kan uitvoeren. Deze bewerking is I/O en CPU-intensief.
- Hoe snel het Data Warehouse gegevens kan opnemen uit Azure Storage blobs of Azure Data Lake. Deze bewerking is netwerk-en CPU-intensief.
- Hoe snel de [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL-opdracht een tabel kan kopiëren. Deze bewerking omvat het lezen van gegevens uit de opslag, het distribueren ervan over de knoop punten van het apparaat en het opnieuw schrijven naar de opslag. Deze bewerking is CPU, i/o en netwerk intensief.

Dwu's verhogen:

- Lineaire wijzigingen in de prestaties van het systeem voor scans, aggregaties en CTAS-instructies
- Verhoogt het aantal lezers en schrijvers voor poly base-laad bewerkingen
- Hiermee wordt het maximum aantal gelijktijdige query's en gelijktijdigheids sleuven verhoogd.

### <a name="service-level-objective"></a>Service Level Objective

De serviceniveau doelstelling (SLO) is de schaal baarheid-instelling waarmee de kosten en het prestatie niveau van uw data warehouse worden bepaald. De service niveaus voor Gen2 worden gemeten in reken Data Warehouse-eenheden (cDWU), bijvoorbeeld DW2000c. Gen1-service niveaus worden gemeten in Dwu's, bijvoorbeeld DW2000.

De serviceniveau doelstelling (SLO) is de schaal baarheid-instelling waarmee de kosten en het prestatie niveau van uw data warehouse worden bepaald. De service niveaus voor Gen2 SQL-pool worden gemeten in data warehouse units (DWU), bijvoorbeeld DW2000c.

> [!NOTE]
> Azure SQL Data Warehouse Gen2 onlangs extra schaal mogelijkheden toegevoegd ter ondersteuning van reken lagen van 100 cDWU. Bestaande data warehouses op gen1 waarvoor de lagere reken lagen zijn vereist, kunnen nu worden bijgewerkt naar Gen2 in de regio's die momenteel beschikbaar zijn voor geen extra kosten.  Als uw regio nog niet wordt ondersteund, kunt u nog steeds een upgrade uitvoeren naar een ondersteunde regio. Zie [upgrade naar Gen2](../sql-data-warehouse/upgrade-to-latest-generation.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor meer informatie.

In T-SQL bepaalt de SERVICE_OBJECTIVE instelling het service niveau en de prestatie-laag voor uw SQL-groep.

```sql
CREATE DATABASE mySQLDW
(Edition = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

### <a name="performance-tiers-and-data-warehouse-units"></a>Prestatie lagen en Data Warehouse-eenheden

Elke prestatie tier maakt gebruik van een iets andere maat eenheid voor de Data Warehouse-eenheden. Dit verschil wordt op de factuur weer gegeven wanneer de eenheid van de schaal rechtstreeks wordt omgezet in de facturering.

- Gen1-data warehouses worden gemeten in data warehouse units (Dwu's).
- Gen2-data warehouses worden gemeten in reken data warehouse units (cDWUs).

Zowel Dwu's als cDWUs ondersteunen het schalen van de computer naar boven of beneden en het onderbreken van de reken kracht wanneer u het Data Warehouse niet hoeft te gebruiken. Deze bewerkingen zijn allemaal op aanvraag. Gen2 maakt gebruik van een lokale schijf cache op de reken knooppunten om de prestaties te verbeteren. Wanneer u het systeem schaalt of onderbreekt, wordt de cache ongeldig en wordt er een periode voor het opwarmen van de cache vereist voordat optimale prestaties worden gerealiseerd.  

Naarmate u Data Warehouse-eenheden verg root, zijn er lineaire toenemende computer bronnen. Gen2 biedt de beste query prestaties en hoogste schaal. Gen2-systemen maken het gebruik van de cache ook optimaal.

#### <a name="capacity-limits"></a>Capaciteitslimieten

Elke SQL-Server (bijvoorbeeld myserver.database.windows.net) heeft een [DTU-quotum (data base Trans Action Unit)](../../sql-database/sql-database-service-tiers-dtu.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) waarmee een specifiek aantal data warehouse-eenheden kan worden toegestaan. Zie [capaciteits limieten voor werk belasting beheer](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#workload-management)voor meer informatie.

### <a name="how-many-data-warehouse-units-do-i-need"></a>Hoeveel data warehouse-eenheden heb ik nodig?

Het ideale aantal data warehouse-eenheden hangt zeer veel af van uw werk belasting en de hoeveelheid gegevens die u in het systeem hebt geladen.

Stappen voor het vinden van de beste DWU voor uw workload:

1. Begin door een kleiner DWU te selecteren.
2. Bewaak de prestaties van uw toepassing tijdens het testen van gegevens die in het systeem worden geladen, waarbij het aantal geselecteerde Dwu's wordt onderzocht vergeleken met de prestaties die u ziet.
3. Identificeer eventuele aanvullende vereisten voor periodieke Peri Oden van piek activiteit. Werk belastingen die aanzienlijke pieken en troughs in activiteiten tonen, moeten mogelijk regel matig worden geschaald.

SQL-groep is een scale-out systeem waarmee u grote hoeveel heden reken-en query gegevens kunt inrichten. Als u de echte mogelijkheden voor schalen wilt zien, met name bij grotere Dwu's, kunt u het beste de gegevensset schalen terwijl u schaalt om ervoor te zorgen dat u voldoende gegevens hebt om de Cpu's te kunnen invoeren. Voor schaal tests kunt u het beste ten minste 1 TB gebruiken.

> [!NOTE]
>
> Query prestaties worden alleen verhoogd met meer parallel Lise ring als het werk kan worden gesplitst tussen reken knooppunten. Als u merkt dat het schalen van de prestaties niet verandert, moet u mogelijk uw tabel ontwerp en/of uw query's afstemmen. Zie [Manage User query's](../overview-cheat-sheet.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)(Engelstalig) voor meer informatie over het afstemmen van query's.

### <a name="permissions"></a>Machtigingen

Voor het wijzigen van de Data Warehouse-eenheden zijn de machtigingen vereist die worden beschreven in [ALTER data base](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

Ingebouwde rollen voor Azure-resources, zoals Inzender voor SQL-data bases en SQL Server Inzender, kunnen DWU-instellingen wijzigen.

#### <a name="view-current-dwu-settings"></a>Huidige DWU-instellingen weer geven

De huidige DWU-instelling weer geven:

1. Open SQL Server-objectverkenner in Visual Studio.
2. Verbinding maken met de hoofd database die aan de logische SQL Database-Server is gekoppeld.
3. Selecteer in de weer gave sys. database_service_objectives Dynamic Management. Hier volgt een voorbeeld:

```sql
SELECT  db.name [Database]
,        ds.edition [Edition]
,        ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

### <a name="change-data-warehouse-units"></a>Data Warehouse-eenheden wijzigen

#### <a name="azure-portal"></a>Azure Portal

Dwu's wijzigen:

1. Open de [Azure Portal](https://portal.azure.com), open uw data base en klik op **schalen**.

2. Verplaats onder **schalen**de schuif regelaar naar links of rechts om de instelling DWU te wijzigen.

3. Klik op **Opslaan**. Er verschijnt een bevestigingsbericht. Klik op **Ja** om te bevestigen of **Nee** om te annuleren.

#### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als u de Dwu's wilt wijzigen, gebruikt u de Power shell [-cmdlet Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) . In het volgende voor beeld wordt de serviceniveau doelstelling ingesteld op DW1000 voor de data base-MySQLDW die wordt gehost op server mijnserver.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

Zie [Power shell-cmdlets voor SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-reference-powershell-cmdlets.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor meer informatie.

#### <a name="t-sql"></a>T-SQL

Met T-SQL kunt u de huidige DWUsettings weer geven, de instellingen wijzigen en de voortgang controleren.

De Dwu's wijzigen:

1. Maak verbinding met de hoofd database die aan uw logische SQL Database-Server is gekoppeld.
2. Gebruik de instructie [ALTER data base](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) TSQL. In het volgende voor beeld wordt de serviceniveau doelstelling ingesteld op DW1000c voor de data base-MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

#### <a name="rest-apis"></a>REST-API’s

Als u de Dwu's wilt wijzigen, gebruikt u de REST API [Data Base maken of bijwerken](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) . In het volgende voor beeld wordt de serviceniveau doelstelling ingesteld op DW1000c voor de data base-MySQLDW, die wordt gehost op server mijnserver. De server bevindt zich in een Azure-resource groep met de naam ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Zie [rest-api's voor SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor meer rest API voor beelden.

### <a name="check-status-of-dwu-changes"></a>Status van DWU-wijzigingen controleren

Het volt ooien van DWU-wijzigingen kan enkele minuten duren. Als u automatisch schaalt, kunt u overwegen logica te implementeren om ervoor te zorgen dat bepaalde bewerkingen zijn voltooid voordat u doorgaat met een andere actie.

Door de database status via verschillende eind punten te controleren, kunt u Automation goed implementeren. De portal biedt een melding na voltooiing van een bewerking en de huidige status van de data bases, maar staat geen programmatische controle van de status toe.

U kunt de status van de data base niet controleren op scale-out-bewerkingen met de Azure Portal.

De status van DWU-wijzigingen controleren:

1. Maak verbinding met de hoofd database die aan uw logische SQL Database-Server is gekoppeld.
2. Verzend de volgende query om de status van de data base te controleren.

```sql
SELECT    *
FROM      sys.databases
;
```

1. Verzend de volgende query om de status van de bewerking te controleren

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

Deze DMV retourneert informatie over verschillende beheer bewerkingen in uw SQL-groep, zoals de bewerking en de status van de bewerking, die IN_PROGRESS of voltooid is.

### <a name="the-scaling-workflow"></a>De werk stroom voor schalen

Wanneer u een schaal bewerking start, beëindigt het systeem eerst alle geopende sessies. alle geopende trans acties worden teruggedraaid om een consistente status te garanderen. Schaal bewerkingen worden alleen uitgevoerd nadat de transactionele terugdraai actie is voltooid.  

- Voor een schaal bewerking koppelt het systeem alle reken knooppunten, worden de extra reken knooppunten ingericht en vervolgens opnieuw gekoppeld aan de opslaglaag.
- Voor een schaal bewerking koppelt het systeem alle reken knooppunten los en koppelt vervolgens alleen de benodigde knoop punten aan de opslaglaag.

## <a name="next-steps"></a>Volgende stappen

Zie [resource klassen voor workload Management](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) en [geheugen-en gelijktijdigheids limieten](../sql-data-warehouse/memory-concurrency-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor meer informatie over het beheren van prestaties.
