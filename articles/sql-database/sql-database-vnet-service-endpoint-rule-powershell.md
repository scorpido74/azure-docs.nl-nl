---
title: Power shell voor VNet-eind punten en-regels voor één en gepoolde data bases
description: Voorziet in Power shell-scripts voor het maken en beheren van virtuele service-eind punten voor uw Azure SQL Database en Azure Synapse.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80124692"
---
# <a name="powershell--create-a-virtual-service-endpoint-and-vnet-rule-for-sql"></a>Power shell: een virtueel service-eind punt en een VNet-regel voor SQL maken

*Regels voor virtuele netwerken* zijn één firewall beveiligings functie waarmee wordt bepaald of de database server voor uw afzonderlijke data bases en elastische Pools in azure [SQL database](sql-database-technical-overview.md) of voor uw data bases in azure [Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) communicatie accepteert die wordt verzonden vanuit bepaalde subnetten in virtuele netwerken.

> [!IMPORTANT]
> Dit artikel is van toepassing op Azure SQL Server en op zowel SQL Database als data warehouse in azure Synapse die zijn gemaakt op de Azure SQL-Server. SQL Database wordt gebruikt bij het verwijzen naar SQL Database en Azure Synapse voor eenvoud. Dit artikel is *niet* van toepassing op de implementatie van een **beheerd exemplaar** in Azure SQL database omdat er geen service-eind punt aan is gekoppeld.

In dit artikel vindt u een Power shell-script dat de volgende acties onderneemt:

1. Hiermee maakt u een Microsoft Azure *virtueel service-eind punt* in uw subnet.
2. Hiermee voegt u het eind punt toe aan de firewall van uw Azure SQL Database-Server om een regel voor het *virtuele netwerk*te maken.

Uw motivaties voor het maken van een regel worden uitgelegd in: [virtuele service-eind punten voor Azure SQL database][sql-db-vnet-service-endpoint-rule-overview-735r].

