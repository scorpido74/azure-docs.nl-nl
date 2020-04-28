---
title: Servers
description: Meer informatie over Azure SQL Database servers en het beheer ervan.
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
ms.openlocfilehash: 7557ed43d9ecb8fc7a584e7e8239bc7ccb972e99
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75647166"
---
# <a name="azure-sql-database-servers-and-their-management"></a>Azure SQL Database servers en hun beheer

## <a name="what-is-an-azure-sql-database-server"></a>Wat is een Azure SQL Database-Server

Een SQL Database-Server is een logische constructie die fungeert als een centraal beheer punt voor meerdere data bases of [gepoolde](sql-database-elastic-pool.md) gegevens sets, [aanmeldingen](sql-database-manage-logins.md), [firewall regels](sql-database-firewall-configure.md), [controle regels](sql-database-auditing.md), [beleid voor detectie van dreigingen](sql-database-threat-detection.md)en failover- [groepen](sql-database-auto-failover-group.md) een SQL database server kan zich in een andere regio bevinden dan de resource groep. De SQL Database-Server moet bestaan voordat u de Azure-SQL database kunt maken. Alle data bases die door een SQL Database-Server worden beheerd, worden in dezelfde regio gemaakt als de SQL Database-Server.

Een SQL Database Server verschilt van een SQL Server exemplaar dat u mogelijk kent in de on-premises wereld. De SQL Database-Service biedt met name geen garanties met betrekking tot de locatie van de data bases ten opzichte van de SQL Database-Server die deze beheert, en geeft geen toegang tot of functies op instantie niveau. De instance-data bases in een beheerd exemplaar zijn daarentegen allemaal hetzelfde, op dezelfde manier als u bekend bent met SQL Server in de on-premises wereld.

Wanneer u een SQL Database Server maakt, geeft u een aanmeldings account voor de server en het wacht woord op dat over beheerders rechten voor de hoofd database op die server beschikt en alle data bases die op die server worden gemaakt. Dit eerste account is een SQL-aanmeldings account. Azure SQL Database ondersteunt SQL-verificatie en Azure Active Directory verificatie voor verificatie. Zie [data bases en aanmeldingen beheren in Azure SQL database](sql-database-manage-logins.md)voor meer informatie over aanmeldingen en verificatie. Windows-verificatie wordt niet ondersteund.

Een SQL Database-Server:

- Wordt gemaakt binnen een Azure-abonnement, maar kan met ingesloten resources naar een ander abonnement worden verplaatst
- Is de bovenliggende resource voor databases, elastische groepen en datawarehouses
- Voorziet in een naam ruimte voor data bases, elastische groepen en data warehouses
- Is een logische container met een sterke levens duur semantiek: een server verwijderen en de Inge sloten data bases, elastische groepen en data warehouses worden verwijderd
- Maakt deel uit van [Azure op rollen gebaseerd toegangs beheer (RBAC)](/azure/role-based-access-control/overview) : data bases, elastische groepen en data warehouses binnen een server nemen toegangs rechten van de server over
- Is een hoog element van de identiteit van data bases, elastische groepen en data warehouses voor Azure resource management-doel einden (Zie het URL-schema voor data bases en Pools)
- Groepeert resources in een regio
- Biedt een verbindingseindpunt voor databasetoegang (`<serverName>`.database.windows.net)
- Biedt toegang tot metagegevens van ingesloten resources via DMV's door verbinding te maken met een hoofddatabase
- Voorziet in het bereik voor beheer beleid dat van toepassing is op de data bases-aanmeldingen, firewall, controle, detectie van bedreigingen en dergelijke
- Wordt beperkt door een quotum binnen het bovenliggende abonnement (zes servers per abonnement standaard: [Zie de limieten voor abonnementen hier](../azure-resource-manager/management/azure-subscription-service-limits.md))
- Voorziet in het bereik voor database quota en DTU-of vCore-quotum voor de resources die het bevat (zoals 45.000 DTU)
- Is het versie bereik voor mogelijkheden die zijn ingeschakeld in opgenomen resources
- Hoofdaanmeldingen op serverniveau kunnen alle databases op een server beheren
- Kunnen aanmeldingen bevatten die vergelijkbaar zijn met die in de exemplaren van SQL Server op uw locaties die toegang hebben tot een of meer databases op de server, en waaraan beperkte beheerrechten kunnen worden verleend. Zie [Aanmeldingen](sql-database-manage-logins.md) voor meer informatie.
- De standaard sortering voor alle data bases die op een SQL Database `SQL_LATIN1_GENERAL_CP1_CI_AS`server zijn `LATIN1_GENERAL` gemaakt, is, waarbij Engels `CP1` (Verenigde Staten), code `CI` pagina 1252, is hoofdletter gevoelig en `AS` is accent gevoelig.

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-portal"></a>Azure SQL-servers, data bases en firewalls beheren met behulp van de Azure Portal

