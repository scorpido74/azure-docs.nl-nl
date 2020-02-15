---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 02/14/2020
ms.author: mathoma
ms.reviewer: vanto
ms.openlocfilehash: 3e2c8a424c9a3744bfb91d03632965c15613a424
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77252110"
---
In deze stap maakt u een Azure SQL Database afzonderlijke data base. 

> [!IMPORTANT]
> Zorg ervoor dat u firewall regels instelt voor het gebruik van het open bare IP-adres van de computer die u gebruikt om dit artikel te volt ooien.
>
> Zie een [firewall regel op database niveau maken](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) of het IP-adres bepalen dat wordt gebruikt voor de firewall regel op server niveau voor uw computer Zie [een firewall op server niveau maken](../sql-database-server-level-firewall-rule.md)voor meer informatie.  

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Maak uw resource groep en één data base met behulp van de Azure Portal.

1. Selecteer **Azure SQL** in het linkermenu van de [Azure Portal](https://portal.azure.com). Als **Azure SQL** niet voor komt in de lijst, selecteert u **alle services**en typt u vervolgens *Azure SQL* in het zoekvak. Beschrijving Selecteer de ster naast **Azure SQL** om deze te favoriet en voeg deze toe als een item in de linkernavigatiebalk. 
2. Selecteer **+ toevoegen** om de **optie pagina SQL-implementatie selecteren** te openen. U kunt aanvullende informatie over de verschillende data bases weer geven door **Details weer geven** te selecteren op de tegel **data bases** .
3. Selecteer **maken**:

   ![Individuele database maken](../media/sql-database-get-started-portal/create-single-database.png)

4. Typ of selecteer de volgende waarden in het gedeelte **Project Details** van het tabblad **basis beginselen** :

   - **Abonnement**: vervolg keuzelijst en selecteer het juiste abonnement als dit nog niet wordt weer gegeven.
   - **Resource groep**: Selecteer **nieuwe maken**, typ `myResourceGroup`en selecteer **OK**.

     ![Tabblad Nieuw SQL database-basis](../media/sql-database-get-started-portal/new-sql-database-basics.png)

5. Typ of selecteer de volgende waarden in de sectie **database Details** :

   - **Database naam**: Voer `mySampleDatabase`in.
   - **Server**: Selecteer **nieuwe maken**, voer de volgende waarden in en selecteer **selecteren**.
       - **Server naam**: Typ `mysqlserver`; samen met enkele getallen voor uniekheid.
       - **Aanmelding van de server beheerder**: type `azureuser`.
       - **Wacht woord**: Typ een complex wacht woord dat voldoet aan de wachtwoord vereisten.
       - **Locatie**: Kies een locatie in de vervolg keuzelijst, bijvoorbeeld `West US`.

         ![Nieuwe server](../media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > Vergeet niet de aanmeldgegevens en het wachtwoord van de server te noteren zodat u zich bij de server en databases voor deze en andere quickstarts kunt aanmelden. Als u uw aanmeldgegevens of wachtwoord vergeet, kunt u de aanmeldnaam ophalen of het wachtwoord opnieuw instellen op de pagina **SQL Server**. U kunt de pagina **SQL Server** openen door de servernaam te selecteren op de **Overzichtspagina** van de database nadat u de database hebt gemaakt.

   - **Elastische SQL-pool wilt gebruiken**: Selecteer de optie **Nee** .
   - **Compute + Storage**: Selecteer **Data Base configureren**. 

     ![SQL Database Details](../media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - Selecteer **ingericht**.

     ![Ingerichte Gen4](../media/sql-database-get-started-portal/create-database-provisioned.png)

   - Controleer de instellingen voor **vCores**en de **maximale grootte**van de gegevens. Wijzig deze naar wens. 
     - U kunt desgewenst ook **configuratie wijzigen** selecteren om het genereren van de hardware te wijzigen.
   - Selecteer **Toepassen**.

6. Selecteer het tabblad **netwerken** en beslis of u Azure- [**Services en-resources toegang wilt geven tot deze server**](../sql-database-networkaccess-overview.md)of een [persoonlijk eind punt](../../private-link/private-endpoint-overview.md)wilt toevoegen.

   ![Tabblad netwerken](../media/sql-database-get-started-portal/create-database-networking.png)

7. Selecteer het tabblad **extra instellingen** . 
8. Selecteer in de sectie **gegevens bron** onder **bestaande gegevens gebruiken**de optie `Sample`.

   ![Aanvullende SQL data base-instellingen](../media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > Selecteer de gegevens uit **Voorbeeld (AdventureWorksLT)** zodat u deze en andere quickstarts voor Azure SQL Database gemakkelijk kunt volgen waarbij deze gegevens ook worden gebruikt.

9. Laat de rest van de waarden als standaard staan en selecteer onder aan het formulier de optie **controleren + maken** .
10. Controleer de laatste instellingen en selecteer **maken**.

11. Selecteer in het **SQL Database**-formulier de optie **Maken** om de resourcegroep, server en database te implementeren en in te richten.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Maak uw resource groep en één data base met behulp van Power shell.

   ```powershell-interactive
   # Set variables for your server and database
   $subscriptionId = '<SubscriptionID>'
   $resourceGroupName = "myResourceGroup-$(Get-Random)"
   $location = "West US"
   $adminLogin = "azureuser"
   $password = "PWD27!"+(New-Guid).Guid
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName 
   Write-host "Password is" $password  
   Write-host "Server name is" $serverName 

   # Connect to Azure
   Connect-AzAccount

   # Set subscription ID
   Set-AzContext -SubscriptionId $subscriptionId 

   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup

   # Create a server with a system wide unique server name
   Write-host "Creating primary logical server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for primary logical server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule

   # Create General Purpose Gen4 database with 1 vCore
   Write-host "Creating a gen5 2 vCore database..."
   $database = New-AzSqlDatabase  -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -VCore 2 `
      -ComputeGeneration Gen5 `
      -MinimumCapacity 2 `
      -SampleName "AdventureWorksLT"
   $database
   ```

In dit gedeelte van het artikel worden de volgende Power shell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Hiermee maakt u een SQL Database-server die individuele databases en elastische pools host. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Hiermee maakt u een firewall regel voor een logische server. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Hiermee maakt u een nieuwe Azure SQL Database afzonderlijke data base. | 

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Maak uw resource groep en één data base met behulp van AZ CLI.

   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   subscriptionID=<SubscriptionID>
   resourceGroupName=myResourceGroup-$RANDOM
   location=SouthCentralUS
   adminLogin=azureuser
   password="PWD27!"+`openssl rand -base64 18`
   serverName=mysqlserver-$RANDOM
   databaseName=mySampleDatabase
   drLocation=NorthEurope
   drServerName=mysqlsecondary-$RANDOM
   failoverGroupName=failovergrouptutorial-$RANDOM

   # The ip address range that you want to allow to access your DB. 
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB
   startip=0.0.0.0
   endip=0.0.0.0
  
   # Connect to Azure
   az login

   # Set the subscription context for the Azure account
   az account set -s $subscriptionID

   # Create a resource group
   echo "Creating resource group..."
   az group create \
      --name $resourceGroupName \
      --location $location \
      --tags Owner[=SQLDB-Samples]

   # Create a logical server in the resource group
   echo "Creating primary logical server..."
   az sql server create \
      --name $serverName \
      --resource-group $resourceGroupName \
      --location $location  \
      --admin-user $adminLogin \
      --admin-password $password

   # Configure a firewall rule for the server
   echo "Configuring firewall..."
   az sql server firewall-rule create \
      --resource-group $resourceGroupName \
      --server $serverName \
      -n AllowYourIp \
      --start-ip-address $startip \
      --end-ip-address $endip

   # Create a gen5 1vCore database in the server 
   echo "Creating a gen5 2 vCore database..."
   az sql db create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --sample-name AdventureWorksLT \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2
   ```

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [AZ-account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Hiermee stelt u een abonnement in als het huidige actieve abonnement. | 
| [az group create](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Hiermee maakt u een SQL Database-server die individuele databases en elastische pools host. |
| [AZ SQL Server firewall-Rule Create](/cli/azure/sql/server/firewall-rule) | Hiermee maakt u de firewall regels van een server. | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Hiermee maakt u een Data Base. | 


---
