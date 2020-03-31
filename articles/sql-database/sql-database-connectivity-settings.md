---
title: Verbindingsinstellingen voor Azure SQL Database en Data Warehouse
description: In dit document wordt uitgelegd hoe TLS-versie keuze en Proxy vs. Redirect-instelling voor Azure SQL
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: d18fdee85bd0fbabe68fe9890c4a2dc74366041d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366084"
---
# <a name="azure-sql-connectivity-settings"></a>Azure SQL-connectiviteitsinstellingen
> [!NOTE]
> Dit artikel is van toepassing op Azure SQL-server en op zowel SQL Database- als SQL Data Warehouse-databases die zijn gemaakt op de Azure SQL-server. Voor het gemak wordt de term 'SQL Database' gebruikt wanneer er wordt verwezen naar zowel SQL Database als SQL Data Warehouse.

> [!IMPORTANT]
> Dit artikel is *niet* van toepassing op **Azure SQL Database Managed Instance**

In dit artikel worden instellingen geïntroduceerd die de connectiviteit met Azure SQL Database op serverniveau beheren. Deze instellingen zijn van toepassing op **alle** SQL Database- en SQL Data Warehouse-databases die aan de server zijn gekoppeld.

> [!NOTE]
> Zodra deze instellingen zijn toegepast, worden ze **onmiddellijk van kracht** en kunnen ze leiden tot verbindingsverlies voor uw klanten als ze niet voldoen aan de vereisten voor elke instelling.

De connectiviteitsinstellingen zijn toegankelijk vanaf de **firewalls en virtuele netwerken,** zoals weergegeven in de onderstaande schermafbeelding:

 ![Schermafbeelding van verbindingsinstellingen][1]


## <a name="deny-public-network-access"></a>Toegang tot openbare netwerken weigeren
In de Azure-portal zijn, wanneer de instelling **Openbare netwerktoegang weigeren** is ingesteld op **Ja,** alleen verbindingen via privéeindpunten toegestaan. Wanneer deze instelling is ingesteld op **Nee,** kunnen clients verbinding maken met het privé- of openbare eindpunt.

Nadat **u De toegang tot het openbare netwerk weigeren tot** **Ja**hebt ingesteld, mislukken aanmeldingspogingen van clients die openbaar eindpunt gebruiken met de volgende fout:

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. The public network interface on this server is not accessible. To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>Toegang tot openbare netwerken wijzigen via PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek. Voor het volgende script is de [Azure PowerShell-module](/powershell/azure/install-az-ps)vereist.

In het volgende PowerShell-script ziet u hoe `Get` u de `Set` eigenschap Openbare **netwerktoegang** op logisch serverniveau uitvoeren:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled" 
```

## <a name="change-public-network-access-via-cli"></a>Openbare netwerktoegang wijzigen via CLI
> [!IMPORTANT]
> Voor alle scripts in deze sectie is [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)vereist.

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI in een bash shell
In het volgende CLI-script ziet u hoe u de **openbare netwerktoegang** in een bash-shell wijzigen:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```


## <a name="connection-policy"></a>Verbindingsbeleid
[Het verbindingsbeleid](sql-database-connectivity-architecture.md#connection-policy) bepaalt hoe clients verbinding maken met Azure SQL Server. 

## <a name="change-connection-policy-via-powershell"></a>Verbindingsbeleid wijzigen via PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek. Voor het volgende script is de [Azure PowerShell-module](/powershell/azure/install-az-ps)vereist.

In het volgende PowerShell-script ziet u hoe u het verbindingsbeleid wijzigen met PowerShell:

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-connection-policy-via-azure-cli"></a>Verbindingsbeleid wijzigen via Azure CLI
> [!IMPORTANT]
> Voor alle scripts in deze sectie is [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)vereist.

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI in een bash shell
In het volgende CLI-script ziet u hoe u het verbindingsbeleid in een bashshell wijzigt: 

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>Azure CLI vanuit een Windows-opdrachtprompt
In het volgende CLI-script wordt uitgelegd hoe u het verbindingsbeleid wijzigen vanuit een Windows-opdrachtprompt (met Azure CLI geïnstalleerd).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Volgende stappen
- Raadpleeg Azure [SQL Connectivity Architecture](sql-database-connectivity-architecture.md) voor een overzicht van hoe connectiviteit werkt in Azure SQL Database
- Zie [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)voor informatie over het wijzigen van het verbindingsbeleid van Azure SQL Database voor een Azure SQL Database-server.

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
