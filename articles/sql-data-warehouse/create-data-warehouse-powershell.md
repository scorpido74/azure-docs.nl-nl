---
title: 'Snelstartgids: een magazijn maken-Azure Power shell'
description: Maak snel een SQL Database logische server, een firewall regel op server niveau en een Data Warehouse met Azure PowerShell.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: development
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 94dcc0dee5dd4fe81eb5ce067d7ace31edeca353
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461524"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>Snelstartgids: een Azure SQL Data Warehouse met Azure PowerShell maken en doorzoeken

Maak snel een Azure SQL Data Warehouse met behulp van Azure PowerShell.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

> [!NOTE]
> Het maken van een SQL Data Warehouse kan leiden tot een nieuwe factureerbare service.  Zie [Prijzen van SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) voor meer informatie.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij uw Azure-abonnement met de opdracht [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) en volg de instructies op het scherm.

```powershell
Connect-AzAccount
```

Als u wilt zien welk abonnement u gebruikt, voert u [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription)uit.

```powershell
Get-AzSubscription
```

Als u een ander abonnement wilt gebruiken dan de standaard instelling, voert u [set-AzContext](/powershell/module/az.accounts/set-azcontext).

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
$databasename = "mySampleDataWarehosue"
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [Azure-resource groep](../azure-resource-manager/management/overview.md) met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam `myResourceGroup` gemaakt op de locatie `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Een logische server maken

Maak een [logische Azure SQL-Server](../sql-database/sql-database-logical-servers.md) met de opdracht [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) . Een logische server bevat een groep met databases die worden beheerd als groep. In het volgende voor beeld wordt een wille keurig benoemde server in de resource groep gemaakt met een beheerder met de naam `ServerAdmin` en een wacht woord van `ChangeYourAdminPassword1`. U kunt deze vooraf gedefinieerde waarden vervangen.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Een serverfirewallregel configureren

Maak een [firewall regel op Azure SQL server-niveau](../sql-database/sql-database-firewall-configure.md) met de opdracht [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) . Met een firewall regel op server niveau kan een externe toepassing, zoals SQL Server Management Studio of het hulp programma SQLCMD, verbinding maken met een SQL Data Warehouse via de firewall van de SQL Data Warehouse-service. In het volgende voorbeeld wordt de firewall alleen geopend voor andere Azure-resources. Voor externe connectiviteit wijzigt u het IP-adres in een correct adres voor uw omgeving. Als u alle IP-adressen wilt openen, gebruikt u 0.0.0.0 als beginadres en 255.255.255.255 als eindadres.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL Database en SQL Data Warehouse communiceren via poort 1433. Als u verbinding probeert te maken vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 1433 mogelijk niet toegestaan door de firewall van uw netwerk. Als dat het geval is, kunt u geen verbinding maken met uw Azure SQL-Server tenzij uw IT-afdeling poort 1433 opent.
>


## <a name="create-a-data-warehouse"></a>Een datawarehouse maken
In dit voor beeld wordt een Data Warehouse gemaakt met de eerder gedefinieerde variabelen.  Hiermee wordt de service doelstelling als DW100c opgegeven. Dit is een goedkope start punt voor uw data warehouse. 

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

* **RequestedServiceObjectiveName**: de hoeveelheid [Data Warehouse-eenheden](what-is-a-data-warehouse-unit-dwu-cdwu.md) die u aanvraagt. Het verhogen van deze waarde verhoogt de reken kosten. Zie [geheugen-en gelijktijdigheids limieten](memory-concurrency-limits.md)voor een lijst met ondersteunde waarden.
* **DATABASENAME**: de naam van de SQL Data Warehouse die u maakt.
* **Servername**: de naam van de server die u gebruikt voor het maken van.
* **ResourceGroupName**: de resource groep die u gebruikt. Gebruik Get-AzureResource om beschikbare resourcegroepen in uw abonnement te zoeken.
* **Edition**: moet DataWarehouse zijn, als u een SQL Data Warehouse wilt maken.

De optionele parameters zijn:

- **CollationName**: de standaardsortering, indien niet opgegeven, is COLLATE SQL_Latin1_General_CP1_CI_AS. Sortering kan niet worden gewijzigd voor een Data Base.
- **MaxSizeBytes**: de maximale grootte van een Data Base is 240TB. De maximale grootte beperkt de rowstore-gegevens. Er is een onbeperkte opslag voor kolom gegevens.

Zie [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)voor meer informatie over de parameter opties.


## <a name="clean-up-resources"></a>Resources opschonen

Andere zelfstudies in deze verzameling zijn gebaseerd op deze snelstart. 

> [!TIP]
> Als u van plan bent om door te gaan met de latere zelf studies, kunt u de resources die u in deze Quick Start hebt gemaakt, niet opschonen. Als u niet wilt door gaan, gebruikt u de volgende stappen om alle resources te verwijderen die door deze Quick Start in de Azure Portal zijn gemaakt.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Volgende stappen

U hebt nu een Data Warehouse gemaakt, een firewall regel gemaakt die is verbonden met uw data warehouse en enkele query's uitgevoerd. Voor meer informatie over Azure SQL Data Warehouse gaat u verder met de zelfstudie voor het laden van gegevens.
> [!div class="nextstepaction"]
>[Gegevens in een SQL Data Warehouse laden](load-data-from-azure-blob-storage-using-polybase.md)
