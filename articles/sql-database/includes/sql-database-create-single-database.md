---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 03/10/2020
ms.author: mathoma
ms.reviewer: vanto
ms.openlocfilehash: 563ac8748e9d4f8a254a151814491113b9c816e6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685146"
---
In deze stap maakt u een Azure SQL Database-server en één database die gebruikmaakt van AdventureWorksLT-voorbeeldgegevens. U de database maken met azure-portalmenu's en -schermen of met behulp van een Azure CLI- of PowerShell-script in de Azure Cloud Shell.

Alle methoden omvatten het instellen van een firewallregel op serverniveau om het openbare IP-adres van de computer die u gebruikt toegang te geven tot de server. Zie Een firewall op serverniveau maken voor meer informatie over het maken [van serverfirewallregels.](../sql-database-server-level-firewall-rule.md) U firewallregels op databaseniveau ook instellen. Zie [Een firewallregel op databaseniveau maken](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database). 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ga als lid van het nieuwe bedrijf naar een brongroep, SQL-server en één database in de Azure-portal:

1. Meld u aan bij de [portal](https://portal.azure.com).
1. Zoek in de zoekbalk naar Azure SQL en selecteer **deze.**
1. Selecteer op de **azure SQL-pagina** De optie **Toevoegen**. 
   
   ![Toevoegen aan Azure SQL](../media/sql-database-single-database-get-started/sqldbportal.png)
   
1. Selecteer op de optiepagina **SQL-implementatie** selecteren de tegel **SQL-databases** met **Één database** onder **Resourcetype**. U meer informatie over de verschillende databases bekijken door **Details weergeven te**selecteren.
1. Selecteer **Maken**.
   
   ![Individuele database maken](../media/sql-database-single-database-get-started/create-single-database.png)
   
1. Selecteer op het tabblad **Basisbeginselen** van het **SQL-databaseformulier maken** onder **Projectdetails**het juiste **Azure-abonnement** als het nog niet is geselecteerd.
1. Selecteer **onder Resourcegroep**De optie **Nieuw maken,** voer *myResourceGroup*in en selecteer **OK**.
1. Voer **onder Databasegegevens**voor **Databasenaam** *mySampleDatabase*in .
1. Selecteer **voor Server**Nieuw **maken**en vul het **nieuwe serverformulier** als volgt in:
   - **Servernaam:** Voer *mysqlserver*in en enkele tekens voor uniciteit.
   - **Serverbeheerder login**: Voer *azureuser*in .
   - **Wachtwoord:** Voer een wachtwoord in dat aan de vereisten voldoet en voer het opnieuw in in het veld **Wachtwoord bevestigen.**
   - **Locatie**: Drop down en kies een locatie, zoals **(US) East US**.
   
   Selecteer **OK**.
   
   ![Nieuwe server](../media/sql-database-single-database-get-started/new-server.png)
   
   Neem de login en het wachtwoord van de serverbeheerder op, zodat u zich aanmelden bij de server en databases. Als u uw aanmelding of wachtwoord bent vergeten, u de aanmeldingsnaam krijgen of het wachtwoord na het aanmaken van de database opnieuw instellen op de **SQL-serverpagina.** Als u de **SQL-serverpagina** wilt openen, selecteert u de servernaam op de pagina **Databaseoverzicht.**
   
1. Als **Compute + storage**u de standaardinstellingen opnieuw wilt configureren, selecteert u **Database configureren**als u de standaardinstellingen opnieuw wilt configureren.
   
   Op de pagina **Configureren** u het optioneel doen:
   - Wijzig de **laag Berekenen** van **Ingerichte** naar **Serverless**.
   - Bekijk en wijzig de instellingen voor **vCores** en **Data max-grootte**.
   - Selecteer **Configuratie wijzigen** om de hardwaregeneratie te wijzigen.
   
   Nadat u wijzigingen hebt aangebracht, selecteert u **Toepassen**.
   
1. Selecteer **Volgende: Netwerken** onder aan de pagina.
   
   ![Nieuwe SQL-database - Tabblad Basis](../media/sql-database-single-database-get-started/new-sql-database-basics.png)
   
1. Selecteer op het tabblad **Netwerken** onder **Verbindingsmethode**de optie **Openbaar eindpunt**. 
1. Stel **onder Firewall-regels** **het huidige IP-adres van de client** toevoegen in op **Ja**.
1. Selecteer **Volgende: Extra instellingen** onder aan de pagina.
   
   ![Tabblad Netwerken](../media/sql-database-single-database-get-started/networking.png)
   
   Zie [Azure-services en -bronnen toestaan toegang te krijgen tot deze server](../sql-database-networkaccess-overview.md) en Een [privéeindpunt toevoegen](../../private-link/private-endpoint-overview.md)voor meer informatie over firewall-instellingen.
   
1. Selecteer **Voorbeeld**op het tabblad **Extra instellingen** in de sectie **Gegevensbron** voor **Het gebruik van bestaande gegevens**.
1. Selecteer **Controleren + maken** onder aan de pagina.
   
   ![Tabblad Extra instellingen](../media/sql-database-single-database-get-started/additional-settings.png)
   
1. Nadat u de instellingen hebt gecontroleerd, selecteert u **Maken**.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U een Azure-brongroep, SQL-server en één database maken met behulp van de Azure Command Line-interface (Azure CLI). Als u de Azure Cloud Shell niet wilt gebruiken, [installeert u Azure CLI](/cli/azure/install-azure-cli) op uw computer.

Als u het volgende codevoorbeeld wilt uitvoeren in Azure Cloud Shell, selecteert **u Proberen in** de titelbalk van het codevoorbeeld. Wanneer de Cloud Shell wordt geopend, selecteert u Kopiëren in de titelbalk **van** het codevoorbeeld en plakt u het codevoorbeeld in het venster Cloud Shell. Vervang `<Subscription ID>` in de code uw Azure-abonnements-id `0.0.0.0` en vervang voor `$startip` en `$endip`, met het openbare IP-adres van de computer die u gebruikt.

Volg de aanwijzingen op het scherm om u aan te melden bij Azure en voer de code uit. 

U de Azure Cloud Shell ook gebruiken via de Azure-portal door het Pictogram Cloud Shell op de bovenste balk te selecteren. 
   
   ![Azure Cloud Shell](../media/sql-database-single-database-get-started/cloudshell.png)
   
De eerste keer dat u Cloud Shell in de portal gebruikt, selecteert u **Bash** in het dialoogvenster **Welkom.** Volgende sessies gebruiken Azure CLI in een Bash-omgeving of u **Bash** selecteren in de cloudshell-besturingselementbalk. 

Met de volgende Azure CLI-code wordt een Azure-brongroep, SQL-server, één database en firewallregel voor toegang tot de server geveld. Zorg ervoor dat u de gegenereerde brongroep en servernamen opneemt, zodat u deze bronnen later beheren.

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

# Set a logical server name that is unique to Azure DNS (<server_name>.database.windows.net)
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

De voorgaande code gebruikt de volgende Azure CLI-opdrachten:

| Opdracht | Beschrijving |
|---|---|
| [az-account ingesteld](/cli/azure/account?view=azure-cli-latest#az-account-set) | Hiermee stelt u een abonnement in als het huidige actieve abonnement. | 
| [az group create](/cli/azure/group#az-group-create) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Hiermee maakt u een SQL Database-server die individuele databases en elastische pools host. |
| [az sql server firewall-rule maken](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-create) | Hiermee maakt u de firewallregels van een server. | 
| [az sql db create](/cli/azure/sql/db#az-sql-db-create?view=azure-cli-latest) | Hiermee maakt u een database. | 

Zie [Azure CLI-voorbeelden](../sql-database-cli-samples.md)voor meer Azure SQL Database Azure CLI-voorbeelden .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

U een Azure-brongroep, SQL-server en één database maken met Windows PowerShell. Als u de Azure Cloud Shell niet wilt gebruiken, [installeert u de Azure PowerShell-module](/powershell/azure/install-az-ps).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als u het volgende codevoorbeeld wilt uitvoeren in de Azure Cloud Shell, selecteert **u Proberen** in de codetitelbalk. Wanneer de Cloud Shell wordt geopend, selecteert u Kopiëren in de titelbalk **van** het codevoorbeeld en plakt u het codevoorbeeld in het venster Cloud Shell. Vervang `<Subscription ID>` in de code uw Azure-abonnements-id `0.0.0.0` en vervang voor `$startIp` en `$endIp`, met het openbare IP-adres van de computer die u gebruikt. 

Volg de aanwijzingen op het scherm om u aan te melden bij Azure en voer de code uit. 

U Azure Cloud Shell ook gebruiken via de Azure-portal door het Pictogram Cloud Shell op de bovenste balk te selecteren. 
   
   ![Azure Cloud Shell](../media/sql-database-single-database-get-started/cloudshell.png)
   
De eerste keer dat u Cloud Shell van de portal gebruikt, selecteert u **PowerShell** in het dialoogvenster **Welkom.** Volgende sessies gebruiken PowerShell of u deze selecteren in de Cloud Shell-besturingsbalk. 

Met de volgende PowerShell-code wordt een Azure-brongroep, SQL-server, één database en firewallregel voor toegang tot de server geveld. Zorg ervoor dat u de gegenereerde brongroep en servernamen opneemt, zodat u deze bronnen later beheren.

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

De voorgaande code gebruikt deze PowerShell-cmdlets:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [Nieuw-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Hiermee maakt u een SQL Database-server die individuele databases en elastische pools host. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Hiermee maakt u een firewallregel voor een logische server. | 
| [Nieuwe AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Hiermee maakt u een enkele database van Azure SQL Database. | 

Zie [Azure PowerShell-voorbeelden](../sql-database-powershell-samples.md)voor meer Azure SQL Database PowerShell-voorbeelden .

---
