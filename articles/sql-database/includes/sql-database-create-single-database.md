---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 03/10/2020
ms.author: mathoma
ms.reviewer: vanto
ms.openlocfilehash: c1ca87b6e7b8afb50522e73107707e15782a0a91
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79200158"
---
In deze stap maakt u een Azure SQL Database-Server en één data base die gebruikmaakt van AdventureWorksLT-voorbeeld gegevens. U kunt de data base maken met behulp van Azure Portal menu's en schermen, of met behulp van een Azure CLI-of Power shell-script in de Azure Cloud Shell.

Alle methoden omvatten het instellen van een firewall regel op server niveau om het open bare IP-adres van de computer die u gebruikt voor toegang tot de server toe te staan. Zie [een firewall op server niveau maken](../sql-database-server-level-firewall-rule.md)voor meer informatie over het maken van firewall regels voor servers. U kunt ook firewall regels op database niveau instellen. Zie [een firewall regel op database niveau maken](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database). 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Voor het maken van een resource groep, een SQL-Server en één data base in de Azure Portal:

1. Meld u aan bij de [portal](https://portal.azure.com).
1. Zoek in de zoek balk naar en selecteer **Azure SQL**.
1. Selecteer op de pagina **Azure SQL** de optie **toevoegen**. 
   
   ![Toevoegen aan Azure SQL](../media/sql-database-single-database-get-started/sqldbportal.png)
   
1. Selecteer op de pagina **SQL-implementatie optie selecteren** de **tegel SQL-data bases** , met **één data base** onder het **resource type**. U kunt meer informatie over de verschillende data bases bekijken door **Details weer geven**te selecteren.
1. Selecteer **Maken**.
   
   ![Individuele database maken](../media/sql-database-single-database-get-started/create-single-database.png)
   
1. Selecteer op het tabblad **basis beginselen** van het formulier **Create SQL database** onder **Project Details**het juiste Azure- **abonnement** als dit nog niet is geselecteerd.
1. Onder **resource groep**selecteert u **nieuwe maken**, voert u *MyResourceGroup*in en selecteert u **OK**.
1. Voer onder **Details van data**Base bij **database naam** *mySampleDatabase*in.
1. Voor **Server**selecteert u **nieuwe maken**en vult u het **nieuwe server** formulier als volgt in:
   - **Server naam**: Voer *MySqlServer*in en een aantal tekens voor uniekheid.
   - **Aanmelding van de server beheerder**: Voer *azureuser*in.
   - **Wacht woord**: Voer een wacht woord in dat voldoet aan de vereisten en voer het opnieuw in het veld **wacht woord bevestigen** .
   - **Locatie**: vervolg keuzelijst en kies een locatie, zoals **(VS) vs-Oost**.
   
   Selecteer **OK**.
   
   ![Nieuwe server](../media/sql-database-single-database-get-started/new-server.png)
   
   Registreer de aanmeldings naam en het wacht woord van de server beheerder zodat u zich kunt aanmelden bij de server en data bases. Als u uw aanmelding of wacht woord bent verg eten, kunt u de aanmeldings naam ophalen of het wacht woord opnieuw instellen op de **SQL Server** -pagina nadat de data base is gemaakt. Als u de **SQL Server** -pagina wilt openen, selecteert u de server naam op de pagina **overzicht** van data base.
   
1. Als u onder **Compute + Storage**de standaard instellingen opnieuw wilt configureren, selecteert u **Data Base configureren**.
   
   Op de pagina **configureren** kunt u desgewenst het volgende doen:
   - Wijzig de **Compute-laag** van **ingericht** naar **serverloos**.
   - Controleer en wijzig de instellingen voor **vCores** en **Data maximum grootte**.
   - Selecteer **configuratie wijzigen** om het genereren van hardware te wijzigen.
   
   Nadat u wijzigingen hebt aangebracht, selecteert u **Toep assen**.
   
1. Selecteer **volgende: netwerken** aan de onderkant van de pagina.
   
   ![Tabblad Nieuw SQL database-basis](../media/sql-database-single-database-get-started/new-sql-database-basics.png)
   
1. Selecteer op het tabblad **netwerken** onder **connectiviteits methode** **open bare eind punt**. 
1. Stel onder **firewall regels**het **huidige client-IP-adres toevoegen** in op **Ja**.
1. Selecteer **volgende: extra instellingen** onder aan de pagina.
   
   ![Tabblad netwerken](../media/sql-database-single-database-get-started/networking.png)
   
   Zie [Azure-Services en-bronnen toestaan om toegang te krijgen tot deze server](../sql-database-networkaccess-overview.md) en [een persoonlijk eind punt toe te voegen](../../private-link/private-endpoint-overview.md)voor meer informatie over Firewall instellingen.
   
1. Op het tabblad **extra instellingen** , in de sectie **gegevens bron** , voor het **gebruik van bestaande gegevens**, selecteert u voor **beeld**.
1. Selecteer onder aan de pagina **bekijken + maken** .
   
   ![Tabblad Extra instellingen](../media/sql-database-single-database-get-started/additional-settings.png)
   
1. Na het controleren van instellingen, selecteert u **maken**.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

U kunt een Azure-resource groep, een SQL-Server en één data base maken met behulp van de Azure-opdracht regel interface (Azure CLI). Als u de Azure Cloud Shell niet wilt gebruiken, [installeert u Azure cli](/cli/azure/install-azure-cli) op uw computer.

Als u het volgende code voorbeeld in Azure Cloud Shell wilt uitvoeren, selecteert u **Dit** in de voorbeeld titel balk van de code. Wanneer de Cloud Shell wordt geopend, selecteert u **kopiëren** in de titel balk van de voorbeeld code en plakt u het code voorbeeld in het venster Cloud shell. Vervang `<Subscription ID>` door de ID van uw Azure-abonnement in de code en vervang `$startip` en `$endip`door `0.0.0.0` te vervangen door het open bare IP-adres van de computer die u gebruikt.

Volg de aanwijzingen op het scherm om u aan te melden bij Azure en de code uit te voeren. 

U kunt ook de Azure Cloud Shell van de Azure Portal gebruiken door het pictogram Cloud Shell te selecteren in de bovenste balk. 
   
   ![Azure Cloud Shell](../media/sql-database-single-database-get-started/cloudshell.png)
   
De eerste keer dat u Cloud Shell in de portal gebruikt, selecteert u **bash** in het **welkomst** venster. Volgende sessies gebruiken Azure CLI in een bash-omgeving of u kunt **bash** selecteren in de Cloud shell controle balk. 

Met de volgende Azure CLI-code maakt u een Azure-resource groep, een SQL-Server, een enkele data base en een firewall regel voor toegang tot de server. Zorg ervoor dat u de gegenereerde resource groep en server namen vastlegt, zodat u deze resources later kunt beheren.

```azurecli-interactive
#!/bin/bash

# Sign in to Azure and set execution context (if necessary)
az login
az account set --subscription <Subscription ID>

# Set the resource group name and location for your server
resourceGroupName=myResourceGroup-$RANDOM
location=westus2

# Set an admin login and password for your database
adminlogin=azureuser
password=Azure1234567

# Set a logical server name that is unique in the system
servername=server-$RANDOM

# Set the ip address range that can access your database
startip=0.0.0.0
endip=0.0.0.0

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a logical server in the resource group
az sql server create \
    --name $servername \
    --resource-group $resourceGroupName \
    --location $location  \
    --admin-user $adminlogin \
    --admin-password $password

# Configure a firewall rule for the server
az sql server firewall-rule create \
    --resource-group $resourceGroupName \
    --server $servername \
    -n AllowYourIp \
    --start-ip-address $startip \
    --end-ip-address $endip

# Create a gen5 2 vCore database in the server
az sql db create \
    --resource-group $resourceGroupName \
    --server $servername \
    --name mySampleDatabase \
    --sample-name AdventureWorksLT \
    --edition GeneralPurpose \
    --family Gen5 \
    --capacity 2 \
```

De voor gaande code maakt gebruik van de volgende Azure CLI-opdrachten:

| Opdracht | Beschrijving |
|---|---|
| [AZ-account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Hiermee stelt u een abonnement in als het huidige actieve abonnement. | 
| [az group create](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Hiermee maakt u een SQL Database-server die individuele databases en elastische pools host. |
| [AZ SQL Server firewall-Rule Create](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-create) | Hiermee maakt u de firewall regels van een server. | 
| [az sql db create](/cli/azure/sql/db#az-sql-db-create?view=azure-cli-latest) | Hiermee maakt u een Data Base. | 

Zie [Azure cli](../sql-database-cli-samples.md)-voor beelden voor meer Azure SQL database Azure CLI-voor beelden.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

U kunt een Azure-resource groep, een SQL-Server en één data base maken met behulp van Windows Power shell. Als u de Azure Cloud Shell niet wilt gebruiken, [installeert u de module Azure PowerShell](/powershell/azure/install-az-ps).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als u het volgende code voorbeeld wilt uitvoeren in de Azure Cloud Shell, selecteert u **Dit** in de titel balk van de code. Wanneer de Cloud Shell wordt geopend, selecteert u **kopiëren** in de titel balk van de voorbeeld code en plakt u het code voorbeeld in het venster Cloud shell. Vervang `<Subscription ID>` door de ID van uw Azure-abonnement in de code en vervang `$startIp` en `$endIp`door `0.0.0.0` te vervangen door het open bare IP-adres van de computer die u gebruikt. 

Volg de aanwijzingen op het scherm om u aan te melden bij Azure en de code uit te voeren. 

U kunt Azure Cloud Shell ook gebruiken via de Azure Portal door het pictogram Cloud Shell te selecteren in de bovenste balk. 
   
   ![Azure Cloud Shell](../media/sql-database-single-database-get-started/cloudshell.png)
   
De eerste keer dat u Cloud Shell uit de portal gebruikt, selecteert u **Power shell** in het **welkomst** venster. Bij volgende sessies wordt Power shell gebruikt, of u kunt deze selecteren in de Cloud Shell controle balk. 

Met de volgende Power shell-code maakt u een Azure-resource groep, een SQL-Server, een enkele data base en een firewall regel voor toegang tot de server. Zorg ervoor dat u de gegenereerde resource groep en server namen vastlegt, zodat u deze resources later kunt beheren.

   ```powershell-interactive
   # Set variables for your server and database
   $subscriptionId = '<SubscriptionID>'
   $resourceGroupName = "myResourceGroup-$(Get-Random)"
   $location = "West US"
   $adminLogin = "azureuser"
   $password = "Azure1234567"
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server 
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName 
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

De bovenstaande code maakt gebruik van de volgende Power shell-cmdlets:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Hiermee maakt u een SQL Database-server die individuele databases en elastische pools host. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Hiermee maakt u een firewall regel voor een logische server. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Hiermee maakt u een Azure SQL Database afzonderlijke data base. | 

Zie Azure PowerShell-voor [beelden](../sql-database-powershell-samples.md)voor meer Azure SQL database Power shell-voor beelden.

---
