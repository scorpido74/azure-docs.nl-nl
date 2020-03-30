---
title: PowerShell voor VNet-eindpunten en -regels voor afzonderlijke en samengevoegde databases
description: Biedt PowerShell-scripts voor het maken en beheren van Virtual Service-eindpunten voor uw Azure SQL Database en Azure Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, vanto
ms.date: 03/12/2019
tags: azure-synapse
ms.openlocfilehash: 1e8ec394eab1df0aebe394b8acebc74c7ed49e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124692"
---
# <a name="powershell--create-a-virtual-service-endpoint-and-vnet-rule-for-sql"></a>PowerShell: een virtual service-eindpunt en VNet-regel voor SQL maken

*Virtuele netwerkregels* zijn een beveiligingsfunctie voor firewalls die bepaalt of de databaseserver voor uw enkele databases en elastische groep in Azure [SQL Database](sql-database-technical-overview.md) of voor uw databases in [Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) communicatie accepteert die vanuit bepaalde subnetten in virtuele netwerken worden verzonden.

> [!IMPORTANT]
> Dit artikel is van toepassing op Azure SQL-server en op zowel SQL Database als datawarehouse in Azure Synapse die zijn gemaakt op de Azure SQL-server. Voor de eenvoud wordt SQL Database gebruikt bij het verwijzen naar zowel SQL Database als Azure Synapse. Dit artikel is *niet* van toepassing op een **beheerde instantieimplementatie** in Azure SQL Database omdat er geen serviceeindpunt aan is gekoppeld.

In dit artikel wordt een PowerShell-script beschreven en uitgelegd dat de volgende acties onderneemt:

1. Hiermee maakt u een Microsoft Azure *Virtual Service-eindpunt* op uw subnet.
2. Hiermee voegt u het eindpunt toe aan de firewall van uw Azure SQL Database-server om een *virtuele netwerkregel*te maken.

Uw motivaties voor het maken van een regel worden uitgelegd in: [Virtual Service-eindpunten voor Azure SQL Database.][sql-db-vnet-service-endpoint-rule-overview-735r]

