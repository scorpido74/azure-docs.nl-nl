---
title: 'Zelfstudie: Een elastische groep toevoegen aan een failovergroep'
description: Voeg een elastische groep azure SQL Database toe aan een failovergroep met behulp van de Azure-portal, PowerShell of Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/27/2019
ms.openlocfilehash: c57f9eed2147504dd7b3313d58468fb76ab40caa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268975"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Zelfstudie: Een elastische groep azure SQL-database toevoegen aan een failovergroep

Configureer een failovergroep voor een elastische groep van Azure SQL Database en test failover met behulp van de Azure-portal.  In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> - Maak een enkele database van Azure SQL Database.
> - Voeg de enkele database toe aan een elastische groep. 
> - Maak een [failovergroep](sql-database-auto-failover-group.md) voor twee elastische pools tussen twee logische SQL-servers.
> - Test failover.

## <a name="prerequisites"></a>Vereisten

Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien: 

- Een Azure-abonnement. [Maak een gratis account](https://azure.microsoft.com/free/) aan als je nog geen account hebt.


## <a name="1---create-a-single-database"></a>1 - Eén database maken 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2 - Eén database toevoegen aan elastische groep
In deze stap maakt u een elastische groep en voegt u er uw enkele database aan toe. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Maak uw elastische pool met behulp van de Azure-portal. 


1. Selecteer **Azure SQL** in het linkermenu van de Azure-portal. Als **Azure SQL** niet in de lijst staat, selecteert u Alle **services**en typt u Azure SQL in het zoekvak. (Optioneel) Selecteer de ster naast **Azure SQL** om deze favoriet te maken en voeg deze toe als een item in de linkernavigatie. 
1. Selecteer **+ Toevoegen** om de **optiepagina SQL-implementatie selecteren** te openen. U aanvullende informatie over de verschillende databases weergeven door Details weergeven op de tegel Databases te selecteren.
1. Selecteer **De vervolgkeuzelijst** **Resourcetype** in de tegel **SQL-databases.** Selecteer **Maken** om uw elastische groep te maken. 

    ![Elastische groep selecteren](media/sql-database-elastic-pool-failover-group-tutorial/select-azure-sql-elastic-pool.png)

1. Configureer uw elastische pool met de volgende waarden:
   - **Naam:** Geef een unieke naam op `myElasticPool`voor uw elastische zwembad, zoals . 
   - **Abonnement**: Selecteer uw abonnement in de vervolgkeuzelijst.
   - **Resourcegroep:** `myResourceGroup` Selecteer uit de vervolgkeuzelijst de resourcegroep die u in sectie 1 hebt gemaakt. 
   - **Server:** Selecteer de server die u in sectie 1 hebt gemaakt in de vervolgkeuzelijst.  

       ![Nieuwe server maken voor elastische pool](media/sql-database-elastic-pool-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **Compute + storage:** selecteer **Elastische pool configureren** om uw compute, storage en voeg uw enkele database toe aan uw elastische groep. Laat op het tabblad **Poolinstellingen** de standaardstandaard van Gen5 achter, met 2 vCores en 32gb. 

1. Selecteer op de pagina **Configureren** het tabblad **Databases** en kies vervolgens de **optie Database toevoegen**. Kies de database die u in sectie 1 hebt gemaakt en selecteer **Toepassen** om deze toe te voegen aan uw elastische groep. Selecteer Opnieuw **toepassen** om de instellingen van uw elastische groep toe te passen en sluit de pagina **Configureren.** 

    ![SQL DB toevoegen aan elastische pool](media/sql-database-elastic-pool-failover-group-tutorial/add-database-to-elastic-pool.png)

1. Selecteer **Controleren + maken** om de instellingen van uw elastische groep te controleren en selecteer Vervolgens **Maken** om uw elastische groep te maken. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Maak uw elastische pools en secundaire server met PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   $poolName = "myElasticPool"
   $databaseName = "mySampleDatabase"
   $drLocation = "West US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # The ip address range that you want to allow to access your server 
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections
   # $startIp = "0.0.0.0"
   # $endIp = "0.0.0.0"
   
   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create primary Gen5 elastic 2 vCore pool
   Write-host "Creating elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
   # Add single db into elastic pool
   Write-host "Creating elastic pool..."
   $addDatabase = Set-AzSqlDatabase -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -DatabaseName $databaseName `
       -ElasticPoolName $poolName
   $addDatabase
   ```

In dit gedeelte van de zelfstudie worden de volgende PowerShell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [Nieuw-AzsqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Hiermee maakt u een elastische databasegroep voor een Azure SQL-database.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Hiermee stelt u eigenschappen in voor een database of verplaatst u een bestaande database naar een elastische groep. | 

---

## <a name="3---create-the-failover-group"></a>3 - De failovergroep maken 
In deze stap maakt u een [failovergroep](sql-database-auto-failover-group.md) tussen een bestaande Azure SQL-server en een nieuwe Azure SQL-server in een andere regio. Voeg vervolgens de elastische pool toe aan de failovergroep. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Maak uw failovergroep met behulp van de Azure-portal. 

1. Selecteer **Azure SQL** in het linkermenu van de [Azure-portal.](https://portal.azure.com) Als **Azure SQL** niet in de lijst staat, selecteert u Alle **services**en typt u Azure SQL in het zoekvak. (Optioneel) Selecteer de ster naast **Azure SQL** om deze favoriet te maken en voeg deze toe als een item in de linkernavigatie. 
1. Selecteer de elastische groep die in `myElasticPool`de vorige sectie is gemaakt, zoals . 
1. Selecteer in het deelvenster **Overzicht** de naam van de server onder **Servernaam** om de instellingen voor de server te openen.
  
    ![Open server voor elastische pool](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Selecteer **Failovergroepen** onder het deelvenster **Instellingen** en selecteer **Groep toevoegen** om een nieuwe failovergroep te maken. 

    ![Nieuwe failovergroep toevoegen](media/sql-database-elastic-pool-failover-group-tutorial/elastic-pool-failover-group.png)

1. Voer op de pagina **Failovergroep** de volgende waarden in of selecteer deze en selecteer **Vervolgens Maken:**
    - **Naam failovergroep:** typ een unieke failovergroepnaam, zoals `failovergrouptutorial`. 
    - **Secundaire server**: Selecteer de optie om *vereiste instellingen* te configureren en kies vervolgens voor Het maken van een **nieuwe server.** U ook een reeds bestaande server als secundaire server kiezen. Nadat u de volgende waarden voor uw nieuwe secundaire server hebt ingevoerd, selecteert u **Selecteren**. 
        - **Servernaam:** typ een unieke naam voor de `mysqlsecondary`secundaire server, zoals . 
        - **Serveradmin login**: Type`azureuser`
        - **Wachtwoord:** Typ een complex wachtwoord dat voldoet aan de wachtwoordvereisten.
        - **Locatie**: Kies een locatie in de `East US`vervolgkeuzelijst, zoals . Deze locatie kan niet dezelfde locatie zijn als uw primaire server.

       > [!NOTE]
       > De instellingen voor serveraanmelding en firewall moeten overeenkomen met die van uw primaire server. 
    
       ![Een secundaire server maken voor de failovergroep](media/sql-database-elastic-pool-failover-group-tutorial/create-secondary-failover-server.png)

1. Selecteer **Databases binnen de groep** en selecteer vervolgens de elastische groep die u in sectie 2 hebt gemaakt. Er moet een waarschuwing verschijnen, waarin u wordt gevraagd een elastische groep op de secundaire server te maken. Selecteer de waarschuwing en selecteer **OK** om de elastische groep op de secundaire server te maken. 
        
    ![Elastische pool toevoegen aan failovergroep](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Selecteer **Selecteren** om de instellingen van de elastische groep voor een groep te toepassen op de failovergroep en selecteer **Vervolgens Maken** om de failovergroep te maken. Als u de elastische groep toevoegt aan de failovergroep, wordt het georeplicatieproces automatisch gestart.


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Maak uw failovergroep met PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   Write-host "Secondary logical server =" $drServerName
   
   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
   New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   Write-host "Firewall configured" 
   
   # Create secondary Gen5 elastic 2 vCore pool
   Write-host "Creating secondary elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $drServerName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
   # Create a failover group between the servers
   Write-host "Creating failover group..." 
   New-AzSqlDatabaseFailoverGroup `
     –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully." 
   
   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...." 
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
                  -ResourceGroupName $resourceGroupName `
                  -ServerName $serverName `
                  -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..." 
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases 
   $failoverGroup
   ```

In dit gedeelte van de zelfstudie worden de volgende PowerShell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [Nieuw-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Hiermee maakt u een SQL Database-server die individuele databases en elastische pools host. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Hiermee maakt u een firewallregel voor een logische server. | 
| [Nieuw-AzsqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Hiermee maakt u een elastische databasegroep voor een Azure SQL-database.| 
| [Nieuwe AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Hiermee maakt u een nieuwe failovergroep. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Hiermee voegt u een of meer Azure SQL-databases toe aan een failovergroep. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hiermee worden Azure SQL Database-failovergroepen op- of weergegeven. |

---


## <a name="4---test-failover"></a>4 - Test failover 
In deze stap mislukt u uw failovergroep naar de secundaire server en mislukt u vervolgens terug met de Azure-portal. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Test failover van uw failovergroep met behulp van de Azure-portal. 

1. Selecteer **Azure SQL** in het linkermenu van de [Azure-portal.](https://portal.azure.com) Als **Azure SQL** niet in de lijst staat, selecteert u Alle **services**en typt u Azure SQL in het zoekvak. (Optioneel) Selecteer de ster naast **Azure SQL** om deze favoriet te maken en voeg deze toe als een item in de linkernavigatie. 
1. Selecteer de elastische groep die in `myElasticPool`de vorige sectie is gemaakt, zoals . 
1. Selecteer de naam van de server onder **Servernaam** om de instellingen voor de server te openen.

    ![Open server voor elastische pool](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Selecteer **Failovergroepen** onder het deelvenster **Instellingen** en kies vervolgens de failovergroep die u in sectie 2 hebt gemaakt. 
  
   ![De failovergroep selecteren in de portal](media/sql-database-elastic-pool-failover-group-tutorial/select-failover-group.png)

1. Controleer welke server primair is en welke server secundair is. 
1. Selecteer **Failover** in het taakvenster om te mislukken over uw failovergroep met uw elastische groep. 
1. Selecteer **Ja** op de waarschuwing die u op de hoogte stelt dat de verbinding met TDS-sessies wordt verbroken. 

   ![Fail over uw failovergroep met uw SQL-database](media/sql-database-elastic-pool-failover-group-tutorial/failover-sql-db.png)

1. Controleer welke server primair is, welke server secundair is. Als failover is geslaagd, moeten de twee servers rollen hebben verwisseld. 
1. Selecteer **Opnieuw Failover** om de failovergroep terug te laten gaan naar de oorspronkelijke instellingen. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Test failover van uw failovergroep met PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary server is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $drServerName 
   ```

Fail your failover group over to the secondary server, and then fail back using the PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is now primary" 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $serverName 
   ```

In dit gedeelte van de zelfstudie worden de volgende PowerShell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hiermee worden Azure SQL Database-failovergroepen op- of weergegeven. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Hiermee wordt een failover van een Failovergroep van Azure SQL Database uitgevoerd. |


---

## <a name="clean-up-resources"></a>Resources opschonen 

Beheer resources door de resourcegroep te verwijderen. 


# <a name="portal"></a>[Portal](#tab/azure-portal)


1. Navigeer naar uw brongroep in de [Azure-portal.](https://portal.azure.com)
1. Selecteer **Resourcegroep verwijderen** om alle bronnen in de groep te verwijderen, evenals de resourcegroep zelf. 
1. Typ de naam van `myResourceGroup`de resourcegroep in het tekstvak en selecteer **Verwijderen** om de brongroep te verwijderen. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Ruim uw resources op met PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   
   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

In dit gedeelte van de zelfstudie wordt de volgende PowerShell-cmdlet gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Een resourcegroep verwijderen | 

---

> [!IMPORTANT]
> Als u de brongroep wilt behouden, maar de secundaire database wilt verwijderen, verwijdert u deze uit de failovergroep voordat u deze verwijdert. Het verwijderen van een secundaire database voordat deze uit de failovergroep wordt verwijderd, kan onvoorspelbaar gedrag veroorzaken. 

## <a name="full-script"></a>Volledige script

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [Nieuw-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Hiermee maakt u een SQL Database-server die individuele databases en elastische pools host. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Hiermee maakt u een firewallregel voor een logische server. | 
| [Nieuwe AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Hiermee maakt u een nieuwe Azure SQL Database single database. | 
| [Nieuw-AzsqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Hiermee maakt u een elastische databasegroep voor een Azure SQL-database.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Hiermee stelt u eigenschappen in voor een database of verplaatst u een bestaande database naar een elastische groep. | 
| [Nieuwe AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Hiermee maakt u een nieuwe failovergroep. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Krijgt een of meer SQL-databases. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Hiermee voegt u een of meer Azure SQL-databases toe aan een failovergroep. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hiermee worden Azure SQL Database-failovergroepen op- of weergegeven. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Hiermee wordt een failover van een Failovergroep van Azure SQL Database uitgevoerd. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Een resourcegroep verwijderen | 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Er zijn geen scripts beschikbaar voor de Azure-portal.

---

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een elastische groep van Azure SQL Database toegevoegd aan een failovergroep en een mislukte fout getest. U hebt geleerd hoe u:

> [!div class="checklist"]
> - Maak een enkele database van Azure SQL Database.
> - Voeg de enkele database toe aan een elastische groep. 
> - Maak een [failovergroep](sql-database-auto-failover-group.md) voor twee elastische pools tussen twee logische SQL-servers.
> - Test failover.

Ga door naar de volgende zelfstudie over het migreren met Behulp van DMS.

> [!div class="nextstepaction"]
> [Zelfstudie: SQL Server migreren naar een gepoolde database met DMS](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
