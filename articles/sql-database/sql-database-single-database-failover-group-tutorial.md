---
title: 'Zelf studie: een enkele data base aan een failovergroep toevoegen'
description: Een Azure SQL Database afzonderlijke data base aan een failovergroep toevoegen met behulp van de Azure Portal, Power shell of Azure CLI.
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
ms.openlocfilehash: 8c4c346dd004e435846aff5592a20cd747c45df7
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552624"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Zelf studie: een Azure SQL Database afzonderlijke data base aan een failovergroep toevoegen

Configureer een failovergroep voor een Azure SQL Database afzonderlijke data base en testfailover met behulp van de Azure Portal, Power shell of Azure CLI.  In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> - Maak een Azure SQL Database afzonderlijke data base.
> - Maak een [failovergroep](sql-database-auto-failover-group.md) voor één data base tussen twee logische SQL-servers.
> - Testfailover.

## <a name="prerequisites"></a>Vereisten

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien: 

- Een Azure-abonnement. [Maak een gratis account](https://azure.microsoft.com/free/) als u er nog geen hebt.


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Zorg ervoor dat u over de volgende items beschikt om de zelf studie te volt ooien:

- Een Azure-abonnement. [Maak een gratis account](https://azure.microsoft.com/free/) als u er nog geen hebt.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)
Zorg ervoor dat u over de volgende items beschikt om de zelf studie te volt ooien:

- Een Azure-abonnement. [Maak een gratis account](https://azure.microsoft.com/free/) als u er nog geen hebt.
- De nieuwste versie van [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest). 

---

## <a name="1---create-a-single-database"></a>1-een enkele data base maken 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2-de groep failover maken 
In deze stap maakt u een [failovergroep](sql-database-auto-failover-group.md) tussen een bestaande Azure SQL-Server en een nieuwe Azure SQL-Server in een andere regio. Voeg vervolgens de voorbeeld database toe aan de failovergroep. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Maak uw failovergroep en voeg uw afzonderlijke data base toe met behulp van de Azure Portal. 

1. Selecteer **Azure SQL** in het linkermenu van de [Azure Portal](https://portal.azure.com). Als **Azure SQL** niet voor komt in de lijst, selecteert u **alle services**en typt u vervolgens Azure SQL in het zoekvak. Beschrijving Selecteer de ster naast **Azure SQL** om deze te favoriet en voeg deze toe als een item in de linkernavigatiebalk. 
1. Selecteer de afzonderlijke Data Base die is gemaakt in sectie 1, zoals `mySampleDatabase`. 
1. Selecteer de naam van de server onder **Server naam** om de instellingen voor de server te openen.

   ![Server openen voor één data base](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Selecteer **failover-groepen** in het deel venster **instellingen** en selecteer vervolgens **groep toevoegen** om een nieuwe failovergroep te maken. 

    ![Nieuwe failovergroep toevoegen](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Voer op de pagina **failover-groep** de volgende waarden in of Selecteer deze en selecteer vervolgens **maken**:
    - **Naam van failovergroep**: Typ een unieke naam voor de failovergroep, zoals `failovergrouptutorial`. 
    - **Secundaire server**: Selecteer de optie voor het configureren van de *vereiste instellingen* en kies vervolgens **een nieuwe server maken**. U kunt ook een al bestaande server kiezen als de secundaire server. Nadat u de volgende waarden hebt ingevoerd, selecteert u **selecteren**. 
        - **Server naam**: Typ een unieke naam voor de secundaire server, zoals `mysqlsecondary`. 
        - **Aanmelding van de server beheerder**: type `azureuser`
        - **Wacht woord**: Typ een complex wacht woord dat voldoet aan de wachtwoord vereisten.
        - **Locatie**: Kies een locatie in de vervolg keuzelijst, bijvoorbeeld `East US`. Deze locatie mag niet dezelfde locatie zijn als de primaire server.

    > [!NOTE]
    > De aanmeldings-en Firewall instellingen van de server moeten overeenkomen met die van de primaire server. 
    
      ![Een secundaire server maken voor de failovergroep](media/sql-database-single-database-failover-group-tutorial/create-secondary-failover-server.png)

   - **Data bases in de groep**: wanneer een secundaire server is geselecteerd, wordt deze optie ontgrendeld. Selecteer deze optie om **data bases te selecteren die u wilt toevoegen** en kies vervolgens de data base die u hebt gemaakt in sectie 1. Als u de data base aan de groep failover toevoegt, wordt het proces voor geo-replicatie automatisch gestart. 
        
    ![SQL-data base toevoegen aan failovergroep](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Maak uw failovergroep en voeg uw afzonderlijke data base toe met behulp van Power shell. 

   > [!NOTE]
   > De aanmeldings-en Firewall instellingen van de server moeten overeenkomen met die van de primaire server. 

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

In dit gedeelte van de zelf studie worden de volgende Power shell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Hiermee maakt u een SQL Database-server die individuele databases en elastische pools host. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Hiermee maakt u een firewall regel voor een logische server. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Hiermee maakt u een nieuwe Azure SQL Database afzonderlijke data base. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Hiermee maakt u een nieuwe failovergroep. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Hiermee worden een of meer SQL-data bases opgehaald. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Hiermee voegt u een of meer Azure SQL-data bases toe aan een failovergroep. |

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)
Maak uw failovergroep en voeg uw afzonderlijke data base toe met behulp van AZ CLI. 

   > [!NOTE]
   > De aanmeldings-en Firewall instellingen van de server moeten overeenkomen met die van de primaire server. 

   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   # subscriptionID=<SubscriptionID>
   # resourceGroupName=myResourceGroup-$RANDOM
   # location=SouthCentralUS
   # adminLogin=azureuser
   # password="PWD27!"+`openssl rand -base64 18`
   # serverName=mysqlserver-$RANDOM
   # databaseName=mySampleDatabase
   drLocation=NorthEurope
   drServerName=mysqlsecondary-$RANDOM
   failoverGroupName=failovergrouptutorial-$RANDOM

   # Create a secondary server in the failover region
   echo "Creating a secondary logical server in the DR region..."
   az sql server create \
      --name $drServerName \
      --resource-group $resourceGroupName \
      --location $drLocation  \
      --admin-user $adminLogin\
      --admin-password $password

   # Configure a firewall rule for the server
   echo "Configuring firewall..."
   az sql server firewall-rule create \
      --resource-group $resourceGroupName \
      --server $drServerName \
      -n AllowYourIp \
      --start-ip-address $startip \
      --end-ip-address $endip
   
   # Create a failover group between the servers and add the database
   echo "Creating a failover group between the two servers..."
   az sql failover-group create \
      --name $failoverGroupName  \
      --partner-server $drServerName \
      --resource-group $resourceGroupName \
      --server $serverName \
      --add-db $databaseName
      --failover-policy Automatic
   ```

In dit gedeelte van de zelf studie worden de volgende AZ CLI-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Hiermee maakt u een SQL Database-server die individuele databases en elastische pools host. |
| [AZ SQL Server firewall-Rule Create](/cli/azure/sql/server/firewall-rule) | Hiermee maakt u de firewall regels van een server. | 
| [AZ SQL failover-Group Create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Hiermee maakt u een failovergroep. | 

---

## <a name="3---test-failover"></a>3-testfailover 
In deze stap wordt uw failover-groep overschreven naar de secundaire server en wordt er een failback uitgevoerd met behulp van de Azure Portal. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Testfailover met behulp van de Azure Portal. 

1. Selecteer **Azure SQL** in het linkermenu van de [Azure Portal](https://portal.azure.com). Als **Azure SQL** niet voor komt in de lijst, selecteert u **alle services**en typt u vervolgens Azure SQL in het zoekvak. Beschrijving Selecteer de ster naast **Azure SQL** om deze te favoriet en voeg deze toe als een item in de linkernavigatiebalk. 
1. Selecteer de afzonderlijke Data Base die is gemaakt in de sectie 2, zoals `mySampleDatbase`. 
1. Selecteer de naam van de server onder **Server naam** om de instellingen voor de server te openen.

   ![Server openen voor één data base](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Selecteer **failover-groepen** in het deel venster **instellingen** en kies vervolgens de failovergroep die u hebt gemaakt in sectie 2. 
  
   ![De groep failover selecteren in de portal](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Controleer welke server primair is en welke server secundair is. 
1. Selecteer **failover** in het taak venster om een failover uit te laten uw failover-groep met uw voor beeld-afzonderlijke data base. 
1. Selecteer **Ja** in de waarschuwing waarmee u wordt gewaarschuwd dat TDS-sessies worden losgekoppeld. 

   ![Failover van de failovergroep met uw SQL database](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Controleer welke server nu primair is en welke server secundair is. Als failover is geslaagd, moeten de twee servers omgewisselde rollen hebben. 
1. Selecteer **failover** opnieuw om de oorspronkelijke rollen van de servers te herstellen. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Testfailover met behulp van Power shell. 


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

Failover naar de secundaire server: 

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

Herstel de failovergroep terug naar de primaire server:

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

In dit gedeelte van de zelf studie worden de volgende Power shell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hiermee worden Azure SQL Database failover-groepen opgehaald of weer gegeven. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Hiermee wordt een failover uitgevoerd van een Azure SQL Database failovergroep. |



# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)
Testfailover met de AZ CLI. 

Controleer of de server de secundaire is:

   
   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   
   # Verify which server is secondary
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list \
      --server $serverName \
      --resource-group $resourceGroupName
   ```

Failover naar de secundaire server: 

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # drServerName=mysqlsecondary-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM

   
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $drServerName
   echo "Successfully failed failover group over to" $drServerName
   ```

Herstel de failovergroep terug naar de primaire server:

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM
   
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $serverName
   echo "Successfully failed failover group back to" $serverName
   ```

In dit gedeelte van de zelf studie worden de volgende AZ CLI-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [AZ SQL failover-Group List](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Geeft een lijst van de failover-groepen op een server. |
| [AZ SQL failover-groeps Set-Primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Stel de primaire groep van de failovergroep in door het uitvoeren van een failover voor alle data bases van de huidige primaire server. | 

---

## <a name="clean-up-resources"></a>Resources opschonen 
Resources opschonen door de resource groep te verwijderen. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Verwijder de resource groep met behulp van de Azure Portal. 

1. Navigeer naar uw resource groep in de [Azure Portal](https://portal.azure.com).
1. Selecteer **resource groep verwijderen** om alle resources in de groep en de resource groep zelf te verwijderen. 
1. Typ de naam van de resource groep, `myResourceGroup`, in het tekstvak en selecteer vervolgens **verwijderen** om de resource groep te verwijderen.  

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Verwijder de resource groep met behulp van Power shell. 


   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

In dit gedeelte van de zelf studie worden de volgende Power shell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resource groep | 

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

Verwijder de resource groep met behulp van AZ CLI. 


   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   
   # Clean up resources by removing the resource group
   echo "Cleaning up resources by removing the resource group..."
   az group delete \
     --name $resourceGroupName
   echo "Successfully removed resource group" $resourceGroupName
   ```

In dit gedeelte van de zelf studie worden de volgende AZ CLI-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

---


> [!IMPORTANT]
> Als u de resource groep wilt blijven gebruiken, maar de secundaire Data Base wilt verwijderen, verwijdert u deze uit de failovergroep voordat u deze verwijdert. Het verwijderen van een secundaire Data Base voordat deze wordt verwijderd uit de failovergroep kan onvoorspelbaar gedrag veroorzaken. 


## <a name="full-scripts"></a>Volledige scripts

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Hiermee maakt u een SQL Database-server die individuele databases en elastische pools host. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Hiermee maakt u een firewall regel voor een logische server. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Hiermee maakt u een nieuwe Azure SQL Database afzonderlijke data base. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Hiermee maakt u een nieuwe failovergroep. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Hiermee worden een of meer SQL-data bases opgehaald. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Hiermee voegt u een of meer Azure SQL-data bases toe aan een failovergroep. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hiermee worden Azure SQL Database failover-groepen opgehaald of weer gegeven. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Hiermee wordt een failover uitgevoerd van een Azure SQL Database failovergroep. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resource groep | 

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to a failover group")]

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [AZ-account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Hiermee stelt u een abonnement in als het huidige actieve abonnement. | 
| [az group create](/cli/azure/group#az-group-create) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Hiermee maakt u een SQL Database-server die individuele databases en elastische pools host. |
| [AZ SQL Server firewall-Rule Create](/cli/azure/sql/server/firewall-rule) | Hiermee maakt u de firewall regels van een server. | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Hiermee maakt u een Data Base. | 
| [AZ SQL failover-Group Create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Hiermee maakt u een failovergroep. | 
| [AZ SQL failover-Group List](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Geeft een lijst van de failover-groepen op een server. |
| [AZ SQL failover-groeps Set-Primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Stel de primaire groep van de failovergroep in door het uitvoeren van een failover voor alle data bases van de huidige primaire server. | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Er zijn geen scripts beschikbaar voor de Azure Portal. 
 
---

U vindt hier ook andere Azure SQL Database scripts: [Azure PowerShell](sql-database-powershell-samples.md) en [Azure cli](sql-database-cli-samples.md). 

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een Azure SQL Database afzonderlijke data base aan een failovergroep toegevoegd en is de failover getest. U hebt geleerd hoe u: 

> [!div class="checklist"]
> - Maak een Azure SQL Database afzonderlijke data base. 
> - Maak een [failovergroep](sql-database-auto-failover-group.md) voor één data base tussen twee logische SQL-servers.
> - Testfailover.

Ga verder met de volgende zelf studie over het toevoegen van een elastische pool aan een failovergroep. 

> [!div class="nextstepaction"]
> [Zelf studie: een Azure SQL Database elastische pool toevoegen aan een failovergroep](sql-database-elastic-pool-failover-group-tutorial.md)
