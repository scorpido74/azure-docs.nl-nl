---
title: Synapse Analytics SQL-bronverbruik
description: Meer informatie over Synapse SQL-verbruiksmodellen in Azure Synapse Analytics.
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: e078893b3bbe0ef5661cd87bad62b320f78ceb5d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424858"
---
# <a name="azure-synapse-analytics-sql-resource-consumption"></a>Azure Synapse Analytics SQL-bronverbruik

In dit artikel worden resourceverbruiksmodellen van Synapse SQL (preview) beschreven.

## <a name="sql-on-demand"></a>SQL on-demand

SQL on-demand is een pay-per-queryservice waarvoor u niet de juiste grootte hoeft te kiezen. Het systeem past automatisch aan op basis van uw behoeften, waardoor u niet meer uw infrastructuur beheert en de juiste grootte voor uw oplossing kiest.

## <a name="sql-pool---data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>SQL-groep - Data Warehouse Units (DDC's) en compute Data Warehouse Units (cDWU's)

Aanbevelingen voor het kiezen van het ideale aantal datawarehouse-eenheden (DDC's) om de prijs en prestaties te optimaliseren en hoe u het aantal eenheden wijzigen.

### <a name="what-are-data-warehouse-units"></a>Wat zijn datawarehouse-eenheden

Een Synapse SQL-pool vertegenwoordigt een verzameling analytische resources die worden ingericht. Analytische bronnen worden gedefinieerd als een combinatie van CPU, geheugen en IO. Deze drie resources worden gebundeld in eenheden van compute scale genaamd Data Warehouse Units (DWUs). Met een DWU wordt een abstracte, genormaliseerde meting van rekenresources en prestaties aangeduid. Een wijziging in uw serviceniveau wijzigt het aantal DBO's dat beschikbaar is voor het systeem, dat op zijn beurt de prestaties en kosten van uw systeem aanpast.

Voor hogere prestaties u het aantal gegevensmagazijneenheden verhogen. Voor minder prestaties u de eenheden van het gegevensmagazijn verlagen. Opslagkosten en rekenkosten worden afzonderlijk gefactureerd, waardoor het wijzigen van het aantal DWU's niet van invloed is op de opslagkosten.

De prestaties voor gegevensmagazijnen zijn gebaseerd op deze workloadmetrics voor gegevensmagazijnen:

- Hoe snel een standaard query voor gegevensopslag een groot aantal rijen kan scannen en vervolgens een complexe aggregatie kan uitvoeren. Deze bewerking is I/O en CPU intensief.
- Hoe snel het gegevensmagazijn gegevens kan opnemen uit Azure Storage Blobs of Azure Data Lake. Deze bewerking is netwerk- en CPU-intensief.
- Hoe snel [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) de opdracht T-SQL een tabel kan kopiëren. Deze bewerking omvat het lezen van gegevens uit de opslag, het distribueren over de knooppunten van het toestel en het opnieuw schrijven naar opslag. Deze bewerking is CPU, IO en netwerkintensief.

Toenemende dOM:

- Wijzigt lineair de prestaties van het systeem voor scans, aggregaties en CTAS-instructies
- Verhoogt het aantal lezers en schrijvers voor PolyBase load operations
- Hiermee verhoogt u het maximum aantal gelijktijdige query's en gelijktijdige sleuven.

### <a name="service-level-objective"></a>Service Level Objective

De Service Level Objective (SLO) is de schaalbaarheidsinstelling die het kosten- en prestatieniveau van uw datawarehouse bepaalt. De serviceniveaus voor Gen2 worden gemeten in compute data warehouse units (cDWU), bijvoorbeeld DW2000c. Gen1-serviceniveaus worden gemeten in DDC's, bijvoorbeeld DW2000.

De Service Level Objective (SLO) is de schaalbaarheidsinstelling die het kosten- en prestatieniveau van uw datawarehouse bepaalt. De serviceniveaus voor Gen2 SQL-pool worden gemeten in data warehouse units (DWU), bijvoorbeeld DW2000c.

> [!NOTE]
> Azure SQL Data Warehouse Gen2 heeft onlangs extra schaalmogelijkheden toegevoegd om rekenlagen te ondersteunen met een kracht van maar 100 cDWU. Bestaande gegevensmagazijnen die momenteel op Gen1 zijn die de lagere rekenlagen vereisen, kunnen nu upgraden naar Gen2 in de regio's die momenteel zonder extra kosten beschikbaar zijn.  Als uw regio nog niet wordt ondersteund, u nog steeds upgraden naar een ondersteunde regio. Zie [Upgraden naar Gen2 voor](../sql-data-warehouse/upgrade-to-latest-generation.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)meer informatie.

In T-SQL bepaalt de instelling SERVICE_OBJECTIVE het serviceniveau en de prestatielaag voor uw SQL-groep.

```sql
CREATE DATABASE mySQLDW
(Edition = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

### <a name="performance-tiers-and-data-warehouse-units"></a>Prestatielagen en gegevensmagazijneenheden

Elke prestatielaag maakt gebruik van een iets andere maateenheid voor hun gegevensmagazijneenheden. Dit verschil wordt weerspiegeld op de factuur als de schaaleenheid zich rechtstreeks vertaalt naar facturering.

- Gen1-datawarehouses worden gemeten in Data Warehouse Units (DBO's).
- Gen2-datawarehouses worden gemeten in compute Data Warehouse Units (cDWU's).

Zowel DFC's als cDWU's ondersteunen het op- of neerschalen van compute en het onderbreken van compute wanneer u het gegevensmagazijn niet hoeft te gebruiken. Deze operaties zijn allemaal on-demand. Gen2 gebruikt een lokale schijfgebaseerde cache op de compute nodes om de prestaties te verbeteren. Wanneer u het systeem schaalt of pauzeert, wordt de cache ongeldig gemaakt en is er dus een periode van cacheopwarming vereist voordat optimale prestaties worden bereikt.  

Naarmate u de eenheden voor gegevensmagazijnen verhoogt, verhoogt u de computerresources lineair. Gen2 biedt de beste queryprestaties en de hoogste schaal. Gen2-systemen maken ook het meeste gebruik van de cache.

#### <a name="capacity-limits"></a>Capaciteitslimieten

Elke SQL-server (myserver.database.windows.net) heeft bijvoorbeeld een [DTU-quotum (Database Transaction Unit)](../../sql-database/sql-database-service-tiers-dtu.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) waarmee een specifiek aantal gegevensmagazijneenheden mogelijk is. Zie voor meer informatie de [capaciteitslimieten voor werkbelastingbeheer](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#workload-management).

### <a name="how-many-data-warehouse-units-do-i-need"></a>Hoeveel datawarehouse-eenheden heb ik nodig

Het ideale aantal datawarehouse-eenheden is sterk afhankelijk van uw werkbelasting en de hoeveelheid gegevens die u in het systeem hebt geladen.

Stappen voor het vinden van de beste DWU voor uw werkbelasting:

1. Begin met het selecteren van een kleinere DWU.
2. Controleer de prestaties van uw toepassing terwijl u gegevensbelastingen in het systeem test, waarbij het aantal selecteerde DFC's wordt waargenomen in vergelijking met de prestaties die u waarneemt.
3. Eventuele aanvullende vereisten voor periodieke perioden van piekactiviteit identificeren. Workloads die aanzienlijke pieken en dalen in activiteit vertonen, moeten mogelijk regelmatig worden geschaald.

SQL-pool is een scale-outsysteem dat enorme hoeveelheden reken- en querygrote hoeveelheden gegevens kan inrichten. Als u de werkelijke mogelijkheden voor schalen wilt zien, met name bij grotere DKU's, raden we u aan de gegevensset te schalen terwijl u schaalt om ervoor te zorgen dat u voldoende gegevens hebt om de CPU's te voeden. Voor schaaltesten raden we aan om ten minste 1 TB te gebruiken.

> [!NOTE]
>
> Queryprestaties nemen alleen toe met meer parallelisatie als het werk kan worden verdeeld over compute nodes. Als u merkt dat schalen uw prestaties niet verandert, moet u mogelijk uw tabelontwerp en/of uw query's afstemmen. Zie Gebruikersquery's beheren voor richtlijnen voor het afstemmen [van query's.](../overview-cheat-sheet.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="permissions"></a>Machtigingen

Voor het wijzigen van de gegevensmagazijneenheden zijn de machtigingen vereist die zijn beschreven in [ALTER DATABASE.](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

Ingebouwde rollen voor Azure-resources zoals SQL DB Contributor en SQL Server Contributor kunnen de DWU-instellingen wijzigen.

#### <a name="view-current-dwu-settings"></a>Huidige DWU-instellingen weergeven

Ga als instellen voor de weergave van de huidige DWU-instelling:

1. Open SQL Server Object Explorer in Visual Studio.
2. Maak verbinding met de hoofddatabase die is gekoppeld aan de logische SQL Database-server.
3. Kies in de dynamische beheerweergave sys.database_service_objectives. Hier volgt een voorbeeld:

```sql
SELECT  db.name [Database]
,        ds.edition [Edition]
,        ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

### <a name="change-data-warehouse-units"></a>Gegevensmagazijneenheden wijzigen

#### <a name="azure-portal"></a>Azure Portal

DOM wijzigen:

1. Open de [Azure-portal,](https://portal.azure.com)open uw database en klik op **Schalen.**

2. Verplaats **onder Schalen**de schuifregelaar naar links of rechts om de DWU-instelling te wijzigen.

3. Klik op **Opslaan**. Er verschijnt een bevestigingsbericht. Klik op **Ja** om te bevestigen of **Nee** om te annuleren.

#### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als u de DDU's wilt wijzigen, gebruikt u de cmdlet [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) PowerShell. In het volgende voorbeeld wordt de doelstelling serviceniveau ingesteld op DW1000 voor de database MySQLDW die wordt gehost op server MyServer.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

Zie [PowerShell-cmdlets voor SQL Data Warehouse voor](../sql-data-warehouse/sql-data-warehouse-reference-powershell-cmdlets.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) meer informatie

#### <a name="t-sql"></a>T-SQL

Met T-SQL u de huidige DWU-instellingen bekijken, de instellingen wijzigen en de voortgang controleren.

Ga als u de DOM's wijzigen:

1. Maak verbinding met de hoofddatabase die is gekoppeld aan uw logische SQL Database-server.
2. Gebruik de verklaring [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) TSQL. In het volgende voorbeeld wordt de doelstelling serviceniveau ingesteld op DW1000c voor de database MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

#### <a name="rest-apis"></a>REST-API’s

Als u de DBO's wilt wijzigen, gebruikt u de API Database REST [maken of bijwerken.](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) In het volgende voorbeeld wordt de doelstelling serviceniveau ingesteld op DW1000c voor de database MySQLDW, die wordt gehost op server MyServer. De server bevindt zich in een Azure-brongroep met de naam ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Zie REST API's voor SQL Data Warehouse voor meer VOORBEELDEN van REST [API's.](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="check-status-of-dwu-changes"></a>Status van DWU-wijzigingen controleren

Het kan enkele minuten duren voordat DWU-wijzigingen zijn doorgevoerd. Als u automatisch schaalt, u logica implementeren om ervoor te zorgen dat bepaalde bewerkingen zijn voltooid voordat u verdergaat met een andere actie.

Als u de databasestatus controleert via verschillende eindpunten, u automatisering correct implementeren. De portal biedt melding na voltooiing van een bewerking en de huidige status van de databases, maar staat geen programmatische controle van de status toe.

U de databasestatus niet controleren op scale-outbewerkingen met de Azure-portal.

Ga als het gaat om de status van DWU-wijzigingen:

1. Maak verbinding met de hoofddatabase die is gekoppeld aan uw logische SQL Database-server.
2. Verzend de volgende query om de databasestatus te controleren.

```sql
SELECT    *
FROM      sys.databases
;
```

1. De volgende query indienen om de status van de bewerking te controleren

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

Deze DMV retourneert informatie over verschillende beheerbewerkingen op uw SQL-groep, zoals de bewerking en de status van de bewerking, die IN_PROGRESS of VOLTOOID is.

### <a name="the-scaling-workflow"></a>De schalingswerkstroom

Wanneer u een schaalbewerking start, doodt het systeem eerst alle geopende sessies en draait het alle geopende transacties terug om een consistente status te garanderen. Voor schaalbewerkingen vindt schalen alleen plaats nadat deze transactionele rollback is voltooid.  

- Voor een scale-upbewerking ontkoppelt het systeem alle compute-knooppunten, voorziet het in de extra compute-knooppunten en wordt het opnieuw aan de opslaglaag gekoppeld.
- Voor een scale-down-bewerking ontkoppelt het systeem alle compute-knooppunten en koppelt het vervolgens alleen de benodigde knooppunten aan de opslaglaag.

## <a name="next-steps"></a>Volgende stappen

Zie [Resourceklassen voor werkbelastingbeheer](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) en [geheugen- en gelijktijdigheidslimieten](../sql-data-warehouse/memory-concurrency-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor meer informatie over het beheren van prestaties.
