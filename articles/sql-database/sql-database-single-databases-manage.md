---
title: Servers en afzonderlijke databases maken, beheren en beheren
description: Meer informatie over het maken en beheren van SQL Database-servers en afzonderlijke databases.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 02c4d7ba545282e3654f3889dd8000af33c728c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268546"
---
# <a name="create-and-manage-sql-database-servers-and-single-databases-in-azure-sql-database"></a>SQL Database-servers en afzonderlijke databases maken en beheren in Azure SQL Database

U SQL Database-servers en afzonderlijke databases maken en beheren met behulp van de Azure-portal, PowerShell, Azure CLI, REST API en Transact-SQL.

## <a name="azure-portal-manage-sql-database-servers-and-single-databases"></a>Azure-portal: SQL-databaseservers en afzonderlijke databases beheren

U de brongroep van de Azure SQL-database van tevoren of tijdens het maken van de server zelf maken. Er zijn meerdere methoden om naar een nieuw SQL-serverformulier te gaan, door een nieuwe SQL-server te maken of als onderdeel van het maken van een nieuwe database.

### <a name="create-a-blank-sql-database-server"></a>Een lege SQL-databaseserver maken

Als u een SQL Database-server wilt maken met behulp van de [Azure-portal,](https://portal.azure.com)navigeert u naar een leeg SQL-serverformulier (logische server).  

### <a name="create-a-blank-or-sample-sql-single-database"></a>Een lege of voorbeeld SQL-database maken

Als u een Azure SQL-enkelvoudige database wilt maken met behulp van de [Azure-portal,](https://portal.azure.com)navigeert u naar een leeg SQL-databaseformulier en verstrekt u de gevraagde informatie. U de brongroep van de Azure SQL-database en de SQL Database-server van tevoren of tijdens het maken van de afzonderlijke database zelf maken. U een lege database maken of een voorbeelddatabase maken op basis van Adventure Works LT.

  ![database-1 maken](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Zie [DTU-gebaseerd inkoopmodel](sql-database-service-tiers-dtu.md) en [vCore-gebaseerd inkoopmodel](sql-database-service-tiers-vcore.md)voor informatie over het selecteren van de prijscategorie voor uw database.

Zie [Een beheerde instantie maken](sql-database-managed-instance-get-started.md) als u een beheerde instantie wilt maken

## <a name="manage-an-existing-sql-database-server"></a>Een bestaande SQL Database-server beheren

Als u een bestaande SQL Database-server wilt beheren, navigeert u naar de server met een aantal methoden, zoals van specifieke SQL-databasepagina, de **pagina SQL-servers** of de pagina **Alle bronnen.**

Als u een bestaande database wilt beheren, navigeert u naar de **pagina SQL-databases** en klikt u op de database die u wilt beheren. In de volgende schermafbeelding ziet u hoe u beginnen met het instellen van een firewall op serverniveau voor een database op de pagina **Overzicht** voor een database.

   ![serverfirewallregel](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> Zie [DTU-gebaseerd inkoopmodel](sql-database-service-tiers-dtu.md) en [vCore-gebaseerd inkoopmodel](sql-database-service-tiers-vcore.md)voor het configureren van prestatie-eigenschappen voor een database.
> [!TIP]
> Zie [Een Azure SQL-database maken in de Azure-portal](sql-database-single-database-get-started.md)voor een snelstart van een Azure-portal.

## <a name="powershell-manage-sql-database-servers-and-single-databases"></a>PowerShell: SQL Database-servers en afzonderlijke databases beheren

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek.

Als u Azure SQL Database-servers, enkele en gepoolde databases en SQL Database-serverfirewalls met Azure PowerShell wilt maken en beheren, gebruikt u de volgende PowerShell-cmdlets. Zie [Azure PowerShell-module installeren](/powershell/azure/install-az-ps)als u PowerShell moet installeren of upgraden.

> [!TIP]
> Zie [PowerShell gebruiken om een Azure SQL-single database te maken en een SQL Database-serverfirewallregel te configureren](scripts/sql-database-create-and-configure-database-powershell.md) en een [SQL-database met PowerShell te controleren en te schalen en te schalen.](scripts/sql-database-monitor-and-scale-database-powershell.md)

| Cmdlet | Beschrijving |
| --- | --- |
|[Nieuwe AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Een database maken |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Krijgt een of meer databases|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Hiermee stelt u eigenschappen in voor een database of verplaatst een bestaande database naar een elastische groep|
|[Verwijder-azsqldatabase](/powershell/module/az.sql/remove-azsqldatabase)|Een database verwijderen|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Een resourcegroep maken|
|[Nieuw-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Een server maken|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Retourneert informatie over servers|
|[Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver)|Wijzigt eigenschappen van een server|
|[Verwijder-azsqlserver](/powershell/module/az.sql/remove-azsqlserver)|Een server verwijderen|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Hiermee maakt u een firewallregel op serverniveau |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Krijgt firewallregels voor een server|
|[Set-azsqlserverfirewallregel instellen](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Wijzigt een firewallregel in een server|
|[Verwijder-azsqlserverfirewallregel](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Hiermee verwijdert u een firewallregel van een server.|
| Nieuwe azsqlserverVirtualNetworkRule | Hiermee maakt u een [*virtuele netwerkregel*](sql-database-vnet-service-endpoint-rule-overview.md)op basis van een subnet dat een eindpunt van de service voor virtuele netwerken is. |

## <a name="azure-cli-manage-sql-database-servers-and-single-databases"></a>Azure CLI: SQL Database-servers en afzonderlijke databases beheren

Als u Azure SQL-server, databases en firewalls wilt maken en beheren met [Azure CLI,](/cli/azure)gebruikt u de volgende [Azure CLI SQL Database-opdrachten.](/cli/azure/sql/db) Gebruik de [Cloud Shell](/azure/cloud-shell/overview) om de CLI in uw browser uit te voeren of [installeer](/cli/azure/install-azure-cli) de CLI op macOS, Linux of Windows. Zie [Elastische pools](sql-database-elastic-pool.md)voor het maken en beheren van elastische pools.

> [!TIP]
> Zie [Een Azure SQL-database maken met de Azure CLI](sql-database-cli-samples.md)voor een Quickstart in Azure CLI . Zie [CLI gebruiken om een Azure SQL-standaardtemaken een SQL-database te maken en een SQL Database-firewallregel](scripts/sql-database-create-and-configure-database-cli.md) te configureren en CLI gebruiken om een Azure [SQL-single-database te controleren en te schalen.](scripts/sql-database-monitor-and-scale-database-cli.md)
>

| Cmdlet | Beschrijving |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Een database maken|
|[az sql db lijst](/cli/azure/sql/db#az-sql-db-list)|Hiermee worden alle databases en gegevensmagazijnen in een server of alle databases in een elastische groep weergegeven|
|[az sql db list-edities](/cli/azure/sql/db#az-sql-db-list-editions)|Bevat lijsten met beschikbare servicedoelstellingen en opslaglimieten|
|[az sql db lijst-gebruik](/cli/azure/sql/db#az-sql-db-list-usages)|Retourneert databasegebruik|
|[az sql db show](/cli/azure/sql/db#az-sql-db-show)|Krijgt een database of datawarehouse|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Een database bijwerken|
|[az sql db verwijderen](/cli/azure/sql/db#az-sql-db-delete)|Een database verwijderen|
|[az group create](/cli/azure/group#az-group-create)|Een resourcegroep maken|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Een server maken|
|[az sql server lijst](/cli/azure/sql/server#az-sql-server-list)|Lijsten servers|
|[lijstgebruik van az sql-server](/cli/azure/sql/server#az-sql-server-list-usages)|Retourneert servergebruik|
|[az sql server show](/cli/azure/sql/server#az-sql-server-show)|Krijgt een server|
|[az sql server update](/cli/azure/sql/server#az-sql-server-update)|Een server bijwerkt|
|[az sql server verwijderen](/cli/azure/sql/server#az-sql-server-delete)|Een server verwijderen|
|[az sql server firewall-rule maken](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Hiermee maakt u een serverfirewallregel|
|[lijst met firewallregels van az sql server](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Hiermee worden de firewallregels op een server weergegeven|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Toont de details van een firewallregel|
|[update van de firewall-regel van AZ SQL Server](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Een firewallregel bijwerken|
|[firewall-regel voor az sql-server verwijderen](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Een firewallregel verwijderen|

## <a name="transact-sql-manage-sql-database-servers-and-single-databases"></a>Transact-SQL: SQL Database-servers en afzonderlijke databases beheren

Als u Azure SQL-server, databases en firewalls wilt maken en beheren met Transact-SQL, gebruikt u de volgende T-SQL-opdrachten. U deze opdrachten uitvoeren via de Azure-portal, [SQL Server Management Studio,](/sql/ssms/use-sql-server-management-studio) [Visual Studio Code](https://code.visualstudio.com/docs)of een ander programma dat verbinding kan maken met een Azure SQL Database-server en transact-SQL-opdrachten kan uitvoeren. Zie [Elastische pools](sql-database-elastic-pool.md)voor het beheren van elastische pools.

> [!TIP]
> Zie [Azure SQL Database: SQL Server Management Studio gebruiken om verbinding te maken en querygegevens te gebruiken voor](sql-database-connect-query-ssms.md)een snelle start met SQL Server Management Studio op Microsoft Windows. Zie [Azure SQL Database: Visual Studio Code gebruiken om](sql-database-connect-query-vscode.md)verbinding te maken en querygegevens te gebruiken voor een snelle start met Visual Studio Code op macOS, Linux of Windows.
> [!IMPORTANT]
> U een server niet maken of verwijderen met Transact-SQL.

| Opdracht | Beschrijving |
| --- | --- |
|[DATABASE MAKEN](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)|Hiermee maakt u één nieuwe database. U moet zijn verbonden met de hoofddatabase om een nieuwe database te maken.|
| [ALTER-DATABASE (Azure SQL-database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Wijzigt een Azure SQL-database. |
|[DROP-DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Hiermee verwijdert u een database.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Retourneert de eventuele editie (servicelaag), servicedoelstelling (prijscategorie) en de eventuele naam van de elastische groep voor een Azure SQL-database of een Azure SQL Data Warehouse. Als u bent aangemeld bij de hoofddatabase in een Azure SQL Database-server, retourneert u informatie over alle databases. Voor Azure SQL Data Warehouse moet u zijn verbonden met de hoofddatabase.|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Retourneert CPU-, IO- en geheugenverbruik voor een Azure SQL Database-database. Eén rij bestaat voor elke 15 seconden, zelfs als er geen activiteit in de database is.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Retourneert CPU-gebruiks- en opslaggegevens voor een Azure SQL-database. De gegevens worden verzameld en samengevoegd binnen intervallen van vijf minuten.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Bevat statistieken voor SQL Database-verbindingsgebeurtenissen, die een overzicht geven van de successen en fouten van de databaseverbinding. |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Retourneert succesvolle Azure SQL Database-databaseverbindingen, verbindingsfouten en impasses. U deze informatie gebruiken om uw databaseactiviteit bij te houden of op te lossen met SQL Database.|
|[sp_set_firewall_rule (Azure SQL-database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Hiermee worden de firewall-instellingen op serverniveau voor uw SQL Database-server genivelleerd of bijgewerkt. Deze opgeslagen procedure is alleen beschikbaar in de hoofddatabase voor de hoofdaanmelding op serverniveau. Een firewallregel op serverniveau kan alleen worden gemaakt met Transact-SQL nadat de eerste firewallregel op serverniveau is gemaakt door een gebruiker met machtigingen op Azure-niveau|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Retourneert informatie over de firewall-instellingen op serverniveau die zijn gekoppeld aan uw Microsoft Azure SQL Database.|
|[sp_delete_firewall_rule (Azure SQL-database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Hiermee verwijdert u firewall-instellingen op serverniveau van uw SQL Database-server. Deze opgeslagen procedure is alleen beschikbaar in de hoofddatabase voor de hoofdaanmelding op serverniveau.|
|[sp_set_database_firewall_rule (Azure SQL-database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Hiermee worden de firewallregels op databaseniveau voor uw Azure SQL Database of SQL Data Warehouse genivelleerd of bijgewerkt. Databasefirewallregels kunnen worden geconfigureerd voor de hoofddatabase en voor gebruikersdatabases in SQL Database. Database firewall regels zijn handig bij het gebruik van opgenomen database gebruikers. |
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Retourneert informatie over de firewall-instellingen op databaseniveau die zijn gekoppeld aan uw Microsoft Azure SQL Database. |
|[sp_delete_database_firewall_rule (Azure SQL-database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Hiermee verwijdert u firewall-instellingen op databaseniveau uit uw Azure SQL Database of SQL Data Warehouse. |

## <a name="rest-api-manage-sql-database-servers-and-single-databases"></a>REST API: SQL Database-servers en afzonderlijke databases beheren

Als u Azure SQL-server, databases en firewalls wilt maken en beheren, gebruikt u deze REST API-aanvragen.

| Opdracht | Beschrijving |
| --- | --- |
|[Servers - Maken of bijwerken](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Hiermee maakt of werkt u een nieuwe server bij.|
|[Servers - Verwijderen](https://docs.microsoft.com/rest/api/sql/servers/delete)|Hiermee verwijdert u een SQL-server.|
|[Servers - Get](https://docs.microsoft.com/rest/api/sql/servers/get)|Krijgt een server.|
|[Servers - Lijst](https://docs.microsoft.com/rest/api/sql/servers/list)|Retourneert een lijst met servers in een abonnement.|
|[Servers - Lijst per resourcegroep](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|Retourneert een lijst met servers in een resourcegroep.|
|[Servers - Bijwerken](https://docs.microsoft.com/rest/api/sql/servers/update)|Werkt een bestaande server bij.|
|[Databases - Maken of bijwerken](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Hiermee maakt u een nieuwe database of werkt u een bestaande database bij.|
|[Databases - Verwijderen](https://docs.microsoft.com/rest/api/sql/databases/delete)|Hiermee verwijdert u een database.|
|[Databases - Get](https://docs.microsoft.com/rest/api/sql/databases/get)|Krijgt een database.|
|[Databases - Lijst per elastische pool](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Retourneert een lijst met databases in een elastische groep.|
|[Databases - Lijst per server](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Retourneert een lijst met databases in een server.|
|[Databases - Bijwerken](https://docs.microsoft.com/rest/api/sql/databases/update)|Hiermee wordt een bestaande database bijgewerkt.|
|[Firewallregels - Maken of bijwerken](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|Hiermee maakt of werkt u een firewallregel bij.|
|[Firewallregels - Verwijderen](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|Hiermee verwijdert u een firewallregel.|
|[Firewallregels - Get](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|Krijgt een firewall regel.|
|[Firewallregels - Lijst per server](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|Geeft als resultaat een lijst met firewallregels.|

## <a name="next-steps"></a>Volgende stappen

- Zie Migreren naar Azure SQL Database voor meer informatie over het migreren van een SQL [Server-database](sql-database-single-database-migrate.md)naar Azure.
- Zie [Functies voor](sql-database-features.md)informatie over ondersteunde functies.
