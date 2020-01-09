---
title: 'Zelf studie: een elastische pool toevoegen aan een failovergroep'
description: Voeg een Azure SQL Database elastische pool toe aan een failovergroep met behulp van de Azure Portal, Power shell of Azure CLI.
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
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552539"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Zelf studie: een Azure SQL Database elastische pool toevoegen aan een failovergroep

Configureer een failovergroep voor een Azure SQL Database elastische pool en testfailover met behulp van de Azure Portal.  In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> - Maak een Azure SQL Database afzonderlijke data base.
> - Voeg de ene data base toe aan een elastische pool. 
> - Maak een [failovergroep](sql-database-auto-failover-group.md) voor twee elastische Pools tussen twee logische SQL-servers.
> - Testfailover.

## <a name="prerequisites"></a>Vereisten

Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien: 

- Een Azure-abonnement. [Maak een gratis account](https://azure.microsoft.com/free/) als u er nog geen hebt.


## <a name="1---create-a-single-database"></a>1-een enkele data base maken 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2-Eén data base toevoegen aan elastische pool
In deze stap maakt u een elastische pool en voegt u de afzonderlijke data base toe. 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Maak uw elastische pool met behulp van de Azure Portal. 


1. Selecteer **Azure SQL** in het linkermenu van de Azure Portal. Als **Azure SQL** niet voor komt in de lijst, selecteert u **alle services**en typt u vervolgens Azure SQL in het zoekvak. Beschrijving Selecteer de ster naast **Azure SQL** om deze te favoriet en voeg deze toe als een item in de linkernavigatiebalk. 
1. Selecteer **+ toevoegen** om de **optie pagina SQL-implementatie selecteren** te openen. U kunt aanvullende informatie over de verschillende data bases weer geven door details weer geven te selecteren op de tegel data bases.
1. Selecteer **elastische groep** in de vervolg keuzelijst **resource type** in de tegel **SQL-data bases** . Selecteer **maken** om uw elastische pool te maken. 

    ![Elastische pool selecteren](media/sql-database-elastic-pool-failover-group-tutorial/select-azure-sql-elastic-pool.png)

1. Configureer uw elastische pool met de volgende waarden:
   - **Naam**: Geef een unieke naam op voor de elastische pool, zoals `myElasticPool`. 
   - **Abonnement**: Selecteer uw abonnement in de vervolg keuzelijst.
   - **ResourceGroup**: Selecteer `myResourceGroup` in de vervolg keuzelijst, de resource groep die u in sectie 1 hebt gemaakt. 
   - **Server**: Selecteer de server die u hebt gemaakt in sectie 1 in de vervolg keuzelijst.  

       ![Nieuwe server voor elastische pool maken](media/sql-database-elastic-pool-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **Compute + Storage**: Selecteer **elastische pool configureren** om uw reken-, opslag-en toevoegen van uw data base te configureren voor uw elastische pool. Accepteer op het tabblad **pool instellingen** de standaard waarde van GEN5, met 2 vCores en 32 GB. 

1. Selecteer op de pagina **configureren** het tabblad **data bases** en kies vervolgens **Data Base toevoegen**. Kies de data base die u hebt gemaakt in sectie 1 en selecteer vervolgens **Toep assen** om deze toe te voegen aan uw elastische pool. Selecteer opnieuw **Toep assen** om de instellingen voor de elastische groep toe te passen en sluit de pagina **configureren** . 

    ![SQL-data base toevoegen aan elastische pool](media/sql-database-elastic-pool-failover-group-tutorial/add-database-to-elastic-pool.png)

1. Selecteer **controleren + maken** om de instellingen voor de elastische groep te controleren en selecteer vervolgens **maken** om uw elastische pool te maken. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Maak uw elastische Pools en secundaire server met behulp van Power shell. 

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

In dit gedeelte van de zelf studie worden de volgende Power shell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Hiermee maakt u een pool voor elastische Data Base voor een Azure SQL Database.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Hiermee stelt u de eigenschappen voor een data base in of verplaatst u een bestaande Data Base naar een elastische pool. | 

---

## <a name="3---create-the-failover-group"></a>3-de groep failover maken 
In deze stap maakt u een [failovergroep](sql-database-auto-failover-group.md) tussen een bestaande Azure SQL-Server en een nieuwe Azure SQL-Server in een andere regio. Voeg vervolgens de elastische pool toe aan de failovergroep. 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Maak uw failover-groep met behulp van de Azure Portal. 

1. Selecteer **Azure SQL** in het linkermenu van de [Azure Portal](https://portal.azure.com). Als **Azure SQL** niet voor komt in de lijst, selecteert u **alle services**en typt u vervolgens Azure SQL in het zoekvak. Beschrijving Selecteer de ster naast **Azure SQL** om deze te favoriet en voeg deze toe als een item in de linkernavigatiebalk. 
1. Selecteer de elastische pool die u in de vorige sectie hebt gemaakt, zoals `myElasticPool`. 
1. Selecteer in het deel venster **overzicht** de naam van de server onder **Server naam** om de instellingen voor de server te openen.
  
    ![Server openen voor elastische pool](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Selecteer **failover-groepen** in het deel venster **instellingen** en selecteer vervolgens **groep toevoegen** om een nieuwe failovergroep te maken. 

    ![Nieuwe failovergroep toevoegen](media/sql-database-elastic-pool-failover-group-tutorial/elastic-pool-failover-group.png)

1. Voer op de pagina **failover-groep** de volgende waarden in of Selecteer deze en selecteer vervolgens **maken**:
    - **Naam van failovergroep**: Typ een unieke naam voor de failovergroep, zoals `failovergrouptutorial`. 
    - **Secundaire server**: Selecteer de optie voor het configureren van de *vereiste instellingen* en kies vervolgens **een nieuwe server maken**. U kunt ook een al bestaande server kiezen als de secundaire server. Nadat u de volgende waarden voor de nieuwe secundaire server hebt ingevoerd, selecteert u **selecteren**. 
        - **Server naam**: Typ een unieke naam voor de secundaire server, zoals `mysqlsecondary`. 
        - **Aanmelding van de server beheerder**: type `azureuser`
        - **Wacht woord**: Typ een complex wacht woord dat voldoet aan de wachtwoord vereisten.
        - **Locatie**: Kies een locatie in de vervolg keuzelijst, bijvoorbeeld `East US`. Deze locatie mag niet dezelfde locatie zijn als de primaire server.

       > [!NOTE]
       > De aanmeldings-en Firewall instellingen van de server moeten overeenkomen met die van de primaire server. 
    
       ![Een secundaire server maken voor de failovergroep](media/sql-database-elastic-pool-failover-group-tutorial/create-secondary-failover-server.png)

1. Selecteer **data bases in de groep** en selecteer vervolgens de elastische pool die u hebt gemaakt in sectie 2. Er wordt een waarschuwing weer gegeven waarin u wordt gevraagd om een elastische pool te maken op de secundaire server. Selecteer de waarschuwing en selecteer **OK** om de elastische pool te maken op de secundaire server. 
        
    ![Elastische pool toevoegen aan failovergroep](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Selecteer **selecteren** om de instellingen voor de elastische groep toe te passen op de failovergroep en selecteer vervolgens **maken** om uw failovergroep te maken. Door de elastische pool toe te voegen aan de groep failover, wordt het proces voor geo-replicatie automatisch gestart.


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Maak uw failover-groep met behulp van Power shell. 

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

In dit gedeelte van de zelf studie worden de volgende Power shell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Hiermee maakt u een SQL Database-server die individuele databases en elastische pools host. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Hiermee maakt u een firewall regel voor een logische server. | 
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Hiermee maakt u een pool voor elastische Data Base voor een Azure SQL Database.| 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Hiermee maakt u een nieuwe failovergroep. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Hiermee voegt u een of meer Azure SQL-data bases toe aan een failovergroep. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hiermee worden Azure SQL Database failover-groepen opgehaald of weer gegeven. |

---


## <a name="4---test-failover"></a>4-failover testen 
In deze stap wordt uw failover-groep overschreven naar de secundaire server en wordt er een failback uitgevoerd met behulp van de Azure Portal. 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Test de failover van de failovergroep met behulp van de Azure Portal. 

1. Selecteer **Azure SQL** in het linkermenu van de [Azure Portal](https://portal.azure.com). Als **Azure SQL** niet voor komt in de lijst, selecteert u **alle services**en typt u vervolgens Azure SQL in het zoekvak. Beschrijving Selecteer de ster naast **Azure SQL** om deze te favoriet en voeg deze toe als een item in de linkernavigatiebalk. 
1. Selecteer de elastische pool die u in de vorige sectie hebt gemaakt, zoals `myElasticPool`. 
1. Selecteer de naam van de server onder **Server naam** om de instellingen voor de server te openen.

    ![Server openen voor elastische pool](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Selecteer **failover-groepen** in het deel venster **instellingen** en kies vervolgens de failovergroep die u hebt gemaakt in sectie 2. 
  
   ![De groep failover selecteren in de portal](media/sql-database-elastic-pool-failover-group-tutorial/select-failover-group.png)

1. Controleer welke server primair is en welke server secundair is. 
1. Selecteer **failover** in het taak venster voor het uitvoeren van een failover van uw groep met elastische groepen. 
1. Selecteer **Ja** in de waarschuwing waarmee u wordt gewaarschuwd dat TDS-sessies worden losgekoppeld. 

   ![Failover van de failovergroep met uw SQL database](media/sql-database-elastic-pool-failover-group-tutorial/failover-sql-db.png)

1. Controleer welke server primair is, welke server secundair is. Als de failover is geslaagd, moeten de twee servers wisselende rollen hebben. 
1. Selecteer **failover** opnieuw om de failback-groep weer in te stellen op de oorspronkelijke instellingen. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Test de failover van de failovergroep met behulp van Power shell. 

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

Voer een failover uit naar de secundaire server en failback uit met behulp van de Power shell. 

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

In dit gedeelte van de zelf studie worden de volgende Power shell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hiermee worden Azure SQL Database failover-groepen opgehaald of weer gegeven. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Hiermee wordt een failover uitgevoerd van een Azure SQL Database failovergroep. |


---

## <a name="clean-up-resources"></a>Resources opschonen 

Resources opschonen door de resource groep te verwijderen. 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)


1. Navigeer naar uw resource groep in de [Azure Portal](https://portal.azure.com).
1. Selecteer **resource groep verwijderen** om alle resources in de groep en de resource groep zelf te verwijderen. 
1. Typ de naam van de resource groep, `myResourceGroup`, in het tekstvak en selecteer vervolgens **verwijderen** om de resource groep te verwijderen. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Ruim uw resources op met behulp van Power shell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   
   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

In dit gedeelte van de zelf studie wordt gebruikgemaakt van de volgende Power shell-cmdlet:

| Opdracht | Opmerkingen |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resource groep | 

---

> [!IMPORTANT]
> Als u de resource groep wilt blijven gebruiken, maar de secundaire Data Base wilt verwijderen, verwijdert u deze uit de failovergroep voordat u deze verwijdert. Het verwijderen van een secundaire Data Base voordat deze wordt verwijderd uit de failovergroep kan onvoorspelbaar gedrag veroorzaken. 

## <a name="full-script"></a>Volledige script

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Hiermee maakt u een SQL Database-server die individuele databases en elastische pools host. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Hiermee maakt u een firewall regel voor een logische server. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Hiermee maakt u een nieuwe Azure SQL Database afzonderlijke data base. | 
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Hiermee maakt u een pool voor elastische Data Base voor een Azure SQL Database.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Hiermee stelt u de eigenschappen voor een data base in of verplaatst u een bestaande Data Base naar een elastische pool. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Hiermee maakt u een nieuwe failovergroep. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Hiermee worden een of meer SQL-data bases opgehaald. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Hiermee voegt u een of meer Azure SQL-data bases toe aan een failovergroep. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hiermee worden Azure SQL Database failover-groepen opgehaald of weer gegeven. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Hiermee wordt een failover uitgevoerd van een Azure SQL Database failovergroep. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resource groep | 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Er zijn geen scripts beschikbaar voor de Azure Portal.

---

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een Azure SQL Database elastische pool toegevoegd aan een failovergroep en een test failover. U hebt geleerd hoe u:

> [!div class="checklist"]
> - Maak een Azure SQL Database afzonderlijke data base.
> - Voeg de ene data base toe aan een elastische pool. 
> - Maak een [failovergroep](sql-database-auto-failover-group.md) voor twee elastische Pools tussen twee logische SQL-servers.
> - Testfailover.

Ga verder met de volgende zelf studie over het migreren met behulp van DMS.

> [!div class="nextstepaction"]
> [Zelf studie: SQL Server migreren naar een gegroepeerde Data Base met behulp van DMS](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