U kunt de resource groep van Azure SQL database van tevoren of tijdens het maken van de server maken. Er zijn meerdere methoden voor het ophalen van een nieuw SQL Server-formulier, hetzij door een nieuwe SQL Server te maken of als onderdeel van het maken van een nieuwe data base.

### <a name="create-a-blank-sql-database-server"></a>Een lege SQL Database-Server maken

Als u een Azure SQL Database Server (zonder een Data Base) met behulp van de [Azure Portal](https://portal.azure.com)wilt maken, gaat u naar een leeg SQL Server-formulier (logische server).  

### <a name="create-a-blank-or-sample-sql-database"></a>Een leeg of Voorbeeld SQL database maken

Als u een Azure-SQL database wilt maken met behulp van de [Azure Portal](https://portal.azure.com), gaat u naar een leeg SQL database formulier en geeft u de gevraagde informatie op. U kunt de resource groep van Azure SQL database en SQL Database Server van tevoren maken of tijdens het maken van de data base zelf. U kunt een lege data base maken of een voorbeeld database maken op basis van Adventure Works LT.

  ![database-1 maken](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Zie voor meer informatie over het selecteren van de prijs categorie voor uw Data Base op [DTU gebaseerd inkoop model](sql-database-service-tiers-dtu.md) en [op vCore gebaseerd aankoop model](sql-database-service-tiers-vcore.md).

Zie [een beheerd exemplaar maken](sql-database-managed-instance-get-started.md) voor informatie over het maken van een beheerd exemplaar

### <a name="manage-an-existing-sql-server"></a>Een bestaande SQL Server beheren

Als u een bestaande server wilt beheren, gaat u naar de-server met behulp van een aantal methoden, bijvoorbeeld vanaf een specifieke SQL database pagina, de pagina **SQL servers** of de pagina **alle resources** .

Als u een bestaande Data Base wilt beheren, gaat u naar de pagina **SQL-data bases** en klikt u op de data base die u wilt beheren. In de volgende scherm afbeelding ziet u hoe u een firewall op server niveau instelt voor een Data Base op de pagina **overzicht** voor een Data Base.

   ![serverfirewallregel](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> Zie op [DTU gebaseerd inkoop model](sql-database-service-tiers-dtu.md) en op [vCore gebaseerd inkoop model](sql-database-service-tiers-vcore.md)voor informatie over het configureren van prestatie-eigenschappen voor een Data Base.
> [!TIP]
> Zie [een Azure SQL database in de Azure portal maken](sql-database-single-database-get-started.md)voor een Azure Portal Snelstartgids.

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-powershell"></a>Azure SQL-servers, data bases en firewalls beheren met Power shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek.

Als u Azure SQL Server, data bases en firewalls met Azure PowerShell wilt maken en beheren, gebruikt u de volgende Power shell-cmdlets. Als u Power shell wilt installeren of upgraden, raadpleegt u [Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Zie [elastische Pools](sql-database-elastic-pool.md)voor het maken en beheren van elastische Pools.

| Cmdlet | Beschrijving |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Hiermee maakt u een Data Base |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Hiermee worden een of meer data bases opgehaald|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Eigenschappen instellen voor een Data Base of een bestaande data base verplaatsen naar een elastische pool|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Hiermee verwijdert u een Data Base|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Hiermee maakt u een resource groep|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Hiermee maakt u een server|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Retourneert informatie over servers|
|[Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver)|Hiermee wijzigt u de eigenschappen van een server|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Hiermee wordt een server verwijderd|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Hiermee maakt u een firewall regel op server niveau |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Hiermee worden de firewall regels voor een server opgehaald|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Hiermee wijzigt u een firewall regel in een server|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Hiermee verwijdert u een firewall regel van een server.|
| New-AzSqlServerVirtualNetworkRule | Hiermee maakt u een regel voor het [*virtuele netwerk*](sql-database-vnet-service-endpoint-rule-overview.md), op basis van een subnet dat Virtual Network Service-eind punt is. |

> [!TIP]
> Zie [een Azure SQL-data base maken met behulp van Power shell](sql-database-single-database-get-started.md)voor een Power shell-Snelstartgids. Zie Power shell-voorbeeld scripts [gebruiken om een Azure SQL-Data Base te maken en een firewall regel te configureren](scripts/sql-database-create-and-configure-database-powershell.md) en een [Azure SQL-Data Base te bewaken en te schalen met behulp van Power](scripts/sql-database-monitor-and-scale-database-powershell.md)shell.
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-cli"></a>Azure-servers, data bases en firewalls beheren met de Azure CLI

Als u Azure SQL Server, data bases en firewalls wilt maken en beheren met de [Azure cli](/cli/azure), gebruikt u de volgende [Azure cli SQL database](/cli/azure/sql/db) -opdrachten. Gebruik de [Cloud Shell](/azure/cloud-shell/overview) om de CLI in uw browser uit te voeren of [installeer](/cli/azure/install-azure-cli) de CLI op macOS, Linux of Windows. Zie [elastische Pools](sql-database-elastic-pool.md)voor het maken en beheren van elastische Pools.

| Cmdlet | Beschrijving |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Hiermee maakt u een Data Base|
|[AZ SQL DB List](/cli/azure/sql/db#az-sql-db-list)|Een lijst met alle data bases en data warehouses in een server, of alle data bases in een elastische pool|
|[AZ SQL DB List-edities](/cli/azure/sql/db#az-sql-db-list-editions)|Een lijst met beschik bare service doelstellingen en opslag limieten|
|[AZ SQL DB List-usages](/cli/azure/sql/db#az-sql-db-list-usages)|Retourneert database gebruik|
|[AZ SQL DB show](/cli/azure/sql/db#az-sql-db-show)|Hiermee wordt een Data Base of Data Warehouse opgehaald|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Hiermee wordt een Data Base bijgewerkt|
|[AZ SQL DB Delete](/cli/azure/sql/db#az-sql-db-delete)|Hiermee verwijdert u een Data Base|
|[az group create](/cli/azure/group#az-group-create)|Hiermee maakt u een resource groep|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Hiermee maakt u een server|
|[AZ SQL Server List](/cli/azure/sql/server#az-sql-server-list)|Servers weer geven|
|[AZ SQL Server List-usages](/cli/azure/sql/server#az-sql-server-list-usages)|Retourneert server gebruik|
|[AZ SQL Server show](/cli/azure/sql/server#az-sql-server-show)|Hiermee wordt een server opgehaald|
|[AZ SQL Server Update](/cli/azure/sql/server#az-sql-server-update)|Hiermee wordt een server bijgewerkt|
|[AZ SQL Server Delete](/cli/azure/sql/server#az-sql-server-delete)|Hiermee wordt een server verwijderd|
|[AZ SQL Server firewall-Rule Create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Hiermee maakt u een server firewall regel|
|[AZ SQL Server firewall-Rule List](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Een lijst met firewall regels op een server|
|[AZ SQL Server firewall-Rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Toont de details van een firewall regel|
|[AZ SQL Server firewall-Rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Hiermee wordt een firewall regel bijgewerkt|
|[AZ SQL Server firewall-Rule Delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Hiermee verwijdert u een firewall regel|

> [!TIP]
> Zie voor een Azure CLI Quick Start [een Azure SQL-data base maken met behulp van de Azure cli](sql-database-cli-samples.md). Zie voor voor beelden van Azure CLI-scripts [CLI gebruiken om een Azure SQL-Data Base te maken en een firewall regel te configureren](scripts/sql-database-create-and-configure-database-cli.md) en [om een enkele data base van Azure SQL te bewaken en te schalen](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-transact-sql"></a>Azure SQL-servers, data bases en firewalls beheren met Transact-SQL

Gebruik de volgende T-SQL-opdrachten om Azure SQL Server, data bases en firewalls te maken en te beheren met Transact-SQL. U kunt deze opdrachten geven met behulp van de Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio code](https://code.visualstudio.com/docs)of een ander programma dat verbinding kan maken met een Azure SQL database server en Transact-SQL-opdrachten kan passeren. Zie [elastische Pools](sql-database-elastic-pool.md)voor het beheren van elastische Pools.

> [!IMPORTANT]
> U kunt geen server maken of verwijderen met behulp van Transact-SQL.
>

| Opdracht | Beschrijving |
| --- | --- |
|[DATA base maken (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Een nieuwe database maken. U moet zijn verbonden met de hoofd database om een nieuwe Data Base te kunnen maken.|
| [ALTER data base (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Hiermee wijzigt u een Azure-SQL database. |
|[ALTER data base (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Hiermee wijzigt u een Azure SQL Data Warehouse.|
|[DROP data base (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Hiermee verwijdert u een Data Base.|
|[sys. database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Retourneert de editie (service tier), de service doelstelling (prijs categorie) en de naam van de elastische groep, indien van toepassing, voor een Azure-SQL database of een Azure SQL Data Warehouse. Als u bent aangemeld bij de hoofd database in een Azure SQL Database-Server, wordt er informatie over alle data bases geretourneerd. Voor Azure SQL Data Warehouse moet u verbinding hebben met de hoofd database.|
|[sys. dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Retourneert CPU, i/o en geheugen verbruik voor een Azure SQL Database-data base. Er bestaat één rij voor elke 15 seconden, zelfs als er geen activiteit in de data base is.|
|[sys. resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Retourneert het CPU-gebruik en de opslag gegevens voor een Azure SQL Database. De gegevens worden binnen een interval van vijf minuten verzameld en geaggregeerd.|
|[sys. database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Bevat statistieken voor SQL Database database connectiviteits gebeurtenissen, met een overzicht van geslaagde en mislukte database verbindingen. |
|[sys. event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Hiermee worden geslaagde Azure SQL Database database verbindingen, verbindings fouten en deadlocks geretourneerd. U kunt deze informatie gebruiken om uw database activiteit bij SQL Database bij te houden of problemen op te lossen.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Hiermee worden de firewall instellingen op server niveau voor uw SQL Database-Server gemaakt of bijgewerkt. Deze opgeslagen procedure is alleen beschikbaar in de hoofd database voor de principal-aanmelding op server niveau. Een firewall regel op server niveau kan alleen worden gemaakt met behulp van Transact-SQL nadat de eerste firewall regel op server niveau is gemaakt door een gebruiker met machtigingen op Azure-niveau|
|[sys. firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Retourneert informatie over de firewall instellingen op server niveau die zijn gekoppeld aan uw Microsoft Azure SQL Database.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Hiermee verwijdert u Firewall instellingen op server niveau van uw SQL Database-Server. Deze opgeslagen procedure is alleen beschikbaar in de hoofd database voor de principal-aanmelding op server niveau.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Hiermee worden de firewall regels op database niveau voor uw Azure SQL Database of SQL Data Warehouse gemaakt of bijgewerkt. De firewall regels voor de data base kunnen worden geconfigureerd voor de hoofd database en voor gebruikers databases op SQL Database. Database firewall regels zijn handig wanneer u Inge sloten database gebruikers gebruikt. |
|[sys. database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Retourneert informatie over de firewall instellingen op database niveau die zijn gekoppeld aan uw Microsoft Azure SQL Database. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Hiermee verwijdert u de firewall-instelling op database niveau van uw Azure SQL Database of SQL Data Warehouse. |

> [!TIP]
> Voor een Snelstartgids met SQL Server Management Studio in micro soft Windows raadpleegt u [Azure SQL database: gebruik SQL Server Management Studio om verbinding te maken en gegevens op te vragen](sql-database-connect-query-ssms.md). Zie [Azure SQL database: Visual Studio code gebruiken om verbinding te maken en gegevens op te vragen](sql-database-connect-query-vscode.md)voor een Snelstartgids met Visual Studio code op het MacOS, Linux of Windows.

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-rest-api"></a>Azure SQL-servers, data bases en firewalls beheren met behulp van de REST API

Als u Azure SQL Server, data bases en firewalls wilt maken en beheren, gebruikt u deze REST API aanvragen.

| Opdracht | Beschrijving |
| --- | --- |
|[Servers: maken of bijwerken](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Hiermee wordt een nieuwe server gemaakt of bijgewerkt.|
|[Servers-verwijderen](https://docs.microsoft.com/rest/api/sql/servers/delete)|Hiermee verwijdert u een SQL-Server.|
|[Servers: ophalen](https://docs.microsoft.com/rest/api/sql/servers/get)|Hiermee wordt een server opgehaald.|
|[Servers-lijst](https://docs.microsoft.com/rest/api/sql/servers/list)|Retourneert een lijst met servers.|
|[Servers-lijst op resource groep](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|Retourneert een lijst met servers in een resource groep.|
|[Servers-bijwerken](https://docs.microsoft.com/rest/api/sql/servers/update)|Hiermee wordt een bestaande server bijgewerkt.|
|[Data bases: maken of bijwerken](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Hiermee maakt u een nieuwe data base of werkt u een bestaande data base bij.|
|[Data bases-verwijderen](https://docs.microsoft.com/rest/api/sql/databases/delete)|Hiermee verwijdert u een Data Base.|
|[Data bases-ophalen](https://docs.microsoft.com/rest/api/sql/databases/get)|Hiermee haalt u een Data Base.|
|[Data bases-lijst op elastische pool](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Retourneert een lijst met data bases in een elastische pool.|
|[Data bases-lijst per server](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Retourneert een lijst met data bases op een server.|
|[Data bases-bijwerken](https://docs.microsoft.com/rest/api/sql/databases/update)|Hiermee wordt een bestaande data base bijgewerkt.|
|[Firewall regels: maken of bijwerken](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|Hiermee wordt een firewall regel gemaakt of bijgewerkt.|
|[Firewall regels-verwijderen](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|Hiermee verwijdert u een firewall regel.|
|[Firewall regels: ophalen](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|Hiermee wordt een firewall regel opgehaald.|
|[Firewall regels-lijst per server](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|Retourneert een lijst met firewall regels.|

## <a name="next-steps"></a>Volgende stappen

- Zie [migreren naar Azure SQL database](sql-database-single-database-migrate.md)voor meer informatie over het migreren van een SQL Server Data Base naar Azure.
- Zie [functies](sql-database-features.md)voor meer informatie over ondersteunde functies.
