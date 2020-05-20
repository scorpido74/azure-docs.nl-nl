---
title: Connectiviteits instellingen voor Azure SQL Database en Data Warehouse
description: In dit document worden de TLS-versie keuze en de instelling voor proxy versus omleiding voor Azure SQL uitgelegd
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: ef846b23bf6c9da2b7dd64927eac7cc1a1704dae
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684951"
---
# <a name="azure-sql-connectivity-settings"></a>Azure SQL-connectiviteitsinstellingen
> [!NOTE]
> Dit artikel is van toepassing op de logische SQL-Server in azure die wordt gebruikt voor zowel Azure SQL Database als SQL Data Warehouse-data bases die zijn gemaakt op de logische server. Voor het gemak wordt de term 'SQL Database' gebruikt wanneer er wordt verwezen naar zowel SQL Database als SQL Data Warehouse.

> [!IMPORTANT]
> Dit artikel is *niet* van toepassing op **Azure SQL database beheerde instantie**

Dit artikel bevat een overzicht van de instellingen waarmee de verbinding met Azure SQL Database op server niveau wordt beheerd. Deze instellingen zijn van toepassing op **alle** SQL database-en SQL Data Warehouse-data bases die zijn gekoppeld aan de server.

> [!NOTE]
> Zodra deze instellingen zijn toegepast, worden ze **onmiddellijk van kracht** . Dit kan leiden tot een verbindings verlies voor uw clients als ze niet voldoen aan de vereisten voor elke instelling.

De connectiviteits instellingen zijn toegankelijk via het scherm **firewalls en virtuele netwerken** , zoals wordt weer gegeven in de volgende scherm afbeelding:

 ![Scherm opname van connectiviteits instellingen][1]


## <a name="deny-public-network-access"></a>Open bare netwerk toegang weigeren

Klanten kunnen verbinding maken met SQL Database met behulp van open bare eind punten (op IP gebaseerde firewall regels, op VNET gebaseerde firewall regels) of privé-eind punten (met behulp van een persoonlijke koppeling), zoals beschreven in het [overzicht van netwerk toegang](sql-database-networkaccess-overview.md). 

Wanneer de instelling **open bare netwerk toegang weigeren** is ingesteld op **Ja**, worden alleen verbindingen via persoonlijke eind punten toegestaan en worden alle verbindingen via open bare eind punten geweigerd met een fout bericht dat lijkt op het volgende:  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>Open bare netwerk toegang wijzigen via Power shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek. Voor het volgende script is de [Azure PowerShell-module](/powershell/azure/install-az-ps)vereist.

In het volgende Power shell-script ziet u hoe `Get` en `Set` de eigenschap **open bare netwerk toegang** op het niveau van de logische server:

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

Zie [TLS-overwegingen voor SQL database connectiviteit](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)voor meer informatie.

Nadat u de minimale versie van TLS hebt ingesteld, mislukken aanmeldings pogingen van clients die een TLS-versie gebruiken die lager is dan de minimale TLS-versie van de server, mislukt met de volgende fout:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Minimale TLS-versie instellen via Power shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
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

## <a name="connection-policy"></a>Verbindings beleid

Het [verbindings beleid](sql-database-connectivity-architecture.md#connection-policy) bepaalt hoe clients verbinding maken met Azure SQL Server. 

## <a name="change-connection-policy-via-powershell"></a>Verbindings beleid wijzigen via Power shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
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

- Raadpleeg [Azure SQL-verbindings architectuur](sql-database-connectivity-architecture.md) voor een overzicht van de werking van connectiviteit in Azure SQL database.
- Zie voor meer informatie over het wijzigen van het Azure SQL Database verbindings beleid voor een Azure SQL Database-Server verbinding [-beleid](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
