---
title: Api-verwijzing voor beheerde instantie
description: Meer informatie over het maken en beheren van Azure SQL Database Managed Instances.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 713217a933c646cc4d04759f5697bbc0312827ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268858"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Beheerde API-verwijzing voor Azure SQL Database Managed Instances

U Azure SQL Database Managed Instances maken en beheren met behulp van de Azure-portal, PowerShell, Azure CLI, REST API en Transact-SQL. In dit artikel vindt u een overzicht van functies en API die u gebruiken om Beheerde instantie te maken en te configureren.

## <a name="azure-portal-create-a-managed-instance"></a>Azure-portal: een beheerde instantie maken

Zie [Quickstart: Een Azure SQL Database Managed Instance maken](sql-database-managed-instance-get-started.md)voor een snelle start waarin u laat zien hoe u een Azure SQL Database Managed Instance maakt.

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell: beheerde exemplaren maken en beheren

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek.

Als u beheerde exemplaren wilt maken en beheren met Azure PowerShell, gebruikt u de volgende PowerShell-cmdlets. Zie [Azure PowerShell-module installeren](/powershell/azure/install-az-ps)als u PowerShell moet installeren of upgraden.

> [!TIP]
> Zie [Snelstartscript voor PowerShell-voorbeeldscripts: Azure SQL Managed Instance maken met PowerShell-bibliotheek](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Cmdlet | Beschrijving |
| --- | --- |
|[Nieuw-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Hiermee maakt u een Azure SQL Database Managed Instance |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Retourneert informatie over Azure SQL Managed Instance|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Stelt eigenschappen in voor een Azure SQL Database Managed Instance|
|[Remove-azsqlinstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Hiermee verwijdert u een Azure SQL Managed Database Instance|
|[Nieuwe azsqlinstancedatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Hiermee maakt u een Azure SQL Database Managed Instance-database|
|[Get-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Retourneert informatie over Azure SQL Managed Instance-database|
|[Database verwijderen-azsqlinstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Hiermee verwijdert u een Azure SQL Managed Database Instance-database|
|[Database restore-azsqlinstancedatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Hiermee herstelt u een Azure SQL Managed Database Instance-database|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Azure CLI: beheerde exemplaren maken en beheren

Als u beheerde exemplaren wilt maken en beheren met [Azure CLI,](/cli/azure)gebruikt u de volgende opdrachten [voor Azure CLI SQL Managed Instance.](/cli/azure/sql/mi) Gebruik de [Cloud Shell](/azure/cloud-shell/overview) om de CLI in uw browser uit te voeren of [installeer](/cli/azure/install-azure-cli) de CLI op macOS, Linux of Windows.

> [!TIP]
> Zie Werken met SQL [Managed Instance met Azure CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)voor een Quickstart van Azure CLI.

| Cmdlet | Beschrijving |
| --- | --- |
|[az sql mi maken](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Een beheerde instantie maken|
|[az sql mi lijst](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Beschikbare beheerde exemplaren weergeven|
|[az sql mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|De details voor een beheerde instantie opvragen|
|[az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Een beheerde instantie bijwerken|
|[az sql mi delete az sql mi delete az sql mi delete az sql](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Een beheerde instantie verwijderen|
|[az sql midb maken](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Een beheerde database maken|
|[az sql midb lijst](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Lijsten met beschikbare beheerde databases|
|[az sql midb herstellen](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Een beheerde database herstellen|
|[az sql midb verwijderen](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Een beheerde database verwijderen|

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL: instancedatabases maken en beheren

Als u de instantiedatabase wilt maken en beheren nadat de beheerde instantie is gemaakt, gebruikt u de volgende T-SQL-opdrachten. U deze opdrachten uitvoeren via de Azure-portal SQL [Server Management Studio](/sql/ssms/use-sql-server-management-studio), Azure Data [Studio.](https://docs.microsoft.com/sql/azure-data-studio/what-is) [Visual Studio Code](https://code.visualstudio.com/docs)of een ander programma dat verbinding kan maken met een Azure SQL Database-server en transact-SQL-opdrachten kan uitvoeren.

> [!TIP]
> Zie [Quickstart: Azure VM configureren om verbinding te maken met een Azure SQL Database Managed Instance](sql-database-managed-instance-configure-vm.md) en [Quickstart: Configureer een point-to-site verbinding met een Azure SQL Database Managed Instance en Quickstart: Configureer een point-to-site verbinding met een Azure SQL Database Managed Instance van on-premises](sql-database-managed-instance-configure-p2s.md).
> [!IMPORTANT]
> U een beheerde instantie niet maken of verwijderen met Transact-SQL.

| Opdracht | Beschrijving |
| --- | --- |
|[DATABASE MAKEN](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Hiermee maakt u een nieuwe database voor beheerde instanties. U moet zijn verbonden met de hoofddatabase om een nieuwe database te maken.|
| [DATABASE WIJZIGEN](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Hiermee wijzigt u een Azure SQL Managed Instance-database.|

## <a name="rest-api-create-and-manage-managed-instances"></a>REST-API: beheerde exemplaren maken en beheren

Als u Beheerde exemplaren wilt maken en beheren, gebruikt u deze REST API-aanvragen.

| Opdracht | Beschrijving |
| --- | --- |
|[Beheerde exemplaren - Maken of bijwerken](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Hiermee maakt of werkt u een beheerde instantie samen.|
|[Beheerde exemplaren - Verwijderen](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Hiermee verwijdert u een beheerde instantie.|
|[Beheerde exemplaren - Oppakken](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Krijgt een beheerde instantie.|
|[Beheerde exemplaren - Lijst](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Retourneert een lijst met beheerde exemplaren in een abonnement.|
|[Beheerde instanties - Lijst per resourcegroep](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Retourneert een lijst met beheerde instanties in een resourcegroep.|
|[Beheerde exemplaren - Bijwerken](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Hiermee wordt een beheerde instantie bijgewerkt.|

## <a name="next-steps"></a>Volgende stappen

- Zie Migreren naar Azure SQL Database voor meer informatie over het migreren van een SQL [Server-database](sql-database-single-database-migrate.md)naar Azure.
- Zie [Functies voor](sql-database-features.md)informatie over ondersteunde functies.
