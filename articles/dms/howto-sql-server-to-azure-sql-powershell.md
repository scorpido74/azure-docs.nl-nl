---
title: 'Powershell: SQL Server migreren naar SQL Database'
titleSuffix: Azure Database Migration Service
description: Lees of u migreren van on-premises SQL Server naar Azure SQL Database met Azure PowerShell met de Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: f63f79402b457017257f1762c6ddc7e04c0ee1af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650687"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-database-using-azure-powershell"></a>SQL Server on-premises migreren naar Azure SQL Database met Azure PowerShell

In dit artikel migreert u de **Adventureworks2012-database** die is hersteld naar een on-premises exemplaar van SQL Server 2016 of hoger naar een Azure SQL-database met Microsoft Azure PowerShell. U databases migreren van een on-premises SQL Server-instantie naar Azure SQL Database met behulp van de `Az.DataMigration` module in Microsoft Azure PowerShell.

In dit artikel leert u het volgende:
> [!div class="checklist"]
>
> * Maak een resourcegroep.
> * De Azure-portal gebruiken om een Azure Database Migration Service-exemplaar te maken.
> * Een migratieproject maken in een instantie Azure Database Migration Service.
> * De migratie uitvoeren.

## <a name="prerequisites"></a>Vereisten

Als u deze stappen wilt uitvoeren, moet u het volgende doen:

