---
title: 'Zelf studie: een Data Base toevoegen aan een failovergroep'
description: Voeg een data base in Azure SQL Database toe aan een groep met herfailovers met behulp van de Azure Portal, Power shell of de Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: 4df537e8cce51cc7358784fc72ef8ae077614df7
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344557"
---
# <a name="tutorial-add-an-azure-sql-database-to-an-autofailover-group"></a>Zelf studie: een Azure SQL Database toevoegen aan een groep van een failover
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Een [failover-groep](auto-failover-group-overview.md) is een declaratieve abstractie laag waarmee u meerdere geo-gerepliceerde data bases kunt groeperen. Meer informatie over het configureren van een failovergroep voor een Azure SQL Database en testfailover met behulp van de Azure Portal, Power shell of de Azure CLI.  In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - Een Data Base maken in Azure SQL Database
> - Maak een failovergroep voor de data base tussen twee servers.
> - Testfailover.

## <a name="prerequisites"></a>Vereisten

# <a name="the-portal"></a>[De portal](#tab/azure-portal)

Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien:

- Een Azure-abonnement. [Maak een gratis account](https://azure.microsoft.com/free/) als u er nog geen hebt.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Zorg ervoor dat u over de volgende items beschikt om de zelf studie te volt ooien:

- Een Azure-abonnement. [Maak een gratis account](https://azure.microsoft.com/free/) als u er nog geen hebt.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)

# <a name="the-azure-cli"></a>[De Azure CLI](#tab/azure-cli)

Zorg ervoor dat u over de volgende items beschikt om de zelf studie te volt ooien:

- Een Azure-abonnement. [Maak een gratis account](https://azure.microsoft.com/free/) als u er nog geen hebt.
- De nieuwste versie van [de Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest).

---

## <a name="1---create-a-database"></a>1: een Data Base maken

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2-de groep failover maken

In deze stap maakt u een [failovergroep](auto-failover-group-overview.md) tussen een bestaande server en een nieuwe server in een andere regio. Voeg vervolgens de voorbeeld database toe aan de failovergroep.

# <a name="the-portal"></a>[De portal](#tab/azure-portal)

Maak uw failovergroep en voeg uw data base toe met behulp van de Azure Portal.

1. Selecteer **Azure SQL** in het linkermenu van de [Azure Portal](https://portal.azure.com). Als **Azure SQL** niet voor komt in de lijst, selecteert u **alle services**en typt u vervolgens Azure SQL in het zoekvak. Beschrijving Selecteer de ster naast **Azure SQL** om deze te favoriet en voeg deze toe als een item in de linkernavigatiebalk.
1. Selecteer de data base die is gemaakt in sectie 1, zoals `mySampleDatabase` .
1. Failover-groepen kunnen op server niveau worden geconfigureerd. Selecteer de naam van de server onder **Server naam** om de instellingen voor de server te openen.

   ![Server voor Data Base openen](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. Selecteer **failover-groepen** in het deel venster **instellingen** en selecteer vervolgens **groep toevoegen** om een nieuwe failovergroep te maken.

   ![Nieuwe failovergroep toevoegen](./media/failover-group-add-single-database-tutorial/sqldb-add-new-failover-group.png)

1. Voer op de pagina **failover-groep** de volgende waarden in of Selecteer deze en selecteer vervolgens **maken**:

   - **Naam van failovergroep**: Typ een unieke naam voor de failovergroep, zoals `failovergrouptutorial` .
   - **Secundaire server**: Selecteer de optie voor het configureren van de *vereiste instellingen* en kies vervolgens **een nieuwe server maken**. U kunt ook een al bestaande server kiezen als de secundaire server. Nadat u de volgende waarden hebt ingevoerd, selecteert u **selecteren**.
      - **Server naam**: Typ een unieke naam voor de secundaire server, bijvoorbeeld `mysqlsecondary` .
      - **Aanmelding van de server beheerder**: type`azureuser`
      - **Wacht woord**: Typ een complex wacht woord dat voldoet aan de wachtwoord vereisten.
      - **Locatie**: Kies een locatie in de vervolg keuzelijst, zoals `East US` . Deze locatie mag niet dezelfde locatie zijn als de primaire server.

     > [!NOTE]
     > De aanmeldings-en Firewall instellingen van de server moeten overeenkomen met die van de primaire server.

     ![Een secundaire server maken voor de failovergroep](./media/failover-group-add-single-database-tutorial/create-secondary-failover-server.png)

   - **Data bases in de groep**: wanneer een secundaire server is geselecteerd, wordt deze optie ontgrendeld. Selecteer deze optie om **data bases te selecteren die u wilt toevoegen** en kies vervolgens de data base die u hebt gemaakt in sectie 1. Als u de data base aan de groep failover toevoegt, wordt het proces voor geo-replicatie automatisch gestart.

   ![SQL Database toevoegen aan failovergroep](./media/failover-group-add-single-database-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Maak uw failovergroep en voeg uw data base aan de groep toe met behulp van Power shell.

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
   Write-host "Creating a secondary server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary server..."
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
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Hiermee maakt u een server in Azure SQL Database die als host fungeert voor afzonderlijke data bases en elastische Pools. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Hiermee maakt u een firewall regel voor een server in Azure SQL Database. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Hiermee maakt u een nieuwe afzonderlijke data base in Azure SQL Database. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Hiermee maakt u een nieuwe failover-groep in Azure SQL Database. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Hiermee haalt u een of meer data bases op in Azure SQL Database. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Hiermee voegt u een of meer data bases toe aan een failovergroep in Azure SQL Database. |

# <a name="the-azure-cli"></a>[De Azure CLI](#tab/azure-cli)

Maak uw failovergroep en voeg uw data base toe met behulp van de Azure CLI.

   > [!NOTE]
   > De aanmeldings-en Firewall instellingen van de server moeten overeenkomen met die van de primaire server.

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $failoverLocation = "West US"
   $failoverServer = "failoverServer-$randomIdentifier"
   $failoverGroup = "failoverGroup-$randomIdentifier"

   echo "Creating a secondary server in the DR region..."
   az sql server create --name $failoverServer --resource-group $resourceGroup --location $failoverLocation --admin-user $login --admin-password $password

   echo "Creating a failover group between the two servers..."
   az sql failover-group create --name $failoverGroup --partner-server $failoverServer --resource-group $resourceGroup --server $server --add-db $database --failover-policy Automatic
   ```

In dit gedeelte van de zelf studie worden de volgende Azure CLI-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Hiermee maakt u een server die als host fungeert voor data bases en elastische Pools. |
| [AZ SQL Server firewall-Rule Create](/cli/azure/sql/server/firewall-rule) | Hiermee maakt u de firewall regels van een server. |
| [AZ SQL failover-Group Create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Hiermee maakt u een failovergroep. |

---

## <a name="3---test-failover"></a>3-testfailover

In deze stap wordt uw failover-groep naar de secundaire server gefailovert en wordt er een failback uitgevoerd met behulp van de Azure Portal.

# <a name="the-portal"></a>[De portal](#tab/azure-portal)

Testfailover met behulp van de Azure Portal.

1. Selecteer **Azure SQL** in het linkermenu van de [Azure Portal](https://portal.azure.com). Als **Azure SQL** niet voor komt in de lijst, selecteert u **alle services**en typt u vervolgens Azure SQL in het zoekvak. Beschrijving Selecteer de ster naast **Azure SQL** om deze te favoriet en voeg deze toe als een item in de linkernavigatiebalk.
1. Selecteer de data base die is gemaakt in de sectie 2, zoals `mySampleDatbase` .
1. Selecteer de naam van de server onder **Server naam** om de instellingen voor de server te openen.

   ![Server voor Data Base openen](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. Selecteer **failover-groepen** in het deel venster **instellingen** en kies vervolgens de failovergroep die u hebt gemaakt in sectie 2.
  
   ![De groep failover selecteren in de portal](./media/failover-group-add-single-database-tutorial/select-failover-group.png)

1. Controleer welke server primair is en welke server secundair is.
1. Selecteer **failover** in het taak venster voor het uitvoeren van een failover voor uw failovercluster-groep met uw voorbeeld database.
1. Selecteer **Ja** in de waarschuwing waarmee u wordt gewaarschuwd dat TDS-sessies worden losgekoppeld.

   ![Failover uitvoeren van uw failoverrelatie met uw data base in SQL database](./media/failover-group-add-single-database-tutorial/failover-sql-db.png)

1. Controleer welke server nu primair is en welke server secundair is. Als de failover is geslaagd, moeten de twee servers wisselende rollen hebben.
1. Selecteer **failover** opnieuw om de oorspronkelijke rollen van de servers te herstellen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="the-azure-cli"></a>[De Azure CLI](#tab/azure-cli)

Testfailover met behulp van de Azure CLI.

Controleer of de server de secundaire is:

   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

Failover naar de secundaire server:

   ```azurecli-interactive
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $failoverServer
   echo "Successfully failed failover group over to" $failoverServer
   ```

Herstel de failovergroep terug naar de primaire server:

   ```azurecli-interactive
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   echo "Successfully failed failover group back to" $server
   ```

In dit gedeelte van de zelf studie worden de volgende Azure CLI-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [AZ SQL failover-Group List](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Geeft een lijst van de failover-groepen op een server. |
| [AZ SQL failover-groeps Set-Primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Stel de primaire groep van de failovergroep in door het uitvoeren van een failover voor alle data bases van de huidige primaire server. |

---

## <a name="clean-up-resources"></a>Resources opschonen

Resources opschonen door de resource groep te verwijderen.

# <a name="the-portal"></a>[De portal](#tab/azure-portal)

Verwijder de resource groep met behulp van de Azure Portal.

1. Navigeer naar uw resource groep in de [Azure Portal](https://portal.azure.com).
1. Selecteer **resource groep verwijderen** om alle resources in de groep en de resource groep zelf te verwijderen.
1. Typ de naam van de resource groep, `myResourceGroup` in het tekstvak en selecteer vervolgens **verwijderen** om de resource groep te verwijderen.  

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="the-azure-cli"></a>[De Azure CLI](#tab/azure-cli)

Verwijder de resource groep met behulp van de Azure CLI.

   ```azurecli-interactive
   echo "Cleaning up resources by removing the resource group..."
   az group delete --name $resourceGroup
   echo "Successfully removed resource group" $resourceGroup
   ```

In dit gedeelte van de zelf studie worden de volgende Azure CLI-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

---

> [!IMPORTANT]
> Als u de resource groep wilt blijven gebruiken, maar de secundaire Data Base wilt verwijderen, verwijdert u deze uit de failovergroep voordat u deze verwijdert. Het verwijderen van een secundaire Data Base voordat deze wordt verwijderd uit de failovergroep kan onvoorspelbaar gedrag veroorzaken.

## <a name="full-scripts"></a>Volledige scripts

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add database to a failover group")]

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Hiermee maakt u een server die als host fungeert voor afzonderlijke data bases en elastische Pools in Azure SQL Database. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Hiermee maakt u een firewall regel voor een server in Azure SQL Database. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Hiermee maakt u een nieuwe data base in Azure SQL Database. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Hiermee maakt u een nieuwe failover-groep in Azure SQL Database. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Hiermee haalt u een of meer data bases op in Azure SQL Database. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Hiermee voegt u een of meer data bases toe aan een failovergroep in Azure SQL Database. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hiermee worden failover-groepen in Azure SQL Database opgehaald of weer gegeven. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Hiermee wordt een failover uitgevoerd van een failovergroep in Azure SQL Database. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resource groep in Azure SQL Database.|

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add database to a failover group")]

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [AZ-account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Hiermee stelt u een abonnement in als het huidige actieve abonnement. |
| [az group create](/cli/azure/group#az-group-create) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Hiermee maakt u een server die als host fungeert voor afzonderlijke data bases en elastische Pools in Azure SQL Database. |
| [AZ SQL Server firewall-Rule Create](/cli/azure/sql/server/firewall-rule) | Hiermee maakt u de IP-firewall regels op server niveau in Azure SQL Database. |
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Hiermee maakt u een data base in Azure SQL Database. |
| [AZ SQL failover-Group Create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Hiermee maakt u een failover-groep in Azure SQL Database. |
| [AZ SQL failover-Group List](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Geeft een lijst van de failover-groepen op een server in Azure SQL Database. |
| [AZ SQL failover-groeps Set-Primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Stel de primaire groep van de failovergroep in door het uitvoeren van een failover voor alle data bases van de huidige primaire server. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

# <a name="the-portal"></a>[De portal](#tab/azure-portal)

Er zijn geen scripts beschikbaar voor de Azure Portal.

---

U vindt hier ook andere Azure SQL Database scripts: [Azure PowerShell](powershell-script-content-guide.md) en [Azure cli](az-cli-script-samples-content-guide.md).

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een data base in Azure SQL Database aan een failovergroep toegevoegd en is de failover getest. U hebt geleerd hoe u:

> [!div class="checklist"]
>
> - Een Data Base maken in Azure SQL Database
> - Maak een failovergroep voor de data base tussen twee servers.
> - Testfailover.

Ga verder met de volgende zelf studie over het toevoegen van een elastische pool aan een failovergroep.

> [!div class="nextstepaction"]
> [Zelf studie: een Azure SQL Database elastische pool toevoegen aan een failovergroep](failover-group-add-elastic-pool-tutorial.md)
