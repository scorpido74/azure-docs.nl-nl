---
title: 'Zelfstudie: Één database toevoegen aan een failovergroep'
description: Voeg een interne database van Azure SQL Database toe aan een failovergroep met behulp van de Azure-portal, PowerShell of Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: c5ce6a1c2f231d372a2a8113eb9043a236090388
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061699"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Zelfstudie: een enkele database van Azure SQL Database toevoegen aan een failovergroep

Een [failovergroep](sql-database-auto-failover-group.md) is een declaratieve abstractielaag waarmee u mulitple geogerepliceerde databases groeperen. Lees of u een failovergroep wilt configureren voor een enkele database van Azure SQL Database en failover testen met de Azure-portal, PowerShell of Azure CLI.  In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> - Maak een enkele database van Azure SQL Database.
> - Maak een failovergroep voor één database tussen twee logische SQL-servers.
> - Test failover.

## <a name="prerequisites"></a>Vereisten

# <a name="portal"></a>[Portal](#tab/azure-portal)
Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien: 

- Een Azure-abonnement. [Maak een gratis account](https://azure.microsoft.com/free/) aan als je nog geen account hebt.


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Als u de zelfstudie wilt voltooien, controleert u of u de volgende items hebt:

- Een Azure-abonnement. [Maak een gratis account](https://azure.microsoft.com/free/) aan als je nog geen account hebt.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
Als u de zelfstudie wilt voltooien, controleert u of u de volgende items hebt:

- Een Azure-abonnement. [Maak een gratis account](https://azure.microsoft.com/free/) aan als je nog geen account hebt.
- De nieuwste versie van [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). 

---

## <a name="1---create-a-single-database"></a>1 - Eén database maken 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 - De failovergroep maken 
In deze stap maakt u een [failovergroep](sql-database-auto-failover-group.md) tussen een bestaande Azure SQL-server en een nieuwe Azure SQL-server in een andere regio. Voeg vervolgens de voorbeelddatabase toe aan de failovergroep. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Maak uw failovergroep en voeg er uw enkele database aan toe met behulp van de Azure-portal. 

1. Selecteer **Azure SQL** in het linkermenu van de [Azure-portal.](https://portal.azure.com) Als **Azure SQL** niet in de lijst staat, selecteert u Alle **services**en typt u Azure SQL in het zoekvak. (Optioneel) Selecteer de ster naast **Azure SQL** om deze favoriet te maken en voeg deze toe als een item in de linkernavigatie. 
1. Selecteer de afzonderlijke database die in `mySampleDatabase`sectie 1 is gemaakt, zoals . 
1. Failovergroepen kunnen op serverniveau worden geconfigureerd. Selecteer de naam van de server onder **Servernaam** om de instellingen voor de server te openen.

   ![Open server voor enkele db](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Selecteer **Failovergroepen** onder het deelvenster **Instellingen** en selecteer **Groep toevoegen** om een nieuwe failovergroep te maken. 

    ![Nieuwe failovergroep toevoegen](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Voer op de pagina **Failovergroep** de volgende waarden in of selecteer deze en selecteer **Vervolgens Maken:**
    - **Naam failovergroep:** typ een unieke failovergroepnaam, zoals `failovergrouptutorial`. 
    - **Secundaire server**: Selecteer de optie om *vereiste instellingen* te configureren en kies vervolgens voor Het maken van een **nieuwe server.** U ook een reeds bestaande server als secundaire server kiezen. Nadat u de volgende waarden hebt ingevoerd, selecteert u **Selecteren**. 
        - **Servernaam:** typ een unieke naam voor de `mysqlsecondary`secundaire server, zoals . 
        - **Serveradmin login**: Type`azureuser`
        - **Wachtwoord:** Typ een complex wachtwoord dat voldoet aan de wachtwoordvereisten.
        - **Locatie**: Kies een locatie in de `East US`vervolgkeuzelijst, zoals . Deze locatie kan niet dezelfde locatie zijn als uw primaire server.

    > [!NOTE]
    > De instellingen voor serveraanmelding en firewall moeten overeenkomen met die van uw primaire server. 
    
      ![Een secundaire server maken voor de failovergroep](media/sql-database-single-database-failover-group-tutorial/create-secondary-failover-server.png)

   - **Databases binnen de groep:** Zodra een secundaire server is geselecteerd, wordt deze optie ontgrendeld. Selecteer deze om **databases te selecteren om toe te voegen** en kies vervolgens de database die u in sectie 1 hebt gemaakt. Als u de database toevoegt aan de failovergroep, wordt het georeplicatieproces automatisch gestart. 
        
    ![SQL DB toevoegen aan failovergroep](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Maak uw failovergroep en voeg er uw enkele database aan toe met PowerShell. 

   > [!NOTE]
   > De instellingen voor serveraanmelding en firewall moeten overeenkomen met die van uw primaire server. 

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule   
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup
   
   # Add the database to the failover group
   Write-host "Adding the database to the failover group..." 
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..." 
   ```

In dit gedeelte van de zelfstudie worden de volgende PowerShell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [Nieuw-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Hiermee maakt u een SQL Database-server die individuele databases en elastische pools host. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Hiermee maakt u een firewallregel voor een logische server. | 
| [Nieuwe AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Hiermee maakt u een nieuwe Azure SQL Database single database. | 
| [Nieuwe AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Hiermee maakt u een nieuwe failovergroep. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Krijgt een of meer SQL-databases. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Hiermee voegt u een of meer Azure SQL-databases toe aan een failovergroep. |

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
Maak uw failovergroep en voeg er uw enkele database aan toe met AZ CLI. 

   > [!NOTE]
   > De instellingen voor serveraanmelding en firewall moeten overeenkomen met die van uw primaire server. 

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $failoverLocation = "West US"
   $failoverServer = "failoverServer-$randomIdentifier"
   $failoverGroup = "failoverGroup-$randomIdentifier"

   echo "Creating a secondary logical server in the DR region..."
   az sql server create --name $failoverServer --resource-group $resourceGroup --location $failoverLocation --admin-user $login --admin-password $password
   
   echo "Creating a failover group between the two servers..."
   az sql failover-group create --name $failoverGroup --partner-server $failoverServer --resource-group $resourceGroup --server $server --add-db $database --failover-policy Automatic
   ```

Dit gedeelte van de zelfstudie maakt gebruik van de volgende Az CLI-cmdlets:

| Opdracht | Opmerkingen |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Hiermee maakt u een SQL Database-server die individuele databases en elastische pools host. |
| [az sql server firewall-rule maken](/cli/azure/sql/server/firewall-rule) | Hiermee maakt u de firewallregels van een server. | 
| [az sql failover-group maken](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Hiermee maakt u een failovergroep. | 

---

## <a name="3---test-failover"></a>3 - Test failover 
In deze stap mislukt u uw failovergroep naar de secundaire server en mislukt u vervolgens terug met de Azure-portal. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Test failover met behulp van de Azure-portal. 

1. Selecteer **Azure SQL** in het linkermenu van de [Azure-portal.](https://portal.azure.com) Als **Azure SQL** niet in de lijst staat, selecteert u Alle **services**en typt u Azure SQL in het zoekvak. (Optioneel) Selecteer de ster naast **Azure SQL** om deze favoriet te maken en voeg deze toe als een item in de linkernavigatie. 
1. Selecteer de afzonderlijke database die in `mySampleDatbase`sectie 2 is gemaakt, zoals . 
1. Selecteer de naam van de server onder **Servernaam** om de instellingen voor de server te openen.

   ![Open server voor enkele db](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Selecteer **Failovergroepen** onder het deelvenster **Instellingen** en kies vervolgens de failovergroep die u in sectie 2 hebt gemaakt. 
  
   ![De failovergroep selecteren in de portal](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Controleer welke server primair is en welke server secundair is. 
1. Selecteer **Failover** in het taakvenster om uw failovergroep te mislukken die uw voorbeelddatabase bevat. 
1. Selecteer **Ja** op de waarschuwing die u op de hoogte stelt dat de verbinding met TDS-sessies wordt verbroken. 

   ![Fail over uw failovergroep met uw SQL-database](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Controleer welke server nu primair is en welke server secundair is. Als het niet lukt, moeten de twee servers rollen hebben verwisseld. 
1. Selecteer **Failover** opnieuw om de servers terug te laten vallen naar hun oorspronkelijke rollen. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Test failover met PowerShell. 


Controleer de rol van de secundaire replica: 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Niet overgaan naar de secundaire server: 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to" $drServerName 
   ```

Failovergroep terugzetten naar de primaire server:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully back to" $serverName
   ```

In dit gedeelte van de zelfstudie worden de volgende PowerShell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hiermee worden Azure SQL Database-failovergroepen op- of weergegeven. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Hiermee wordt een failover van een Failovergroep van Azure SQL Database uitgevoerd. |



# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
Test failover met behulp van de AZ CLI. 

Controleer welke server de secundaire server is:

   
   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

Niet overgaan naar de secundaire server: 

   ```azurecli-interactive
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $failoverServer
   echo "Successfully failed failover group over to" $failoverServer
   ```

Failovergroep terugzetten naar de primaire server:

   ```azurecli-interactive
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   echo "Successfully failed failover group back to" $server
   ```

Dit gedeelte van de zelfstudie maakt gebruik van de volgende Az CLI-cmdlets:

| Opdracht | Opmerkingen |
|---|---|
| [lijst van az sql failover-group](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Hiermee worden de failovergroepen in een server weergegeven. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Stel de primaire groep van de failover groep door niet over alle databases van de huidige primaire server. | 

---

## <a name="clean-up-resources"></a>Resources opschonen 
Beheer resources door de resourcegroep te verwijderen. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Verwijder de brongroep met behulp van de Azure-portal. 

1. Navigeer naar uw brongroep in de [Azure-portal.](https://portal.azure.com)
1. Selecteer **Resourcegroep verwijderen** om alle bronnen in de groep te verwijderen, evenals de resourcegroep zelf. 
1. Typ de naam van `myResourceGroup`de resourcegroep in het tekstvak en selecteer **Verwijderen** om de brongroep te verwijderen.  

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Verwijder de brongroep met PowerShell. 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

In dit gedeelte van de zelfstudie worden de volgende PowerShell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Een resourcegroep verwijderen | 

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Verwijder de brongroep met AZ CLI. 


   ```azurecli-interactive
   echo "Cleaning up resources by removing the resource group..."
   az group delete --name $resourceGroup
   echo "Successfully removed resource group" $resourceGroup
   ```

Dit gedeelte van de zelfstudie maakt gebruik van de volgende Az CLI-cmdlets:

| Opdracht | Opmerkingen |
|---|---|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

---


> [!IMPORTANT]
> Als u de brongroep wilt behouden, maar de secundaire database wilt verwijderen, verwijdert u deze uit de failovergroep voordat u deze verwijdert. Het verwijderen van een secundaire database voordat deze uit de failovergroep wordt verwijderd, kan onvoorspelbaar gedrag veroorzaken. 


## <a name="full-scripts"></a>Volledige scripts

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [Nieuw-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Hiermee maakt u een SQL Database-server die individuele databases en elastische pools host. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Hiermee maakt u een firewallregel voor een logische server. | 
| [Nieuwe AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Hiermee maakt u een nieuwe Azure SQL Database single database. | 
| [Nieuwe AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Hiermee maakt u een nieuwe failovergroep. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Krijgt een of meer SQL-databases. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Hiermee voegt u een of meer Azure SQL-databases toe aan een failovergroep. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hiermee worden Azure SQL Database-failovergroepen op- of weergegeven. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Hiermee wordt een failover van een Failovergroep van Azure SQL Database uitgevoerd. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Een resourcegroep verwijderen | 

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to a failover group")]

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az-account ingesteld](/cli/azure/account?view=azure-cli-latest#az-account-set) | Hiermee stelt u een abonnement in als het huidige actieve abonnement. | 
| [az group create](/cli/azure/group#az-group-create) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Hiermee maakt u een SQL Database-server die individuele databases en elastische pools host. |
| [az sql server firewall-rule maken](/cli/azure/sql/server/firewall-rule) | Hiermee maakt u de firewallregels van een server. | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Hiermee maakt u een database. | 
| [az sql failover-group maken](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Hiermee maakt u een failovergroep. | 
| [lijst van az sql failover-group](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Hiermee worden de failovergroepen in een server weergegeven. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Stel de primaire groep van de failover groep door niet over alle databases van de huidige primaire server. | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

# <a name="portal"></a>[Portal](#tab/azure-portal)
Er zijn geen scripts beschikbaar voor de Azure-portal. 
 
---

U hier andere Azure SQL Database-scripts vinden: [Azure PowerShell](sql-database-powershell-samples.md) en [Azure CLI](sql-database-cli-samples.md). 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een enkele azure-database toegevoegd aan een failovergroep en een mislukte failover getest. U hebt geleerd hoe u: 

> [!div class="checklist"]
> - Maak een enkele database van Azure SQL Database. 
> - Maak een [failovergroep](sql-database-auto-failover-group.md) voor één database tussen twee logische SQL-servers.
> - Test failover.

Ga door naar de volgende zelfstudie over het toevoegen van uw elastische pool aan een failovergroep. 

> [!div class="nextstepaction"]
> [Zelfstudie: Een elastische groep azure SQL-database toevoegen aan een failovergroep](sql-database-elastic-pool-failover-group-tutorial.md)
