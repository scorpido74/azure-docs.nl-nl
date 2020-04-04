---
title: Een Synapse SQL-groep maken en opvragen met Azure PowerShell
description: Maak snel een Synapse SQL-poollogische server met een firewallregel op serverniveau met Azure PowerShell.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 57564e9dffd6022e1e4fe464b4b26a5bb8eb318b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631328"
---
# <a name="quickstart-create-and-query-a-synapse-sql-pool-with-azure-powershell"></a>Snelstart: een Synapse SQL-groep maken en opvragen met Azure PowerShell

Maak een Synapse SQL-pool (datawarehouse) in Azure Synapse Analytics met Azure PowerShell.

## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

> [!IMPORTANT]
> Het maken van een SQL-pool kan resulteren in een nieuwe factureerbare service.  Zie [Azure Synapse Analytics-prijzen voor](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)meer informatie.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij uw Azure-abonnement met de opdracht [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) en volg de aanwijzingen op het scherm.

```powershell
Connect-AzAccount
```

Voer [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)uit om te zien welk abonnement u gebruikt.

```powershell
Get-AzSubscription
```

Als u een ander abonnement moet gebruiken dan de standaardinstelling, voert u [Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)uit.

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="create-variables"></a>Variabelen maken

In deze snelstart leert u om variabelen te definiëren voor gebruik in de scripts.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehouse"
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [Azure-brongroep](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) met de opdracht [Nieuw-AzResourceGroep.](/powershell/module/az.resources/new-azresourcegroup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam `myResourceGroup` gemaakt op de locatie `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```

## <a name="create-a-logical-server"></a>Een logische server maken

Maak een [Azure SQL-logische server](../../sql-database/sql-database-logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) met de opdracht [Nieuw-AzSqlServer.](/powershell/module/az.sql/new-azsqlserver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Een logische server bevat een groep met databases die worden beheerd als groep. In het volgende voorbeeld wordt een willekeurig benoemde server `ServerAdmin` in uw `ChangeYourAdminPassword1`brongroep met een benoemde beheerdersgebruiker en een wachtwoord van . U kunt deze vooraf gedefinieerde waarden vervangen.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Een serverfirewallregel configureren

Maak een [firewallregel op Azure SQL-serverniveau](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) met de opdracht [Nieuw-AzSqlServerFirewallRule.](/powershell/module/az.sql/new-azsqlserverfirewallrule?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Met een firewallregel op serverniveau kan een externe toepassing, zoals SQL Server Management Studio of het SQLCMD-hulpprogramma, verbinding maken met een SQL-groep via de SQL-poolservicefirewall.

In het volgende voorbeeld wordt de firewall alleen geopend voor andere Azure-resources. Voor externe connectiviteit wijzigt u het IP-adres in een correct adres voor uw omgeving. Als u alle IP-adressen wilt openen, gebruikt u 0.0.0.0 als beginadres en 255.255.255.255 als eindadres.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL-eindpunten communiceren via poort 1433. Als u verbinding probeert te maken vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 1433 mogelijk niet toegestaan door de firewall van uw netwerk. Als dat het zo is, u geen verbinding maken met uw Azure SQL-server, tenzij uw IT-afdeling poort 1433 opent.
>

## <a name="create-a-sql-pool"></a>Een SQL-groep maken

In het volgende voorbeeld wordt een SQL-groep gemaakt met behulp van de eerder gedefinieerde variabelen.  Hiermee wordt de servicedoelstelling als DW100c opgegeven, wat een lager uitgangspunt is voor uw SQL-groep.

```Powershell
New-AzSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW100c" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

De vereiste parameters zijn:

* **RequestedServiceObjectiveName:** de hoeveelheid [datawarehouse-eenheden die](what-is-a-data-warehouse-unit-dwu-cdwu.md) u aanvraagt. Het verhogen van dit bedrag verhoogt de rekenkosten. Zie [geheugen- en gelijktijdigheidslimieten](memory-concurrency-limits.md)voor een lijst met ondersteunde waarden .
* **DatabaseName:** de naam van de SQL-groep die u maakt.
* **ServerNaam:** de naam van de server die u gebruikt voor het maken.
* **ResourceGroupName:** resourcegroep die u gebruikt. Gebruik Get-AzureResource om beschikbare resourcegroepen in uw abonnement te zoeken.
* **Editie**: Moet "DataWarehouse" zijn om een SQL-groep te maken.

De optionele parameters zijn:

* **CollationName**: de standaardsortering, indien niet opgegeven, is COLLATE SQL_Latin1_General_CP1_CI_AS. Collatie kan niet worden gewijzigd in een database.
* **MaxSizeBytes:** de standaard maximale grootte van een database is 240 TB. De maximale grootte beperkt de gegevens van rowstore. Er is onbeperkte opslag voor kolomgegevens.

Zie [Nieuw-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)voor meer informatie over de parameteropties.

## <a name="clean-up-resources"></a>Resources opschonen

Andere zelfstudies in deze verzameling zijn gebaseerd op deze snelstart.

> [!TIP]
> Als u van plan bent om verder te werken met latere quickstart-zelfstudies, ruimt u de bronnen die in deze quickstart zijn gemaakt, niet op. Als u niet van plan bent door te gaan, gebruikt u de volgende stappen om alle resources te verwijderen die door deze quickstart in de Azure-portal zijn gemaakt.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Volgende stappen

U hebt nu een SQL-groep gemaakt, een firewallregel gemaakt, verbonden met uw SQL-groep en een paar query's uitgevoerd. Ga voor meer informatie verder naar het artikel [Gegevens laden in SQL-groep.](load-data-from-azure-blob-storage-using-polybase.md)
