---
title: Power shell-voor beeld-SQL Data Sync synchronisatie schema bijwerken
description: Azure PowerShell-voorbeeldscript voor het bijwerken van het synchronisatieschema voor SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 0106b80259083c6e5e3e527063a18aae2e7c6cee
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74421615"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>PowerShell gebruiken voor het bijwerken van het synchronisatieschema in een bestaande synchronisatiegroep

Met dit PowerShell-voorbeeld werkt u het synchronisatieschema bij in een bestaande synchronisatiegroep van SQL Data Sync. Wanneer u meerdere tabellen synchroniseert, helpt dit script u om het synchronisatieschema efficiënt bij te werken. In dit voorbeeld wordt het gebruik van het script **UpdateSyncSchema** gedemonstreerd, dat beschikbaar is op GitHub als [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om Power shell lokaal te installeren en te gebruiken, hebt u voor deze zelf studie AZ Power shell 1.4.0 of hoger nodig. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

Zie [Gegevens synchroniseren tussen meerdere cloud- en on-premises databases met SQL Data Sync](../sql-database-sync-data.md) voor een overzicht van SQL Data Sync.

> [!IMPORTANT]
> Azure SQL Data Sync biedt op dit moment geen ondersteuning voor het beheerde exemplaar van Azure SQL database.

## <a name="examples"></a>Voorbeelden

### <a name="add-all-tables-to-the-sync-schema"></a>Alle tabellen aan het synchronisatie schema toevoegen

In het volgende voorbeeld wordt het databaseschema vernieuwd en worden alle geldige tabellen in de hubdatabase aan het synchronisatieschema toegevoegd.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="add-and-remove-tables-and-columns"></a>Tabellen en kolommen toevoegen en verwijderen

In het volgende voorbeeld worden `[dbo].[Table1]` en `[dbo].[Table2].[Column1]` aan het synchronisatieschema toegevoegd en wordt `[dbo].[Table3]` verwijderd.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Scriptparameters

Het script **UpdateSyncSchema** heeft de volgende parameters:

| Parameter | Opmerkingen |
|---|---|
| $subscriptionId | Het abonnement waarin de synchronisatiegroep wordt gemaakt. |
| $resourceGroupName | De resourcegroep waarin de synchronisatiegroep wordt gemaakt.|
| $serverName | De servernaam van de hubdatabase.|
| $databaseName | De naam van de hubdatabase. |
| $syncGroupName | De naam van de synchronisatiegroep. |
| $memberName | Geef de lidnaam op als u het databaseschema wilt laden uit het synchronisatielid in plaats van uit de hubdatabase. Als u het databaseschema uit de hub wilt laden, laat u deze parameter leeg. |
| $timeoutInSeconds | Time-out waarna het script het databaseschema vernieuwt. De standaardwaarde is 900 seconden. |
| $refreshDatabaseSchema | Geef aan of het script het databaseschema moet vernieuwen. Als uw databaseschema na de vorige configuratie is gewijzigd, bijvoorbeeld, als u een nieuwe tabel of kolom hebt toegevoegd, moet u het schema vernieuwen voordat u het opnieuw configureert. De standaardinstelling is onwaar. |
| $addAllTables | Als deze waarde op waar is ingesteld, worden alle geldige tabellen en kolommen aan het synchronisatieschema toegevoegd. De waarden van $TablesAndColumnsToAdd en $TablesAndColumnsToRemove worden genegeerd. |
| $tablesAndColumnsToAdd | Hier kunt u opgeven welke tabellen en kolommen aan het synchronisatieschema moeten worden toegevoegd. Elke tabel- of kolomnaam moet volledig worden gescheiden met de schemanaam. Bijvoorbeeld: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. U kunt meerdere tabel- of kolomnamen opgeven, gescheiden door een komma (,). |
| $tablesAndColumnsToRemove | Hier kunt u opgeven welke tabellen en kolommen uit het synchronisatieschema moeten worden verwijderd. Elke tabel- of kolomnaam moet volledig worden gescheiden met de schemanaam. Bijvoorbeeld: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. U kunt meerdere tabel- of kolomnamen opgeven, gescheiden door een komma (,). |

## <a name="script-explanation"></a>Uitleg van het script

Het script **UpdateSyncSchema** gebruikt de volgende opdrachten. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncgroup) | Retourneert informatie over een synchronisatiegroep. |
| [Update-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncgroup) | Werkt een synchronisatiegroep bij. |
| [Get-AzSqlSyncMember](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncmember) | Retourneert informatie over een synchronisatielid. |
| [Get-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncschema) | Retourneert informatie over een synchronisatieschema. |
| [Update-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncschema) | Werkt een synchronisatieschema bij. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie over Azure PowerShell](/powershell/azure/overview) voor meer informatie over Azure PowerShell.

Aanvullende SQL database Power shell-voorbeeld scripts vindt u in [Azure SQL database Power shell](../sql-database-powershell-samples.md)-script.

Zie de volgende onderwerpen voor meer informatie over SQL Data Sync:

- Overzicht: [Gegevens synchroniseren tussen meerdere cloud- en on-premises databases met SQL Data Sync](../sql-database-sync-data.md)
- Data Sync instellen
    - In de portal: [Zelfstudie: SQL Data Sync instellen om gegevens te synchroniseren tussen Azure SQL Database en SQL Server on-premises](../sql-database-get-started-sql-data-sync.md)
    - Met PowerShell
        - [PowerShell gebruiken om meerdere Azure SQL-databases te synchroniseren](sql-database-sync-data-between-sql-databases.md)
        - [PowerShell gebruiken om te synchroniseren tussen een Azure SQL-database en een on-premises database](sql-database-sync-data-between-azure-onprem.md)
- Data Sync-agent: [Data Sync-agent voor Azure SQL Data Sync](../sql-database-data-sync-agent.md)
- Best practices: [Best practices voor Azure SQL Data Sync](../sql-database-best-practices-data-sync.md)
- SQL Data Sync controleren [met Azure monitor-logboeken](../sql-database-sync-monitor-oms.md)
- Problemen oplossen: [Problemen met Azure SQL Data Sync oplossen](../sql-database-troubleshoot-data-sync.md)
- Het synchronisatieschema bijwerken
    - Met Transact-SQL: [De replicatie van schemawijzigingen in Azure SQL Data Sync automatiseren](../sql-database-update-sync-schema.md)

Zie de volgende onderwerpen voor meer informatie over SQL Database:

- [Wat is de Azure SQL Database-service?](../sql-database-technical-overview.md)
- [Database Lifecycle Management (DLM)](https://msdn.microsoft.com/library/jj907294.aspx)
