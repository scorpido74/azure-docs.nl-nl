---
title: Connectiviteits instellingen voor Azure SQL Database en Data Warehouse
description: Dit document bevat informatie over de TLS-versie keuze en de instelling van proxy versus omleiding voor Azure SQL Database en Azure Synapse Analytics
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: 7b8fc2145c8b1c9da67f0ef44397b0e765b03de2
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84046605"
---
# <a name="azure-sql-connectivity-settings"></a>Azure SQL-connectiviteitsinstellingen
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Dit artikel bevat een inleiding tot de instellingen die de verbinding met de server voor Azure SQL Database en Azure Synapse Analytics regelen. Deze instellingen zijn van toepassing op **alle** SQL database-en Azure Synapse-data bases die zijn gekoppeld aan de server.

> [!IMPORTANT]
> Dit artikel is *niet* van toepassing op een door **Azure SQL beheerd exemplaar**

De connectiviteits instellingen zijn toegankelijk via het scherm **firewalls en virtuele netwerken** , zoals wordt weer gegeven in de volgende scherm afbeelding:

 ![Scherm opname van connectiviteits instellingen][1]

> [!NOTE]
> Zodra deze instellingen zijn toegepast, worden ze **onmiddellijk van kracht** . Dit kan leiden tot een verbindings verlies voor uw clients als ze niet voldoen aan de vereisten voor elke instelling.

## <a name="deny-public-network-access"></a>Open bare netwerk toegang weigeren

In de Azure Portal, wanneer de instelling **open bare netwerk toegang weigeren** is ingesteld op **Ja**, worden alleen verbindingen via persoonlijke eind punten toegestaan. Als deze instelling is ingesteld op **Nee**, kunnen clients verbinding maken met behulp van het privé-of open bare eind punt.

Klanten kunnen verbinding maken met SQL Database met behulp van open bare eind punten (op IP gebaseerde firewall regels, op VNET gebaseerde firewall regels) of privé-eind punten (met behulp van een persoonlijke koppeling), zoals beschreven in het [overzicht van netwerk toegang](network-access-controls-overview.md). 

Wanneer de instelling **open bare netwerk toegang weigeren** is ingesteld op **Ja**, worden alleen verbindingen via persoonlijke eind punten toegestaan en worden alle verbindingen via open bare eind punten geweigerd met een fout bericht dat lijkt op het volgende:  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>Open bare netwerk toegang wijzigen via Power shell

> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek. Voor het volgende script is de [Azure PowerShell-module](/powershell/azure/install-az-ps)vereist.

In het volgende Power shell-script ziet u hoe `Get` en `Set` de eigenschap **open bare netwerk toegang** op server niveau:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled"
```

## <a name="change-public-network-access-via-cli"></a>Open bare netwerk toegang via CLI wijzigen

> [!IMPORTANT]
> Alle scripts in deze sectie vereisen [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI in een bash-shell

Het volgende CLI-script laat zien hoe u de **open bare netwerk toegang** in een bash-shell wijzigt:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```

## <a name="minimal-tls-version"></a>Minimale TLS-versie 

Met de instelling minimale versie van [Transport Layer Security (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) kunnen klanten de versie van TLS beheren die door hun Azure SQL database wordt gebruikt.

Momenteel ondersteunen we TLS 1,0, 1,1 en 1,2. Als u een minimale TLS-versie instelt, worden nieuwe versies van TLS ondersteund. Bijvoorbeeld, bijvoorbeeld het kiezen van een TLS-versie die groter is dan 1,1. houdt in dat alleen verbindingen met TLS 1,1 en 1,2 worden geaccepteerd en dat TLS 1,0 wordt afgewezen. Nadat u hebt getest om te bevestigen dat uw toepassingen deze ondersteunen, wordt u aangeraden minimale TLS-versie in te stellen op 1,2, omdat deze oplossingen bevat voor beveiligings problemen die in vorige versies zijn gevonden en de hoogste versie van TLS ondersteund in Azure SQL Database.

Voor klanten met toepassingen die afhankelijk zijn van oudere versies van TLS, wordt u aangeraden de minimale TLS-versie in te stellen volgens de vereisten van uw toepassingen. Voor klanten die afhankelijk zijn van toepassingen om verbinding te maken met behulp van een niet-versleutelde verbinding, wordt u aangeraden geen minimale TLS-versie in te stellen. 

Zie [TLS-overwegingen voor SQL database connectiviteit](connect-query-content-reference-guide.md#tls-considerations-for-sql-database-connectivity)voor meer informatie.

Nadat u de minimale versie van TLS hebt ingesteld, mislukken aanmeldings pogingen van clients die een TLS-versie gebruiken die lager is dan de minimale TLS-versie van de server, mislukt met de volgende fout:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Minimale TLS-versie instellen via Power shell

> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek. Voor het volgende script is de [Azure PowerShell-module](/powershell/azure/install-az-ps)vereist.

Het volgende Power shell-script laat zien hoe `Get` en hoe `Set` de eigenschap van de **minimale TLS-versie** op het niveau van de logische server:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Minimale TLS-versie instellen via Azure CLI

> [!IMPORTANT]
> Alle scripts in deze sectie vereisen [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI in een bash-shell

Het volgende CLI-script laat zien hoe u de instelling **minimale TLS-versie** in een bash-shell wijzigt:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```


## <a name="change-connection-policy"></a>Verbindings beleid wijzigen

Het [verbindings beleid](connectivity-architecture.md#connection-policy) bepaalt hoe clients verbinding maken met Azure SQL database.


## <a name="change-connection-policy-via-powershell"></a>Verbindings beleid wijzigen via Power shell

> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek. Voor het volgende script is de [Azure PowerShell-module](/powershell/azure/install-az-ps)vereist.

Het volgende Power shell-script laat zien hoe u het verbindings beleid kunt wijzigen met behulp van Power shell:

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

## <a name="change-connection-policy-via-azure-cli"></a>Verbindings beleid wijzigen via Azure CLI

> [!IMPORTANT]
> Alle scripts in deze sectie vereisen [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI in een bash-shell

Het volgende CLI-script laat zien hoe u het verbindings beleid wijzigt in een bash-shell:

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

### <a name="azure-cli-from-a-windows-command-prompt"></a>Azure CLI vanaf een Windows-opdracht prompt

Het volgende CLI-script laat zien hoe u het verbindings beleid wijzigt vanuit een Windows-opdracht prompt (waarbij Azure CLI is geïnstalleerd).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg [connectiviteits architectuur](connectivity-architecture.md) voor een overzicht van de werking van connectiviteit in Azure SQL database.
- Zie voor meer informatie over het wijzigen van het verbindings beleid voor een-server verbinding [-beleid](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