> [!TIP]
> Als alles u nodig hebt om de naam van het virtuele-service-eindpunt *type* voor SQL database aan uw subnet te evalueren of toe te voegen, kunt u verder gaan naar ons meer [Direct Power shell-script](#a-verify-subnet-is-endpoint-ps-100).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek.

## <a name="major-cmdlets"></a>Primaire cmdlets

Dit artikel benadrukt de cmdlet **New-AzSqlServerVirtualNetworkRule** waarmee het subnet-eind punt wordt toegevoegd aan de toegangs beheer lijst (ACL) van uw Azure SQL database-server, waardoor een regel wordt gemaakt.

In de volgende lijst ziet u de volg orde van andere *belang rijke* cmdlets die u moet uitvoeren om u voor te bereiden op het aanroepen van **New-AzSqlServerVirtualNetworkRule**. In dit artikel vinden deze aanroepen plaats in [script 3 "virtuele netwerk regel"](#a-script-30):

1. [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig): Hiermee wordt een subnetobject gemaakt.
2. [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork): maakt uw virtuele netwerk, waardoor het het subnet.
3. [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig): wijst een virtueel service-eind punt toe aan uw subnet.
4. [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetwork): persistente updates die zijn aangebracht in uw virtuele netwerk.
5. [New-AzSqlServerVirtualNetworkRule](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlservervirtualnetworkrule): nadat het subnet een eind punt is, wordt uw subnet als een regel voor het virtuele netwerk toegevoegd aan de ACL van uw Azure SQL database-server.
   - Deze cmdlet biedt de para meter **-IgnoreMissingVNetServiceEndpoint**, te beginnen met de module versie 5.1.1 van Azure RM Power shell.

## <a name="prerequisites-for-running-powershell"></a>Vereisten voor het uitvoeren van Power shell

- U kunt zich al aanmelden bij Azure, zoals via de [Azure Portal][http-azure-portal-link-ref-477t].
- U kunt al Power shell-scripts uitvoeren.

> [!NOTE]
> Zorg ervoor dat service-eind punten zijn ingeschakeld voor het VNet/subnet dat u wilt toevoegen aan uw server, anders mislukt het maken van de VNet-firewall regel.

## <a name="one-script-divided-into-four-chunks"></a>Eén script onderverdeeld in vier segmenten

Het Power shell-script voor demonstraties is onderverdeeld in een reeks kleinere scripts. De afdeling vereenvoudigt het leren en biedt flexibiliteit. De scripts moeten in hun aangegeven volg orde worden uitgevoerd. Als u nu geen tijd hebt om de scripts uit te voeren, wordt de daad werkelijke test uitvoer weer gegeven na het script 4.

<a name="a-script-10" />

### <a name="script-1-variables"></a>Script 1: variabelen

Met dit eerste Power shell-script worden waarden toegewezen aan variabelen. De volgende scripts zijn afhankelijk van deze variabelen.

> [!IMPORTANT]
> Voordat u dit script uitvoert, kunt u de waarden als u wilt bewerken. Als u al een resource groep hebt, kunt u bijvoorbeeld de naam van de resource groep bewerken als de toegewezen waarde.
>
> De naam van uw abonnement moet worden bewerkt in het script.

### <a name="powershell-script-1-source-code"></a>Power shell-script 1-bron code

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

### <a name="script-2-prerequisites"></a>Script 2: vereisten

Met dit script wordt het volgende script voor bereid, waarbij de eindpunt actie is. Met dit script maakt u de volgende items in de lijst, maar alleen als ze nog niet bestaan. U kunt script 2 overs Laan als u zeker weet dat deze items al bestaan:

- Azure-resourcegroep
- Azure SQL Database Server

### <a name="powershell-script-2-source-code"></a>Bron code voor Power shell-script 2

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

## <a name="script-3-create-an-endpoint-and-a-rule"></a>Script 3: een eind punt en een regel maken

Met dit script maakt u een virtueel netwerk met een subnet. Vervolgens wijst het script het type **micro soft. SQL** -eind punt toe aan uw subnet. Ten slotte voegt het script uw subnet toe aan de toegangs beheer lijst (ACL) van uw SQL Database-Server, waardoor een regel wordt gemaakt.

### <a name="powershell-script-3-source-code"></a>Power shell-script 3-bron code

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

## <a name="script-4-clean-up"></a>Script 4: opschonen

Met dit laatste script verwijdert u de resources die de vorige scripts hebben gemaakt voor de demonstratie. Het script vraagt echter om bevestiging voordat het volgende wordt verwijderd:

- Azure SQL Database Server
- Azure-resource groep

U kunt script 4 telkens uitvoeren nadat het script 1 is voltooid.

### <a name="powershell-script-4-source-code"></a>Power shell-script 4-bron code

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

## <a name="verify-your-subnet-is-an-endpoint"></a>Controleren of uw subnet een eind punt is

Mogelijk hebt u een subnet dat al is toegewezen aan de naam van het **micro soft. SQL** -type, wat betekent dat het al een virtueel service-eind punt is. U kunt de [Azure Portal][http-azure-portal-link-ref-477t] gebruiken om een regel voor een virtueel netwerk te maken op basis van het eind punt.

U kunt er ook voor zorgen dat u niet zeker weet of uw subnet de naam van het **micro soft. SQL** -type heeft. U kunt het volgende Power shell-script uitvoeren om de volgende acties uit te voeren:

1. Controleer of het subnet de naam van het **micro soft. SQL** -type heeft.
2. U kunt desgewenst de naam van het type toewijzen als deze niet aanwezig is.
    - Het script vraagt u om te *bevestigen*voordat de naam van het ontbrekende type wordt toegepast.

### <a name="phases-of-the-script"></a>Fasen van het script

Dit zijn de fasen van het Power shell-script:

1. Meld u aan bij uw Azure-account en u hebt slechts één keer per PS-sessie nodig.  Variabelen toewijzen.
2. Zoek naar uw virtuele netwerk en vervolgens naar uw subnet.
3. Is het subnet gelabeld als **micro soft. SQL** -eindpunt server type?
4. Voeg in uw subnet een virtueel service-eind punt van de type naam **micro soft. SQL**toe.

> [!IMPORTANT]
> Voordat u dit script uitvoert, moet u de waarden bewerken die zijn toegewezen aan de $-variabelen, aan de bovenkant van het script.

### <a name="direct-powershell-source-code"></a>Directe Power shell-bron code

Met dit Power shell-script wordt niets bijgewerkt, tenzij u Ja antwoordt als u wordt gevraagd om een bevestiging. Met het script kan de type naam **micro soft. SQL** worden toegevoegd aan uw subnet. Maar het script probeert de add alleen toe als uw subnet de type naam niet heeft.

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
