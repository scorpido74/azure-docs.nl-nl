---
title: Pools voor Elastic Database beheren
description: Maak en beheer elastische Azure SQL-Pools.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 4ae2e84ab5f6108de4e8236c39371cf9658809d8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256326"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Elastische Pools beheren in Azure SQL Database

Met een elastische pool bepaalt u de hoeveelheid resources die de elastische pool nodig heeft voor het afhandelen van de werk belasting van de data bases en de hoeveelheid resources voor elke gegroepeerde Data Base.

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Azure Portal: elastische Pools en gegroepeerde Data bases beheren

Alle groeps instellingen vindt u op één locatie: de Blade **groep configureren** . Als u dit wilt doen, gaat u naar een elastische groep in de portal en klikt u op **groep configureren** , hetzij aan de bovenkant van de Blade of in het menu resource aan de linkerkant.

Hier kunt u een combi natie van de volgende wijzigingen maken en deze allemaal in één batch opslaan:

1. De servicelaag van de pool wijzigen
2. De prestaties (DTU of vCores) en de opslag omhoog of omlaag schalen
3. Data bases toevoegen aan of verwijderen uit de groep
4. Stel een minimale (gegarandeerde) en maximale prestatie limiet voor de data bases in de Pools in
5. Bekijk het kosten overzicht om eventuele wijzigingen in uw factuur weer te geven als gevolg van uw nieuwe selecties

