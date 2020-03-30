---
title: Pools voor Elastic Database beheren
description: Azure SQL-elastische pools maken en beheren.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: d8dde76753e58c713763c16230e5461fef43be88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067339"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Elastische pools beheren in Azure SQL Database

Met een elastische groep bepaalt u de hoeveelheid resources die de elastische groep nodig heeft om de werkbelasting van de databases te verwerken en de hoeveelheid resources voor elke samengevoegde database.

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Azure-portal: elastische pools en gepoolde databases beheren

Alle poolinstellingen zijn op één plaats te vinden: het **poolblad configureren.** Als u hier wilt komen, zoekt u een elastische pool in de portal en klikt u op **Pool configureren** vanaf de bovenkant van het blad of in het resourcemenu aan de linkerkant.

Vanaf hier u elke combinatie van de volgende wijzigingen aanbrengen en ze allemaal in één batch opslaan:

1. De servicelaag van de groep wijzigen
2. Schaal de prestaties (DTU of vCores) en opslag omhoog of omlaag
3. Databases toevoegen of verwijderen aan/uit de groep
4. Stel een min (gegarandeerd) en maximale prestatielimiet in voor de databases in de pools
5. Bekijk het kostenoverzicht om eventuele wijzigingen in uw factuur te bekijken als gevolg van uw nieuwe selecties

