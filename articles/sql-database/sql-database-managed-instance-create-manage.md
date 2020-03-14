---
title: Naslag informatie over beheer-API'S voor het beheerde exemplaar
description: Meer informatie over het maken en beheren van Azure SQL Database Managed instances.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268858"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Naslag informatie over beheerde API voor beheerde instanties van Azure SQL Database

U kunt Azure SQL Database beheerde exemplaren maken en beheren met behulp van de Azure Portal, Power shell, Azure CLI, REST API en Transact-SQL. In dit artikel vindt u een overzicht van de functies en API die u kunt gebruiken om een beheerd exemplaar te maken en te configureren.

## <a name="azure-portal-create-a-managed-instance"></a>Azure Portal: een beheerd exemplaar maken

Zie [Quick Start: Create a Azure SQL database Managed instance](sql-database-managed-instance-get-started.md)(Engelstalig) voor een Snelstartgids waarmee u kunt zien hoe u een Azure SQL database beheerd exemplaar maakt.

## <a name="powershell-create-and-manage-managed-instances"></a>Power shell: beheerde instanties maken en beheren

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek.

Als u beheerde exemplaren met Azure PowerShell wilt maken en beheren, gebruikt u de volgende Power shell-cmdlets. Als u Power shell wilt installeren of upgraden, raadpleegt u [Azure PowerShell-module installeren](/powershell/azure/install-az-ps).

> [!TIP]
> Zie voor voor beelden van Power shell-scripts [Quick start script: Azure SQL Managed instance maken met Power shell-bibliotheek](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Cmdlet | Beschrijving |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Hiermee maakt u een Azure SQL Database beheerd exemplaar |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Retourneert informatie over Azure SQL Managed instance|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Hiermee worden eigenschappen ingesteld voor een door Azure SQL Database beheerd exemplaar|
|[Remove-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Hiermee verwijdert u een exemplaar van Azure SQL Managed data base|
|[New-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Hiermee maakt u een Azure SQL Database Managed instance data base|
|[Get-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Hiermee wordt informatie geretourneerd over de data base van Azure SQL Managed instance|
|[Remove-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Hiermee verwijdert u een Data Base van een Azure SQL Managed data base-exemplaar|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Hiermee wordt een Data Base-exemplaar van Azure SQL Managed data base hersteld|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Azure CLI: beheerde instanties maken en beheren

Als u beheerde exemplaren wilt maken en beheren met [Azure cli](/cli/azure), gebruikt u de volgende [Azure cli SQL Managed instance](/cli/azure/sql/mi) -opdrachten. Gebruik de [Cloud Shell](/azure/cloud-shell/overview) om de CLI in uw browser uit te voeren of [installeer](/cli/azure/install-azure-cli) de CLI op macOS, Linux of Windows.

> [!TIP]
> Zie [werken met SQL Managed instance met Azure cli](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)voor een Snelstartgids van Azure cli.

| Cmdlet | Beschrijving |
| --- | --- |
|[AZ SQL mi Create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Hiermee maakt u een beheerd exemplaar|
|[AZ SQL mi List](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Beschik bare beheerde instanties weer geven|
|[AZ SQL mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|De Details voor een beheerd exemplaar ophalen|
|[AZ SQL mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Hiermee wordt een beheerd exemplaar bijgewerkt|
|[AZ SQL mi Delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Hiermee verwijdert u een beheerd exemplaar|
|[AZ SQL DEELB Create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Hiermee maakt u een beheerde data base|
|[AZ SQL DEELB List](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Een lijst met beschik bare beheerde data bases|
|[AZ SQL DEELB Restore](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Een beheerde data base herstellen|
|[AZ SQL DEELB Delete](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Hiermee verwijdert u een beheerde data base|

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL: instantie databases maken en beheren

Als u exemplaar database wilt maken en beheren nadat het beheerde exemplaar is gemaakt, gebruikt u de volgende T-SQL-opdrachten. U kunt deze opdrachten geven met behulp van de Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio) [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is). [Visual Studio code](https://code.visualstudio.com/docs)of andere Program ma's waarmee verbinding kan worden gemaakt met een Azure SQL database server en Transact-SQL-opdrachten kunnen worden door gegeven.

> [!TIP]
> Voor Quick starts die aangeven dat u een beheerd exemplaar moet configureren en er verbinding mee moet maken met SQL Server Management Studio in micro soft Windows, Zie [Quick Start: Azure VM configureren om verbinding te maken met een Azure SQL database beheerd exemplaar](sql-database-managed-instance-configure-vm.md) en [Quick Start: Configureer een punt-naar-site-verbinding met een Azure SQL database beheerde instantie van on-premises](sql-database-managed-instance-configure-p2s.md).
> [!IMPORTANT]
> U kunt een beheerd exemplaar niet maken of verwijderen met behulp van Transact-SQL.

| Opdracht | Beschrijving |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Hiermee maakt u een nieuwe Data Base voor een beheerd exemplaar. U moet zijn verbonden met de hoofd database om een nieuwe Data Base te kunnen maken.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Hiermee wijzigt u een Azure SQL Managed instance-data base.|

## <a name="rest-api-create-and-manage-managed-instances"></a>REST API: beheerde instanties maken en beheren

Als u beheerde exemplaren wilt maken en beheren, gebruikt u deze REST API aanvragen.

| Opdracht | Beschrijving |
| --- | --- |
|[Beheerde instanties: maken of bijwerken](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Hiermee wordt een beheerd exemplaar gemaakt of bijgewerkt.|
|[Beheerde instanties-verwijderen](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Hiermee verwijdert u een beheerd exemplaar.|
|[Beheerde instanties-ophalen](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Hiermee haalt u een beheerd exemplaar op.|
|[Beheerde instanties-lijst](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Retourneert een lijst met beheerde exemplaren in een abonnement.|
|[Beheerde instanties-lijst op resource groep](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Retourneert een lijst met beheerde exemplaren in een resource groep.|
|[Beheerde instanties-bijwerken](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Hiermee wordt een beheerd exemplaar bijgewerkt.|

## <a name="next-steps"></a>Volgende stappen

- Zie [migreren naar Azure SQL database](sql-database-single-database-migrate.md)voor meer informatie over het migreren van een SQL Server Data Base naar Azure.
- Zie [Functies](sql-database-features.md) voor meer informatie over ondersteunde functies.