> [!TIP]
> Als u alleen de *typenaam* Virtual Service endpoint voor SQL Database aan uw subnet moet beoordelen of toevoegen, u doorgaan naar ons [meer directe PowerShell-script.](#a-verify-subnet-is-endpoint-ps-100)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek.

## <a name="major-cmdlets"></a>Grote cmdlets

In dit artikel wordt de cmdlet **Nieuw-AzSqlServerVirtualNetworkRule** benadrukt waarmee het subneteindpunt wordt toegevoegd aan de toegangscontrolelijst (ACL) van uw Azure SQL Database-server, waardoor een regel wordt gemaakt.

De volgende lijst toont de volgorde van andere *belangrijke* cmdlets die u moet uitvoeren om uw oproep naar **New-AzSqlServerVirtualNetworkRule**voor te bereiden. In dit artikel vinden deze aanroepen plaats in [script 3 "Virtuele netwerkregel":](#a-script-30)

1. [Nieuw-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig): Maakt een subnetobject.
2. [Nieuw-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork)Creëert uw virtuele netwerk, waardoor het subnet.
3. [Set-AzVirtualNetworkSubnetConfig:](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig)Wijst een Virtual Service-eindpunt toe aan uw subnet.
4. [Set-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetwork)Blijft updates voor uw virtuele netwerk uitvoeren.
5. [Nieuw-AzSqlServerVirtualNetworkRule:](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlservervirtualnetworkrule)Nadat uw subnet een eindpunt is, voegt u uw subnet als virtuele netwerkregel toe aan de ACL van uw Azure SQL Database-server.
   - Deze cmdlet biedt de parameter **-IgnoreMissingVNetServiceEndpoint**, te beginnen in Azure RM PowerShell Module versie 5.1.1.

## <a name="prerequisites-for-running-powershell"></a>Voorwaarden voor het uitvoeren van PowerShell

- U zich al aanmelden bij Azure, bijvoorbeeld via de [Azure-portal.][http-azure-portal-link-ref-477t]
- U PowerShell-scripts al uitvoeren.

> [!NOTE]
> Zorg ervoor dat serviceeindpunten zijn ingeschakeld voor het VNet/Subnet dat u aan uw server wilt toevoegen, anders mislukt het maken van de VNet Firewall-regel.

## <a name="one-script-divided-into-four-chunks"></a>Eén script verdeeld in vier segmenten

Onze demonstratie PowerShell script is verdeeld in een reeks van kleinere scripts. De divisie vereenvoudigt het leren en biedt flexibiliteit. De scripts moeten in de aangegeven volgorde worden uitgevoerd. Als u nu geen tijd hebt om de scripts uit te voeren, wordt onze werkelijke testuitvoer weergegeven na script 4.

<a name="a-script-10" />

### <a name="script-1-variables"></a>Script 1: Variabelen

Dit eerste PowerShell-script wijst waarden toe aan variabelen. De volgende scripts zijn afhankelijk van deze variabelen.

> [!IMPORTANT]
> Voordat u dit script uitvoert, u de waarden bewerken, als u dat wilt. Als u bijvoorbeeld al een resourcegroep hebt, u de naam van uw resourcegroep bewerken als de toegewezen waarde.
>
> Uw abonnementsnaam moet in het script worden bewerkt.

### <a name="powershell-script-1-source-code"></a>PowerShell script 1 broncode

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.
$SubscriptionName = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName $SubscriptionName;

$ResourceGroupName = 'RG-YourNameHere';
$Region            = 'westcentralus';

$VNetName            = 'myVNet';
$SubnetName          = 'mySubnet';
$VNetAddressPrefix   = '10.1.0.0/16';
$SubnetAddressPrefix = '10.1.1.0/24';
$VNetRuleName        = 'myFirstVNetRule-ForAcl';

$SqlDbServerName         = 'mysqldbserver-forvnet';
$SqlDbAdminLoginName     = 'ServerAdmin';
$SqlDbAdminLoginPassword = 'ChangeYourAdminPassword1';

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Official type name.

Write-Host 'Completed script 1, the "Variables".';
```

<a name="a-script-20" />

### <a name="script-2-prerequisites"></a>Script 2: Voorwaarden

Dit script bereidt zich voor op het volgende script, waar de endpoint-actie zich bevindt. Dit script maakt voor u de volgende vermelde items, maar alleen als ze nog niet bestaan. U script 2 overslaan als u zeker weet dat deze items al bestaan:

- Azure-resourcegroep
- Azure SQL Database-server

### <a name="powershell-script-2-source-code"></a>PowerShell script 2 broncode

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists.";

$gottenResourceGroup = $null;

$gottenResourceGroup = Get-AzResourceGroup `
  -Name        $ResourceGroupName `
  -ErrorAction SilentlyContinue;

if ($null -eq $gottenResourceGroup)
{
    Write-Host "Creating your missing Resource Group - $ResourceGroupName.";

    $gottenResourceGroup = New-AzResourceGroup `
      -Name $ResourceGroupName `
      -Location $Region;

    $gottenResourceGroup;
}
else { Write-Host "Good, your Resource Group already exists - $ResourceGroupName."; }

$gottenResourceGroup = $null;

###########################################################
## Ensure your Azure SQL Database server already exists. ##
###########################################################

Write-Host "Check whether your Azure SQL Database server already exists.";

$sqlDbServer = $null;

$sqlDbServer = Get-AzSqlServer `
  -ResourceGroupName $ResourceGroupName `
  -ServerName        $SqlDbServerName `
  -ErrorAction       SilentlyContinue;

if ($null -eq $sqlDbServer) {
    Write-Host "Creating the missing Azure SQL Database server - $SqlDbServerName.";
    Write-Host "Gather the credentials necessary to next create an Azure SQL Database server.";

    $sqlAdministratorCredentials = New-Object `
      -TypeName     System.Management.Automation.PSCredential `
      -ArgumentList `
        $SqlDbAdminLoginName, `
        $(ConvertTo-SecureString `
            -String      $SqlDbAdminLoginPassword `
            -AsPlainText `
            -Force `
         );

    if ($null -eq $sqlAdministratorCredentials) {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending.";
        return;
    }

    Write-Host "Create your Azure SQL Database server.";

    $sqlDbServer = New-AzSqlServer `
      -ResourceGroupName $ResourceGroupName `
      -ServerName        $SqlDbServerName `
      -Location          $Region `
      -SqlAdministratorCredentials $sqlAdministratorCredentials;

    $sqlDbServer;
}
else {
    Write-Host "Good, your Azure SQL Database server already exists - $SqlDbServerName."; 
}

$sqlAdministratorCredentials = $null;
$sqlDbServer                 = $null;

Write-Host 'Completed script 2, the "Prerequisites".';
```

<a name="a-script-30" />

## <a name="script-3-create-an-endpoint-and-a-rule"></a>Script 3: Een eindpunt en een regel maken

Dit script maakt een virtueel netwerk met een subnet. Vervolgens wijst het script het **Microsoft.Sql-eindpunttype** toe aan uw subnet. Ten slotte voegt het script uw subnet toe aan de toegangscontrolelijst (ACL) van uw SQL Database-server, waardoor een regel wordt gemaakt.

### <a name="powershell-script-3-source-code"></a>PowerShell script 3 broncode

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network.";

$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Create a virtual network '$VNetName'." `
  "  Give the subnet to the virtual network that we created.";

$vnet = New-AzVirtualNetwork `
  -Name              $VNetName `
  -AddressPrefix     $VNetAddressPrefix `
  -Subnet            $subnet `
  -ResourceGroupName $ResourceGroupName `
  -Location          $Region;

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.";

$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Persist the updates made to the virtual network > subnet.";

$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.";

$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnet.Id;

$vnetRuleObject1;

Write-Host "Verify that the rule is in the SQL DB ACL.";

$vnetRuleObject2 = Get-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName;

$vnetRuleObject2;

Write-Host 'Completed script 3, the "Virtual-Network-Rule".';
```

<a name="a-script-40" />

## <a name="script-4-clean-up"></a>Script 4: Opschonen

In dit laatste script worden de bronnen verwijderd die de vorige scripts voor de demonstratie hebben gemaakt. Het script vraagt echter om bevestiging voordat het het volgende verwijdert:

- Azure SQL Database-server
- Azure-brongroep

U script 4 op elk gewenst moment uitvoeren nadat script 1 is voltooid.

### <a name="powershell-script-4-source-code"></a>PowerShell script 4 broncode

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL DB ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL DB ACL.";

Remove-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -ErrorAction            SilentlyContinue;

Write-Host "Delete the endpoint from the subnet.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

Remove-AzVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Delete the virtual network (thus also deletes the subnet).";

Remove-AzVirtualNetwork `
  -Name              $VNetName `
  -ResourceGroupName $ResourceGroupName `
  -ErrorAction       SilentlyContinue;

###########################################################
##   Clean-up phase B:  Conditional deletes.             ##
##                                                       ##
##   These might have already existed, so user might     ##
##   want to keep.                                       ##
##                                                       ##
##   1. Azure SQL Database server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]';
if ('yes' -eq $yesno) {
    Write-Host "Remove the Azure SQL DB server.";

    Remove-AzSqlServer `
      -ServerName        $SqlDbServerName `
      -ResourceGroupName $ResourceGroupName `
      -ErrorAction       SilentlyContinue;

    Write-Host "Remove the Azure Resource Group.";

    Remove-AzResourceGroup `
      -Name        $ResourceGroupName `
      -ErrorAction SilentlyContinue;
}
else {
    Write-Host "Skipped over the DELETE of SQL Database and resource group.";
}

Write-Host 'Completed script 4, the "Clean-Up".';
```

<a name="a-actual-output" />

<a name="a-verify-subnet-is-endpoint-ps-100" />

## <a name="verify-your-subnet-is-an-endpoint"></a>Controleren of uw subnet een eindpunt is

Mogelijk hebt u een subnet dat al de **typenaam Microsoft.Sql** is toegewezen, wat betekent dat het al een eindpunt voor virtuele service is. U de [Azure-portal][http-azure-portal-link-ref-477t] gebruiken om een virtuele netwerkregel vanaf het eindpunt te maken.

U weet ook niet zeker of uw subnet de **microsoft.sql-typenaam** heeft. U het volgende PowerShell-script uitvoeren om deze acties uit te voeren:

1. Controleer of uw subnet de **Microsoft.Sql-typenaam** heeft.
2. Wijs de typenaam destijds toe als deze afwezig is.
    - Het script vraagt u om te *bevestigen,* voordat het de afwezige typenaam toepast.

### <a name="phases-of-the-script"></a>Fasen van het script

Dit zijn de fasen van het PowerShell-script:

1. Meld u aan bij uw Azure-account, die slechts één keer per PS-sessie nodig is.  Variabelen toewijzen.
2. Zoek naar uw virtuele netwerk en vervolgens naar uw subnet.
3. Is uw subnet gelabeld als **Microsoft.Sql** endpoint-servertype?
4. Voeg een Virtual Service-eindpunt van de typenaam **Microsoft.Sql toe**aan uw subnet.

> [!IMPORTANT]
> Voordat u dit script uitvoert, moet u de waarden bewerken die zijn toegewezen aan de $-variabelen, boven aan het script.

### <a name="direct-powershell-source-code"></a>Direct PowerShell-broncode

Dit PowerShell-script werkt niets bij, tenzij u ja antwoordt als u om bevestiging vraagt. Het script kan de typenaam **Microsoft.Sql** toevoegen aan uw subnet. Maar het script probeert de add alleen als uw subnet ontbreekt de typenaam.

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.
$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName  = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName "$SubscriptionName";

$ResourceGroupName   = 'yourRGName';
$VNetName            = 'yourVNetName';
$SubnetName          = 'yourSubnetName';
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.'; # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.
# Search for the virtual network.
$vnet = $null;
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

if ($vnet -eq $null) {
    Write-Host "Caution: No virtual network found by the name '$VNetName'.";
    Return;
}

$subnet = $null;
for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $subnet = $vnet.Subnets[$nn];
    if ($subnet.Name -eq $SubnetName)
    { break; }
    $subnet = $null;
}

if ($subnet -eq $null) {
    Write-Host "Caution: No subnet found by the name '$SubnetName'";
    Return;
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?
$endpointMsSql = $null;
for ($nn=0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++) {
    $endpointMsSql = $subnet.ServiceEndpoints[$nn];
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb) {
        $endpointMsSql;
        break;
    }
    $endpointMsSql = $null;
}

if ($endpointMsSql -ne $null) {
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";
    Return;
}
else {
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]';
    if ('no' -eq $yesno) { Return; }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.
$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

# Persist the subnet update.
$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $vnet.Subnets[0].ServiceEndpoints; }  # Display.
```

<!-- Link references: -->
[sql-db-vnet-service-endpoint-rule-overview-735r]: sql-database-vnet-service-endpoint-rule-overview.md
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