![Elastisch poolconfiguratieblad](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>PowerShell: elastische pools en gepoolde databases beheren

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek.

Als u elastische groepen en samengevoegde databases van SQL Database wilt maken en beheren met Azure PowerShell, gebruikt u de volgende PowerShell-cmdlets. Zie [Azure PowerShell-module installeren](/powershell/azure/install-az-ps)als u PowerShell moet installeren of upgraden. Zie [SQL Database-servers](sql-database-servers.md)maken en beheren als u de SQL Database-servers voor een elastische groep wilt maken en beheren. Zie [Firewallregels maken en beheren met PowerShell](sql-database-firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules)als u firewallregels wilt maken en beheren.

> [!TIP]
> Zie [Elastische pools maken en databases verplaatsen tussen pools en uit een groep met PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) en [PowerShell gebruiken om een SQL-elastische groep in Azure SQL Database te controleren en te schalen](scripts/sql-database-monitor-and-scale-pool-powershell.md)voor PowerShell-voorbeeldscripts.
>

| Cmdlet | Beschrijving |
| --- | --- |
|[Nieuw-AzsqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Hiermee maakt u een elastische pool.|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|Krijgt elastische zwembaden en hun waarde van hun eigendommen.|
|[Set-azsqlelasticpool](/powershell/module/az.sql/set-azsqlelasticpool)|Wijzigt eigenschappen van een elastische pool Gebruik bijvoorbeeld de eigenschap **StorageMB** om de maximale opslag van een elastische pool aan te passen.|
|[Verwijder-azsqlelasticpool](/powershell/module/az.sql/remove-azsqlelasticpool)|Hiermee verwijdert u een elastische groep.|
|[Get-azsqlelasticpoolactiviteit](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Krijgt de status van bewerkingen op een elastische pool|
|[Nieuwe AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Hiermee maakt u een nieuwe database in een bestaande groep of als één database. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Hiermee haalt u een of meer databases op.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Hiermee stelt u eigenschappen in voor een database of verplaatst u een bestaande database naar, uit of tussen elastische pools.|
|[Verwijder-azsqldatabase](/powershell/module/az.sql/remove-azsqldatabase)|Hiermee verwijdert u een database.|

> [!TIP]
> Het maken van veel databases in een elastische groep kan enige tijd duren wanneer dit wordt gedaan met behulp van de portal of PowerShell-cmdlets die slechts één database tegelijk maken. Zie [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae)om creatie te automatiseren in een elastische pool.

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Azure CLI: elastische pools en gepoolde databases beheren

Als u elastische pools van SQL Database wilt maken en beheren met de [Azure CLI,](/cli/azure)gebruikt u de volgende Azure [CLI SQL Database-opdrachten.](/cli/azure/sql/db) Gebruik de [Cloud Shell](/azure/cloud-shell/overview) om de CLI in uw browser uit te voeren of [installeer](/cli/azure/install-azure-cli) de CLI op macOS, Linux of Windows.

> [!TIP]
> Zie [CLI gebruiken om een Azure SQL-database in een SQL-elastische groep te verplaatsen](scripts/sql-database-move-database-between-pools-cli.md) en Azure CLI gebruiken om een [SQL-elastische groep in Azure SQL Database te schalen.](scripts/sql-database-scale-pool-cli.md)
>

| Cmdlet | Beschrijving |
| --- | --- |
|[az sql elastic-pool maken](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Hiermee maakt u een elastische pool.|
|[az sql elastic-pool lijst](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Retourneert een lijst met elastische pools in een server.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Retourneert een lijst met databases in een elastische groep.|
|[az sql elastic-pool list-edities](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Bevat ook beschikbare DTU-instellingen voor de groep, opslaglimieten en per database-instellingen. Om de verbositeit te verminderen, worden standaard extra opslaglimieten en per database-instellingen verborgen.|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Werkt een elastische pool bij.|
|[az sql elastic-pool delete az sql elastic-pool delete az sql elastic-pool delete az sql](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Hiermee verwijdert u de elastische groep.|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL: gepoolde databases beheren

Als u databases wilt maken en verplaatsen binnen bestaande elastische pools of om informatie over een SQL Database-elastische groep met Transact-SQL terug te sturen, gebruikt u de volgende T-SQL-opdrachten. U deze opdrachten uitvoeren via de Azure-portal, [SQL Server Management Studio,](/sql/ssms/use-sql-server-management-studio) [Visual Studio Code](https://code.visualstudio.com/docs)of een ander programma dat verbinding kan maken met een Azure SQL Database-server en transact-SQL-opdrachten kan uitvoeren. Zie [Firewallregels beheren met Transact-SQL](sql-database-firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules)als u firewallregels wilt maken en beheren met T-SQL.

> [!IMPORTANT]
> U geen elastische groep azure SQL Database maken, bijwerken of verwijderen met Transact-SQL. U databases toevoegen of verwijderen uit een elastische groep en u DMVs gebruiken om informatie over bestaande elastische groepen terug te sturen.
>

| Opdracht | Beschrijving |
| --- | --- |
|[CREATE-database (Azure SQL-database)](/sql/t-sql/statements/create-database-azure-sql-database)|Hiermee maakt u een nieuwe database in een bestaande groep of als één database. U moet zijn verbonden met de hoofddatabase om een nieuwe database te maken.|
| [ALTER-DATABASE (Azure SQL-database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Een database verplaatsen naar, uit of tussen elastische pools.|
|[DROP-DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Hiermee verwijdert u een database.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Retourneert resourcegebruiksstatistieken voor alle elastische pools in een SQL Database-server. Voor elke elastische pool is er één rij voor elk rapportagevenster van 15 seconden (vier rijen per minuut). Dit omvat CPU, IO, Log, opslagverbruik en gelijktijdig gebruik van aanvragen/sessies door alle databases in de groep.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Retourneert de eventuele editie (servicelaag), servicedoelstelling (prijscategorie) en de eventuele naam van de elastische groep voor een Azure SQL-database of een Azure SQL Data Warehouse. Als u bent aangemeld bij de hoofddatabase in een Azure SQL Database-server, retourneert u informatie over alle databases. Voor Azure SQL Data Warehouse moet u zijn verbonden met de hoofddatabase.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>REST-API: Elastische pools en gepoolde databases beheren

Als u elastische groepen en samengevoegde databases van SQL Database wilt maken en beheren, gebruikt u deze REST API-aanvragen.

| Opdracht | Beschrijving |
| --- | --- |
|[Elastische groepen - Maken of bijwerken](https://docs.microsoft.com/rest/api/sql/elasticpools/createorupdate)|Hiermee maakt u een nieuwe elastische groep of werkt u een bestaande elastische groep bij.|
|[Elastische groepen - Verwijderen](https://docs.microsoft.com/rest/api/sql/elasticpools/delete)|Hiermee verwijdert u de elastische groep.|
|[Elastische zwembaden - Get](https://docs.microsoft.com/rest/api/sql/elasticpools/get)|Krijgt een elastisch zwembad.|
|[Elastische groepen - Lijst per server](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Retourneert een lijst met elastische pools in een server.|
|[Elastische groepen - Bijwerken](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Werkt een bestaande elastische pool bij.|
|[Elastische poolactiviteiten](https://docs.microsoft.com/rest/api/sql/elasticpoolactivities)|Retourneert elastische poolactiviteiten.|
|[Activiteiten in elastische pooldatabase](https://docs.microsoft.com/rest/api/sql/elasticpooldatabaseactivities)|Retourneert activiteit op databases in een elastische groep.|
|[Databases - Maken of bijwerken](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Hiermee maakt u een nieuwe database of werkt u een bestaande database bij.|
|[Databases - Get](https://docs.microsoft.com/rest/api/sql/databases/get)|Krijgt een database.|
|[Databases - Lijst per elastische pool](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Retourneert een lijst met databases in een elastische groep.|
|[Databases - Lijst per server](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Retourneert een lijst met databases in een server.|
|[Databases - Bijwerken](https://docs.microsoft.com/rest/api/sql/databases/update)|Hiermee wordt een bestaande database bijgewerkt.|

## <a name="next-steps"></a>Volgende stappen

* Zie [Ontwerppatronen voor SaaS-toepassingen met meerdere tenants met behulp van Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) voor meer informatie over ontwerppatronen voor SaaS-toepassingen met elastische pools.
* Zie [Inleiding tot de Wingtip SaaS-toepassing](sql-database-wtp-overview.md)voor een SaaS-zelfstudie met elastische pools.