* [SQL Server 2016 of hoger](https://www.microsoft.com/sql-server/sql-server-downloads) (elke editie)
* Het TCP/IP-protocol inschakelen, dat standaard is uitgeschakeld met sql server express-installatie. Schakel het TCP/IP-protocol in door het artikel [Een ServerNetwerkprotocol in- of uitschakelen te](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)volgen.
* Uw Windows Firewall configureren [voor toegang tot database-engine.](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)
* Een Azure SQL Database-exemplaar. U een Azure SQL Database-instantie maken door de details in het artikel [Een Azure SQL-database maken in de Azure-portal te](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)volgen.
* [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 of hoger.
* Een Microsoft Azure Virtual Network hebben gemaakt met behulp van het Azure Resource Manager-implementatiemodel, dat de Azure Database Migration Service biedt met site-to-site-connectiviteit met uw on-premises bronservers met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)
* De beoordeling van uw on-premises database en schemamigratie hebben voltooid met behulp van gegevensmigratieassistent zoals beschreven in het artikel [Een SQL Server-migratiebeoordeling uitvoeren](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
* De Az.DataMigration-module downloaden en installeren vanuit de PowerShell Gallery met behulp van [PowerShell-cmdlet van installatiemodule](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1); zorg ervoor dat u het powershell-opdrachtvenster opent met uitvoeren als administrator.
* Om ervoor te zorgen dat de referenties die worden gebruikt om verbinding te maken met de sql server-instantie van de bron, de machtiging [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) hebben.
* Als u ervoor wilt zorgen dat de referenties die worden gebruikt om verbinding te maken met de doelstelling van Azure SQL DB-instantie, de machtiging CONTROLEDATABASE heeft op de doelazure SQL Database-databases.
* Een Azure-abonnement. Als je nog geen account hebt, maak je een [gratis](https://azure.microsoft.com/free/) account aan voordat je begint.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Aanmelden bij uw Microsoft Azure-abonnement

Gebruik de aanwijzingen in het artikel [Log in met Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps) om u aan te melden bij uw Azure-abonnement met PowerShell.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Maak een resourcegroep voordat u een virtuele machine maken.

Maak een resourcegroep met de opdracht [Nieuw-AzResourceGroep.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* in het *EastUS-gebied* geareerd.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Een instantie van Azure Database Migration Service maken

U een nieuw exemplaar van Azure `New-AzDataMigrationService` Database Migration Service maken met behulp van de cmdlet. Deze cmdlet verwacht de volgende vereiste parameters:

* *Naam Azure Resource Group*. U de opdracht [Nieuw-AzResourceGroep](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) gebruiken om de Azure Resource-groep te maken zoals eerder weergegeven en de naam als parameter op te geven.
* *Servicenaam*. Tekenreeks die overeenkomt met de gewenste unieke servicenaam voor Azure Database Migration Service 
* *Locatie*. Hiermee geeft u de locatie van de service op. Een Azure-datacenterlocatie opgeven, zoals West-VS of Zuidoost-Azië
* *Sku.* Deze parameter komt overeen met de naam DMS Sku. De momenteel ondersteunde Sku-naam is *GeneralPurpose_4vCores*.
* *Virtuele subnet-id*. U cmdlet [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) gebruiken om een subnet te maken. 

In het volgende voorbeeld wordt een service met de naam *MyDMS* gemaakt in de resourcegroep *MyDMSResourceGroup* in de regio *Oost-VS* met behulp van een virtueel netwerk met de naam *MyVNET* en subnet genaamd *MySubnet*.

```powershell
 $vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Maak een migratieproject

Nadat u een instantie Azure Database Migration Service hebt gemaakt, maakt u een migratieproject. Een Azure Database Migration Service-project vereist verbindingsgegevens voor zowel de bron- als doelinstanties, evenals een lijst met databases die u wilt migreren als onderdeel van het project.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Een object Databaseverbindingsgegevens maken voor de bron- en doelverbindingen

U een object DatabaseConnection `New-AzDmsConnInfo` Info maken met de cmdlet. Deze cmdlet verwacht de volgende parameters:

* *ServerType*. Het type databaseverbinding dat is aangevraagd, bijvoorbeeld SQL, Oracle of MySQL. SQL gebruiken voor SQL Server en Azure SQL.
* *DataSource*. De naam of het IP-adres van een SQL Server-instantie of Azure SQL-database.
* *AuthType*. Het verificatietype voor verbinding, dat sqlauthentication of WindowsAuthentication kan zijn.
* De parameter *TrustServerCertificate* stelt een waarde in die aangeeft of het kanaal is versleuteld terwijl u de certificaatketen omzeilt om de vertrouwensrelatie te valideren. Waarde kan waar of onwaar zijn.

In het volgende voorbeeld wordt het object Connection Info gemaakt voor bron SQL Server genaamd MySourceSQLServer met sql-verificatie:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

In het volgende voorbeeld wordt het maken van verbindingsgegevens voor een Azure SQL-databaseserver genaamd SQLAzureTarget met sql-verificatie weergegeven:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "sqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Databases voor het migratieproject verstrekken

Maak een `AzDataMigrationDatabaseInfo` lijst met objecten die databases opgeeft als onderdeel van het Azure Database Migration-project dat kan worden opgegeven als parameter voor het maken van het project. De Cmdlet `New-AzDataMigrationDatabaseInfo` kan worden gebruikt om AzDataMigrationDatabaseInfo te maken. 

In het `AzDataMigrationDatabaseInfo` volgende voorbeeld wordt project gemaakt voor de **AdventureWorks2016-database** en wordt deze toegevoegd aan de lijst die moet worden opgegeven als parameter voor het maken van projecten.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Een projectobject maken

Ten slotte u azure databasemigratieproject genaamd *MyDMSProject* in *Oost-VS* maken met behulp van `New-AzDataMigrationProject` de eerder gemaakte bron- en doelverbindingen en de lijst met databases die moeten migreren.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLDB `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Een migratietaak maken en starten

Maak en start ten slotte de taak Azure Database Migration. Azure Database Migration-taak vereist verbindingsgegevens voor zowel bron- als doel- en lijst met databasetabellen die moeten worden gemigreerd naast de informatie die al is verstrekt met het project dat als vereiste is gemaakt.

### <a name="create-credential-parameters-for-source-and-target"></a>Referentieparameters voor bron en doel maken

Verbindingsbeveiligingsreferenties kunnen worden gemaakt als een [PSCredential-object.](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0)

In het volgende voorbeeld wordt de creatie van *PSCredential-objecten* voor zowel bron- als doelverbindingen weergegeven die wachtwoorden bevatten als tekenreeksvariabelen *$sourcePassword* en *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Een tabelkaart maken en bron- en doelparameters voor migratie selecteren

Een andere parameter die nodig is voor migratie is het toewijzen van tabellen van bron naar doel die moeten worden gemigreerd. Maak woordenboek met tabellen die een toewijzing biedt tussen bron- en doeltabellen voor migratie. In het volgende voorbeeld wordt het in kaart brengen tussen bron- en doeltabellen Human Resources-schema voor de AdventureWorks 2016-database weergegeven.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

De volgende stap is het selecteren van de bron- en doeldatabases `New-AzDmsSelectedDB` en het toewijzen van tabeltoewijzing om als parameter te migreren met behulp van de cmdlet, zoals in het volgende voorbeeld wordt weergegeven:

```powershell
$selectedDbs = New-AzDmsSelectedDB -MigrateSqlServerSqlDb -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-the-migration-task-and-start-it"></a>De migratietaak maken en starten

Gebruik `New-AzDataMigrationTask` de cmdlet om een migratietaak te maken en te starten. Deze cmdlet verwacht de volgende parameters:

* *Taaktype*. Type migratietaak die moet worden gemaakt voor SQL Server naar Azure SQL Database-migratietype *MigrateSqlServerSqlDb* wordt verwacht. 
* *Naam resourcegroep*. Naam van azure-resourcegroep waarin de taak moet worden gemaakt.
* *ServiceNaam*. Instantie Azure Database Migration Service waarin de taak kan worden gemaakt.
* *ProjectNaam*. Naam van azure database migration service project waarin de taak te maken. 
* *Taaknaam*. Naam van de taak die moet worden gemaakt. 
* *BronVerbinding*. AzDmsConnInfo-object dat de SQL Server-verbinding van de bron vertegenwoordigt.
* *Doelverbinding*. AzDmsConnInfo-object dat de verbinding met Azure SQL Database van doel vertegenwoordigt.
* *SourceCred*. [PSCredential-object](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) voor verbinding met de bronserver.
* *TargetCred*. [PSCredential-object](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) voor verbinding met doelserver.
* *SelectedDatabase*. AzDataMigrationSelectedDB-object dat de bron- en doeldatabasetoewijzing vertegenwoordigt.
* *Schemavalidatie*. (optioneel, schakelparameter) Na de migratie wordt een vergelijking gemaakt van de schema-informatie tussen bron en doel.
* *DataIntegrityValidatie*. (optioneel, schakelparameter) Na de migratie wordt een op checksum gebaseerde gegevensintegriteitsvalidatie uitgevoerd tussen bron en doel.
* *QueryAnalysisValidatie*. (optioneel, schakelparameter) Na de migratie voert u een snelle en intelligente queryanalyse uit door query's op te halen uit de brondatabase en deze uit te voeren in het doel.

In het volgende voorbeeld wordt een migratietaak met de naam myDMSTask gesneut en gestart:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
```

In het volgende voorbeeld wordt dezelfde migratietaak als hierboven gemaakt en gestart, maar worden ook alle drie de validaties uitgevoerd:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -SchemaValidation `
  -DataIntegrityValidation `
  -QueryAnalysisValidation `
```

## <a name="monitor-the-migration"></a>Bewaak de migratie

U de migratietaak controleren door de eigenschap status van de taak op te vragen zoals weergegeven in het volgende voorbeeld:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>De instantie DMS verwijderen

Nadat de migratie is voltooid, u het Azure DMS-exemplaar verwijderen:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-step"></a>Volgende stap

* Bekijk de migratierichtlijnen in de [Migratiehandleiding voor Microsoft-database](https://datamigration.microsoft.com/).