![Blade voor elastische groeps configuratie](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>Power shell: elastische Pools en gegroepeerde Data bases beheren

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek.

Gebruik de volgende Power shell-cmdlets om SQL Database elastische Pools en gegroepeerde Data bases te maken en te beheren met Azure PowerShell. Als u Power shell wilt installeren of upgraden, raadpleegt u [Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Zie [SQL database-servers maken en beheren](sql-database-servers.md)voor het maken en beheren van de SQL database servers voor een elastische pool. Zie [firewall regels maken en beheren met Power shell](sql-database-firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules)voor meer informatie over het maken en beheren van firewall regels.

> [!TIP]
> Zie voor voor beelden van Power shell-scripts [elastische Pools maken en data bases verplaatsen tussen Pools en uit een pool met Power shell](scripts/sql-database-move-database-between-pools-powershell.md) en [Power shell gebruiken om een elastische SQL-groep in Azure SQL database te controleren en te schalen](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Cmdlet | Beschrijving |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Hiermee maakt u een elastische pool.|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|Hiermee haalt u elastische Pools en de bijbehorende eigenschaps waarden op.|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|Hiermee worden de eigenschappen van een elastische pool gewijzigd. Gebruik bijvoorbeeld de eigenschap **StorageMB** om de maximale opslag van een elastische pool te wijzigen.|
|[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|Hiermee verwijdert u een elastische pool.|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Hiermee wordt de status van bewerkingen in een elastische pool opgehaald|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Hiermee maakt u een nieuwe data base in een bestaande groep of als één data base. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Hiermee haalt u een of meer databases op.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Hiermee stelt u de eigenschappen voor een data base in, of verplaatst u een bestaande Data Base naar, van of tussen elastische Pools.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Hiermee verwijdert u een Data Base.|

> [!TIP]
> Het maken van een groot aantal data bases in een elastische pool kan enige tijd in beslag nemen met de portal-of Power shell-cmdlets waarmee slechts één data base tegelijk wordt gemaakt. Zie [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae)voor het automatiseren van het maken van een elastische pool.

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Azure CLI: elastische Pools en gegroepeerde Data bases beheren

Als u SQL Database elastische Pools wilt maken en beheren met de [Azure cli](/cli/azure), gebruikt u de volgende [Azure cli SQL database](/cli/azure/sql/db) -opdrachten. Gebruik de [Cloud Shell](/azure/cloud-shell/overview) om de CLI in uw browser uit te voeren of [installeer](/cli/azure/install-azure-cli) de CLI op macOS, Linux of Windows.

> [!TIP]
> Zie voor voor beelden van Azure CLI-scripts [CLI gebruiken om een Azure-SQL database te verplaatsen in een elastische SQL-pool](scripts/sql-database-move-database-between-pools-cli.md) en [Azure CLI te gebruiken om een elastische SQL-groep in Azure SQL database te schalen](scripts/sql-database-scale-pool-cli.md).
>

| Cmdlet | Beschrijving |
| --- | --- |
|[AZ SQL Elastic-pool Create](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Hiermee maakt u een elastische pool.|
|[AZ SQL Elastic-pool List](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Retourneert een lijst met elastische Pools in een server.|
|[AZ SQL Elastic-pool List-db's](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Retourneert een lijst met data bases in een elastische pool.|
|[AZ SQL Elastic-pool List-edities](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Bevat ook de beschik bare DTU-instellingen voor de groep, opslag limieten en instellingen per data base. Om uitgebreidere mogelijkheden te beperken, zijn extra opslag limieten en instellingen per data base standaard verborgen.|
|[AZ SQL Elastic-pool update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Hiermee werkt u een elastische pool bij.|
|[AZ SQL Elastic-pool Delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Hiermee verwijdert u de elastische pool.|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL: gegroepeerde Data bases beheren

Gebruik de volgende T-SQL-opdrachten om data bases te maken en te verplaatsen binnen bestaande elastische Pools of om informatie over een SQL Database elastische pool te retour neren met Transact-SQL. U kunt deze opdrachten geven met behulp van de Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio code](https://code.visualstudio.com/docs)of een ander programma dat verbinding kan maken met een Azure SQL database server en Transact-SQL-opdrachten kan passeren. Zie [firewall regels beheren met behulp van Transact-SQL](sql-database-firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules)om firewall regels te maken en beheren met behulp van T-SQL.

> [!IMPORTANT]
> Het is niet mogelijk om een Azure SQL Database elastische pool te maken, bij te werken of te verwijderen met behulp van Transact-SQL. U kunt data bases toevoegen aan of verwijderen uit een elastische pool en u kunt Dmv's gebruiken om informatie over bestaande elastische Pools te retour neren.
>

| Opdracht | Beschrijving |
| --- | --- |
|[DATA base maken (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Hiermee maakt u een nieuwe data base in een bestaande groep of als één data base. U moet zijn verbonden met de hoofd database om een nieuwe Data Base te kunnen maken.|
| [ALTER data base (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Een Data Base verplaatsen naar, uit of tussen elastische Pools.|
|[DROP data base (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Hiermee verwijdert u een Data Base.|
|[sys. elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Retourneert statistieken over het resource gebruik voor alle elastische Pools in een SQL Database-Server. Voor elke elastische pool is er één rij voor elk venster van 15 seconden (vier rijen per minuut). Dit omvat CPU, IO, logboek, opslag verbruik en gelijktijdige aanvraag/sessie gebruik door alle data bases in de pool.|
|[sys. database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Retourneert de editie (service tier), de service doelstelling (prijs categorie) en de naam van de elastische groep, indien van toepassing, voor een Azure-SQL database of een Azure SQL Data Warehouse. Als u bent aangemeld bij de hoofd database in een Azure SQL Database-Server, wordt er informatie over alle data bases geretourneerd. Voor Azure SQL Data Warehouse moet u verbinding hebben met de hoofd database.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>REST API: elastische Pools en gegroepeerde Data bases beheren

Gebruik deze REST API aanvragen voor het maken en beheren van SQL Database elastische Pools en gepoolde data bases.

| Opdracht | Beschrijving |
| --- | --- |
|[Elastische Pools-maken of bijwerken](https://docs.microsoft.com/rest/api/sql/elasticpools/createorupdate)|Hiermee maakt u een nieuwe elastische pool of werkt u een bestaande elastische pool bij.|
|[Elastische Pools-verwijderen](https://docs.microsoft.com/rest/api/sql/elasticpools/delete)|Hiermee verwijdert u de elastische pool.|
|[Elastische Pools-ophalen](https://docs.microsoft.com/rest/api/sql/elasticpools/get)|Hiermee haalt u een elastische pool op.|
|[Elastische Pools-lijst op server](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Retourneert een lijst met elastische Pools in een server.|
|[Elastische Pools-bijwerken](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Hiermee wordt een bestaande elastische pool bijgewerkt.|
|[Activiteiten voor elastische Pools](https://docs.microsoft.com/rest/api/sql/elasticpoolactivities)|Hiermee worden activiteiten voor elastische Pools geretourneerd.|
|[Activiteiten voor Elastic pool-data bases](https://docs.microsoft.com/rest/api/sql/elasticpooldatabaseactivities)|Hiermee wordt de activiteit in data bases binnen een elastische pool geretourneerd.|
|[Data bases: maken of bijwerken](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Hiermee maakt u een nieuwe data base of werkt u een bestaande data base bij.|
|[Data bases-ophalen](https://docs.microsoft.com/rest/api/sql/databases/get)|Hiermee haalt u een Data Base.|
|[Data bases-lijst op elastische pool](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Retourneert een lijst met data bases in een elastische pool.|
|[Data bases-lijst per server](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Retourneert een lijst met data bases op een server.|
|[Data bases-bijwerken](https://docs.microsoft.com/rest/api/sql/databases/update)|Hiermee wordt een bestaande data base bijgewerkt.|

## <a name="next-steps"></a>Volgende stappen

* Zie [Ontwerppatronen voor SaaS-toepassingen met meerdere tenants met behulp van Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) voor meer informatie over ontwerppatronen voor SaaS-toepassingen met elastische pools.
* Zie [Introduction to the Wingtip SaaS Application](sql-database-wtp-overview.md)voor een SaaS-zelf studie over het gebruik van elastische Pools.
