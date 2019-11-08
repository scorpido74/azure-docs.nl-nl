---
title: Power shell voor DNS-alias
description: Met Power shell-cmdlets zoals New-AzSqlServerDNSAlias kunt u nieuwe client verbindingen omleiden naar een andere Azure SQL Database Server, zonder dat u een client configuratie hoeft aan te gaan.
keywords: DNS SQL-data base
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
ms.openlocfilehash: cb1854c27a3722bc9c3c682c4787395c680d6241
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808457"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>Power shell voor het Azure SQL Database van DNS-alias

Dit artikel bevat een Power shell-script dat laat zien hoe u een DNS-alias voor Azure SQL Database kunt beheren. Het script voert de volgende cmdlets uit die de volgende acties uitvoeren:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De cmdlets die in het code voorbeeld worden gebruikt, zijn de volgende:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): maakt een nieuwe DNS-alias in het Azure SQL database-service systeem. De alias verwijst naar Azure SQL Database Server 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): alle DNS-aliassen ophalen en weer geven die zijn toegewezen aan SQL DB-server 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): wijzigt de server naam waarmee de alias is geconfigureerd, van Server 1 naar SQL DB Server 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Verwijder de DNS-alias uit SQL DB-Server 2, met behulp van de naam van de alias.

## <a name="dns-alias-in-connection-string"></a>DNS-alias in connection string

Een client zoals SQL Server Management Studio (SSMS) kan de DNS-alias naam opgeven in plaats van de werkelijke server naam om verbinding te maken met een bepaalde Azure SQL Database Server. In het volgende voor beeld van een server teken reeks vervangt de alias *elke-unieke alias naam* het knoop punt met de eerste punt als scheidings teken in de reeks van de vier knooppunt server:

- Voor beeld van een server teken reeks: `any-unique-alias-name.database.windows.net`.

## <a name="prerequisites"></a>Vereisten

Als u het Power shell-script wilt uitvoeren dat in dit artikel wordt vermeld, gelden de volgende vereisten:

- Een Azure-abonnement en-account. Klik op [https://azure.microsoft.com/free/][https://azure.microsoft.com/free/]voor een gratis proef versie.
- Azure PowerShell-module met de cmdlet **New-AzSqlServerDNSAlias**.
  - Zie [Azure PowerShell-module installeren][install-Az-ps-84p] om de module te installeren of te upgraden.
  - Voer `Get-Module -ListAvailable Az;` in Power shell\_ISE. exe uit om de versie te vinden.
- Twee Azure SQL Database-servers.

## <a name="code-example"></a>Voorbeeld van code

Het volgende Power shell-voorbeeld code wordt gestart door letterlijke waarden toe te wijzen aan verschillende variabelen. Als u de code wilt uitvoeren, moet u eerst alle waarden van de tijdelijke aanduiding bewerken zodat deze overeenkomen met echte waarden in uw systeem. Of u hoeft alleen maar de code te bestuderen. En de console-uitvoer van de code wordt ook gegeven.

```powershell
################################################################
###    Assign prerequisites.                                 ###
################################################################
cls;

$SubscriptionName             = '<EDIT-your-subscription-name>';
[string]$SubscriptionGuid_Get = '?'; # The script assigns this value, not you.

$SqlServerDnsAliasName = '<EDIT-any-unique-alias-name>';

$1ResourceGroupName = '<EDIT-rg-1>';  # Can be same or different than $2ResourceGroupName.
$1SqlServerName     = '<EDIT-sql-1>'; # Must differ from $2SqlServerName.

$2ResourceGroupName = '<EDIT-rg-2>';
$2SqlServerName     = '<EDIT-sql-2>';

# Login to your Azure subscription, first time per session.
Write-Host "You must log into Azure once per powershell_ise.exe session,";
Write-Host "  thus type 'yes' only the first time.";
Write-Host " ";
$yesno = Read-Host '[yes/no]  Do you need to log into Azure now?';
if ('yes' -eq $yesno)
{
    Connect-AzAccount -SubscriptionName $SubscriptionName;
}

$SubscriptionGuid_Get = Get-AzSubscription `
    -SubscriptionName $SubscriptionName;

################################################################
###    Working with DNS aliasing for Azure SQL DB server.    ###
################################################################

Write-Host '[1] Assign a DNS alias to SQL DB server 1.';
New-AzSqlServerDNSAlias `
    –ResourceGroupName  $1ResourceGroupName `
    -ServerName         $1SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;

Write-Host '[2] Get and list all the DNS aliases that are assigned to SQL DB server 1.';
Get-AzSqlServerDNSAlias `
    –ResourceGroupName $1ResourceGroupName `
    -ServerName        $1SqlServerName;

Write-Host '[3] Move the DNS alias from 1 to SQL DB server 2.';
Set-AzSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -NewServerName      $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName `
    -OldServerResourceGroup  $1ResourceGroupName `
    -OldServerName           $1SqlServerName `
    -OldServerSubscriptionId $SubscriptionGuid_Get;

# Here your client, such as SSMS, can connect to your "$2SqlServerName"
# by using "$SqlServerDnsAliasName" in the server name.
# For example, server:  "any-unique-alias-name.database.windows.net".

# Remove-AzSqlServerDNSAlias  - would fail here for SQL DB server 1.

Write-Host '[4] Remove the DNS alias from SQL DB server 2.';
Remove-AzSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -ServerName         $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;
```

### <a name="actual-console-output-from-the-powershell-example"></a>Werkelijke console-uitvoer van het Power shell-voor beeld

De volgende console-uitvoer is gekopieerd en geplakt vanuit een daad werkelijke uitvoering.

```powershell
You must log into Azure once per powershell_ise.exe session,
  thus type 'yes' only the first time.

[yes/no]  Do you need to log into Azure now?: yes


Environment           : AzureCloud
Account               : gm@acorporation.com
TenantId              : 72f988bf-1111-1111-1111-111111111111
SubscriptionId        : 45651c69-2222-2222-2222-222222222222
SubscriptionName      : mysubscriptionname
CurrentStorageAccount :

[1] Assign a DNS alias to SQL DB server 1.
[2] Get the DNS alias that is assigned to SQL DB server 1.
[3] Move the DNS alias from 1 to SQL DB server 2.
[4] Remove the DNS alias from SQL DB server 2.
ResourceGroupName ServerName         ServerDNSAliasName
----------------- ----------         ------------------
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-2       gm-sqldb-dns-2     unique-alias-name-food


[C:\windows\system32\]
>>
```

## <a name="next-steps"></a>Volgende stappen

Zie [DNS-alias voor Azure SQL database][dns-alias-overview-37v]voor een volledige uitleg van de functie voor de DNS-alias voor SQL database.

<!-- Article links. -->

[https://azure.microsoft.com/free/]: https://azure.microsoft.com/free/

[install-Az-ps-84p]: https://docs.microsoft.com/powershell/azure/install-az-ps

[dns-alias-overview-37v]: dns-alias-overview.md
