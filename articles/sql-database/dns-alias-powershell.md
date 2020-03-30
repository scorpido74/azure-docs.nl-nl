---
title: PowerShell voor DNS-alias
description: Met PowerShell-cmdlets zoals New-AzSqlServerDNSAlias u nieuwe clientverbindingen omleiden naar een andere Azure SQL Database-server, zonder dat u een clientconfiguratie hoeft aan te raken.
keywords: dns sql-database
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: 9232a99ddd29201e6743c09455d79e9ba22b3b9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74420399"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell voor DNS-alias naar Azure SQL-database

In dit artikel wordt een PowerShell-script weergegeven dat laat zien hoe u een DNS-alias voor Azure SQL Database beheren.

> [!NOTE]
> Dit artikel is bijgewerkt om de Azure PowerShell Az-module of Azure CLI te gebruiken. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020.
>
> Zie [De Azure PowerShell Az-module](/powershell/azure/new-azureps-module-az)introduceren voor meer informatie over de Az-module en AzureRM-compatibiliteit. Zie [Azure PowerShell installeren](/powershell/azure/install-az-ps) of [Azure CLI installeren](/cli/azure/install-azure-cli)voor installatie-instructies.

## <a name="dns-alias-in-connection-string"></a>DNS-alias in verbindingstekenreeks

Als u een bepaalde Azure SQL Database-server wilt verbinden, kan een client zoals SQL Server Management Studio (SSMS) de DNS-aliasnaam opgeven in plaats van de ware servernaam. In de volgende voorbeeldservertekenreeks vervangt de alias *any-unique-alias-name* het eerste knooppunt met een gedelimiteerd knooppunt in de servertekenreeks met vier knooppunten:

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>Vereisten

Als u het powershell-script in dit artikel wilt uitvoeren, zijn de volgende voorwaarden van toepassing:

- Een Azure-abonnement en -account, voor gratis proefversies, zie [Azure-proefversies](https://azure.microsoft.com/free/)
- Twee Azure SQL-databaseservers

## <a name="example"></a>Voorbeeld

In het volgende codevoorbeeld worden letterlijke waarden toewijzen aan verschillende variabelen.

Als u de code wilt uitvoeren, bewerkt u de tijdelijke aanduidingswaarden om de werkelijke waarden in uw systeem weer te geven.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

De gebruikte cmdlets zijn de volgende:

- [Nieuw-AzSqlServerDNSAlias:](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias)Hiermee maakt u een DNS-alias in het Azure SQL Database-servicesysteem. De alias verwijst naar databaseserver 1.
- [Get-AzSqlServerDNSAlias:](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias)Alle aliassen die zijn toegewezen aan SQL DB server 1 oppakken en weergeven.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): wijzigt de servernaam waarnaar de alias is geconfigureerd, van server 1 naar server 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Verwijder de alias van databaseserver 2 met de naam van de alias.

Zie [Azure PowerShell-module installeren](/powershell/azure/install-az-ps) om de module te installeren of te upgraden.

Gebruik `Get-Module -ListAvailable Az` in *powershell\_ise.exe*, om de versie te vinden.

```powershell
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
Connect-AzAccount -SubscriptionName $subscriptionName;
$subscriptionId = Get-AzSubscription -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
New-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName `
    -Name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
Set-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -TargetServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName `
    -SourceServerResourceGroup $resourceGroupName -SourceServerName $sqlServerName `
    -SourceServerSubscriptionId $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
Remove-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName;
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

De gebruikte opdrachten zijn de volgende:

- [dns-alias maken van az sql server:](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias)Maakt een DNS-alias in het Azure SQL Database-servicesysteem. De alias verwijst naar databaseserver 1.
- [dns-alias az sql server tonen](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Alle aliassen oppakken en vermelden die zijn toegewezen aan SQL DB server 1.
- [dns-aliasset van az sql server](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): wijzigt de servernaam waarnaar de alias is geconfigureerd, van server 1 naar server 2.
- [dns-alias delete van az sql server](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Verwijder de alias van databaseserver 2 met de naam van de alias.

Zie Azure CLI [installeren](/cli/azure/install-azure-cli).

```azurecli-interactive
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
az login -SubscriptionName $subscriptionName;
$subscriptionId = az account list[0].i -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
az sql server dns-alias create –-resource-group $resourceGroupName --server $sqlServerName `
    --name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
az sql server dns-alias show –-resource-group $resourceGroupName --server $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
az sql server dns-alias set –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName `
    --original-resource-group $resourceGroupName --original-server $sqlServerName `
    --original-subscription-id $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
az sql server dns-alias show –-resource-group $resourceGroupName2 --server $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
az sql server dns-alias delete –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName;
```

* * *

## <a name="next-steps"></a>Volgende stappen

Zie [DNS-alias voor Azure SQL-database voor](dns-alias-overview.md)een volledige uitleg van de DNS-aliasfunctie voor SQL-database.
