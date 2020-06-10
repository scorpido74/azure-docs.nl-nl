---
title: 'Zelf studie: toevoegen aan een groep met automatische failover'
titleSuffix: Azure SQL Managed Instance
description: In deze zelf studie maakt u twee Azure SQL Managed instances als primair en secundair en voegt u deze vervolgens toe aan een groep voor automatische failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 08/27/2019
ms.openlocfilehash: 31dba12023643f96018d1192111a19c80d0ba3ef
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636203"
---
# <a name="tutorial-add-a-sql-managed-instance-to-a-failover-group"></a>Zelf studie: een door SQL beheerd exemplaar toevoegen aan een failovergroep
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Voeg een door Azure SQL beheerd exemplaar toe aan een failovergroep. In dit artikel leert u het volgende:

> [!div class="checklist"]
> - Een primair SQL-beheerd exemplaar maken
> - Maak een secundair SQL Managed instance als onderdeel van een [failovergroep](../database/auto-failover-group-overview.md). 
> - Testfailover

  > [!NOTE]
  > - Wanneer u deze zelf studie doorloopt, moet u ervoor zorgen dat u uw resources configureert met de [vereisten voor het instellen van failover-groepen voor SQL Managed instance](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets). 
  > - Het maken van een SQL Managed instance kan een aanzienlijke hoeveelheid tijd in beslag nemen. Als gevolg hiervan kan het enkele uren duren voordat deze zelf studie is voltooid. Zie [SQL Managed instance Management Operations](sql-managed-instance-paas-overview.md#management-operations)(Engelstalig) voor meer informatie over het inrichten van tijden. 
  > - Voor SQL Managed instances die deel nemen aan een failovergroep zijn [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) of twee verbonden VPN-gateways vereist. Deze zelf studie bevat stappen voor het maken en koppelen van de VPN-gateways. Sla deze stappen over als u ExpressRoute al hebt geconfigureerd. 


## <a name="prerequisites"></a>Vereisten

# <a name="portal"></a>[Portal](#tab/azure-portal)
Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien: 

- Een Azure-abonnement. [Maak een gratis account](https://azure.microsoft.com/free/) als u er nog geen hebt.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Zorg ervoor dat u over de volgende items beschikt om de zelf studie te volt ooien:

- Een Azure-abonnement. [Maak een gratis account](https://azure.microsoft.com/free/) als u er nog geen hebt.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)

---


## <a name="1---create-resource-group-and-primary-sql-mi"></a>1-Maak een resource groep en primaire SQL MI

In deze stap maakt u de resource groep en het primaire SQL Managed instance voor uw failover-groep met behulp van de Azure Portal of Power shell. 


# <a name="portal"></a>[Portal](#tab/azure-portal) 

Maak de resource groep en uw primaire SQL beheerd exemplaar met behulp van de Azure Portal. 

1. Selecteer **Azure SQL** in het linkermenu van de Azure Portal. Als **Azure SQL** niet voor komt in de lijst, selecteert u **alle services**en typt u `Azure SQL` in het zoekvak. Beschrijving Selecteer de ster naast **Azure SQL** om deze te favoriet en voeg deze toe als een item in de linkernavigatiebalk. 
1. Selecteer **+ toevoegen** om de **optie pagina SQL-implementatie selecteren** te openen. U kunt aanvullende informatie over de verschillende data bases weer geven door details weer geven te selecteren op de tegel data bases.
1. Selecteer **maken** in de tegel **SQL Managed instances** . 

    ![SQL Managed instance selecteren](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. Op de pagina **Azure SQL Managed instance maken** op het tabblad **basis beginselen**
    1. Selecteer uw **abonnement** in de vervolg keuzelijst onder **Project Details**en kies vervolgens nieuwe resource groep **maken** . Typ een naam voor de resource groep, zoals `myResourceGroup` . 
    1. Geef onder **Details van SQL Managed instance**de naam op van uw SQL Managed instance en de regio waar u uw SQL Managed instance wilt implementeren. De **berekening en opslag** op standaard waarden behouden. 
    1. Geef onder **Administrator-account**een beheerders aanmelding op, zoals `azureuser` en een complex beheerders wachtwoord. 

    ![Primaire MI maken](./media/failover-group-add-instance-tutorial/primary-sql-mi-values.png)

1. Laat de overige instellingen op de standaard waarden staan en selecteer **controleren + maken** om de instellingen van uw SQL Managed instance te controleren. 
1. Selecteer **maken** om uw primaire SQL Managed instance te maken. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Maak de resource groep en het primaire SQL-beheerde exemplaar met behulp van Power shell. 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for your SQL Managed Instance
   $resourceGroupName = "myResourceGroup-$randomIdentifier"
   $location = "eastus"
   $drLocation = "eastus2"
   
   # Set the networking values for your primary SQL Managed Instance
   $primaryVNet = "primaryVNet-$randomIdentifier"
   $primaryAddressPrefix = "10.0.0.0/16"
   $primaryDefaultSubnet = "primaryDefaultSubnet-$randomIdentifier"
   $primaryDefaultSubnetAddress = "10.0.0.0/24"
   $primaryMiSubnetName = "primaryMISubnet-$randomIdentifier"
   $primaryMiSubnetAddress = "10.0.0.0/24"
   $primaryMiGwSubnetAddress = "10.0.255.0/27"
   $primaryGWName = "primaryGateway-$randomIdentifier"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   $primaryGWConnection = $primaryGWName + "-connection"
   
   
   # Set the networking values for your secondary SQL Managed Instance
   $secondaryVNet = "secondaryVNet-$randomIdentifier"
   $secondaryAddressPrefix = "10.128.0.0/16"
   $secondaryDefaultSubnet = "secondaryDefaultSubnet-$randomIdentifier"
   $secondaryDefaultSubnetAddress = "10.128.0.0/24"
   $secondaryMiSubnetName = "secondaryMISubnet-$randomIdentifier"
   $secondaryMiSubnetAddress = "10.128.0.0/24"
   $secondaryMiGwSubnetAddress = "10.128.255.0/27"
   $secondaryGWName = "secondaryGateway-$randomIdentifier"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   $secondaryGWConnection = $secondaryGWName + "-connection"
   
   
   
   # Set the SQL Managed Instance name for the new SQL Managed Instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for your SQL Managed Instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the SQL Managed Instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server licence that can be used for AHB discount
   
   # Set failover group details
   $vpnSharedKey = "mi1mi2psk"
   $failoverGroupName = "failovergroup-$randomIdentifier"
   
   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Password is" $secpasswd
   Write-host "Primary Virtual Network name is" $primaryVNet
   Write-host "Primary default subnet name is" $primaryDefaultSubnet
   Write-host "Primary SQL Managed Instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary SQL Managed Instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary SQL Managed Instance name is" $primaryInstance
   Write-host "Secondary SQL Managed Instance name is" $secondaryInstance
   Write-host "Failover group name is" $failoverGroupName
   
   # Suppress networking breaking changes warning (https://aka.ms/azps-changewarnings
   Set-Item Env:\SuppressAzurePowerShellBreakingChangeWarnings "true"
   
   # Set subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure primary virtual network
   Write-host "Creating primary virtual network..."
   $primaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -Name $primaryVNet `
                         -AddressPrefix $primaryAddressPrefix
   
                     Add-AzVirtualNetworkSubnetConfig `
                         -Name $primaryMiSubnetName `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                     | Set-AzVirtualNetwork
   $primaryVirtualNetwork
   
   
   # Configure primary MI subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure route table management service
   Write-host "Configuring primary MI route table management service..."
   
   $primaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'primaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryRouteTableMiManagementService
   
   # Configure the primary network security group
   Write-host "Configuring primary network security group..."
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -Name $primaryMiSubnetName `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NetworkSecurityGroup $primaryNSGMiManagementService `
                         -RouteTable $primaryRouteTableMiManagementService | `
                       Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryNSGMiManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $PrimaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $PrimaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   Write-host "Primary network security group configured successfully."
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "primaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Primary network route table configured successfully."
   
   
   # Create primary SQL Managed Instance
   
   Write-host "Creating primary SQL Managed Instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $primaryInstance `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -SubnetId $primaryMiSubnetConfigId `
                         -AdministratorCredential $mycreds `
                         -StorageSizeInGB $maxStorage `
                         -VCore $vCores `
                         -Edition $edition `
                         -ComputeGeneration $computeGeneration `
                         -LicenseType $license
   Write-host "Primary SQL Managed Instance created successfully."
   ```

In dit gedeelte van de zelf studie worden de volgende Power shell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een Azure-resourcegroep.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Hiermee maakt u een virtueel netwerk.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnet configuratie toe aan een virtueel netwerk. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hiermee haalt u een virtueel netwerk op in een resourcegroep. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hiermee wordt een subnet in een virtueel netwerk opgehaald. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Hiermee maakt u een netwerkbeveiligingsgroep. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Hiermee maakt u een route tabel. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Hiermee wordt een subnet-configuratie voor een virtueel netwerk bijgewerkt.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Hiermee wordt een virtueel netwerk bijgewerkt.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hiermee wordt een netwerk beveiligings groep opgehaald. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hiermee voegt u een configuratie van een netwerk beveiligings regel toe aan een netwerk beveiligings groep. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Hiermee wordt een netwerk beveiligings groep bijgewerkt.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Hiermee voegt u een route naar een route tabel toe. |
| [Get-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Hiermee werkt u een route tabel bij.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Hiermee maakt u een beheerd exemplaar van Azure SQL.  |

---

## <a name="2---create-secondary-virtual-network"></a>2-secundair virtueel netwerk maken

Als u de Azure Portal gebruikt om uw SQL Managed instance te maken, moet u het virtuele netwerk afzonderlijk maken, omdat er een vereiste is dat het subnet van het primaire en het secundaire SQL Managed-exemplaar geen overlappende bereiken heeft. Als u Power shell gebruikt om uw SQL Managed instance te configureren, gaat u verder met stap 3. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Voer de volgende stappen uit om het subnet-bereik van het primaire virtuele netwerk te controleren:

1. Navigeer in het [Azure Portal](https://portal.azure.com)naar de resource groep en selecteer het virtuele netwerk voor uw primaire exemplaar.  
2. Selecteer **subnetten** onder **instellingen** en noteer het **adres bereik**. Het adres bereik van het subnet van het virtuele netwerk voor het secundaire SQL Managed instance kan dit niet overlappen. 


   ![Primair subnet](./media/failover-group-add-instance-tutorial/verify-primary-subnet-range.png)

Voer de volgende stappen uit om een virtueel netwerk te maken:

1. Selecteer in de [Azure Portal](https://portal.azure.com) **een resource maken** en zoek naar *virtueel netwerk*. 
1. Selecteer de **Virtual Network** -optie die door micro soft is gepubliceerd en selecteer vervolgens **maken** op de volgende pagina. 
1. Vul de vereiste velden in om het virtuele netwerk voor uw secundaire SQL Managed instance te configureren en selecteer vervolgens **maken**. 

   In de volgende tabel ziet u de waarden die nodig zijn voor het secundaire virtuele netwerk:

    | **Veld** | Waarde |
    | --- | --- |
    | **Naam** |  De naam van het virtuele netwerk dat moet worden gebruikt door het secundaire SQL-beheerde exemplaar, zoals `vnet-sql-mi-secondary` . |
    | **Adres ruimte** | De adres ruimte voor het virtuele netwerk, zoals `10.128.0.0/16` . | 
    | **Abonnement** | Het abonnement waar uw primaire SQL beheerde instantie en resource groep zich bevinden. |
    | **Regio** | De locatie waar u uw secundaire SQL Managed instance gaat implementeren. |
    | **Subnetrouter** | De naam voor het subnet. `default`is standaard voor u bestemd. |
    | **Adresbereik**| Het adres bereik voor uw subnet. Dit moet verschillen van het adres bereik van het subnet dat wordt gebruikt door het virtuele netwerk van uw primaire SQL beheerde instantie, zoals `10.128.0.0/24` .  |
    | &nbsp; | &nbsp; |

    ![Secundaire waarden van virtueel netwerk](./media/failover-group-add-instance-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Deze stap is alleen nodig als u de Azure Portal gebruikt voor het implementeren van uw SQL Managed instance. Ga verder met stap 3 als u Power shell gebruikt. 

---

## <a name="3---create-a-secondary-sql-managed-instance"></a>3-een secundair SQL-beheerd exemplaar maken
In deze stap maakt u een secundair SQL-beheerd exemplaar in het Azure Portal, waarmee ook de netwerken tussen de twee door SQL beheerde instanties worden geconfigureerd. 

Uw tweede SQL Managed instance moet:
- Leeg zijn. 
- Hebben een ander subnet en IP-bereik dan het primaire SQL-beheerde exemplaar. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Maak het secundaire SQL-beheerde exemplaar met behulp van de Azure Portal. 

1. Selecteer **Azure SQL** in het linkermenu van de Azure Portal. Als **Azure SQL** niet voor komt in de lijst, selecteert u **alle services**en typt u vervolgens Azure SQL in het zoekvak. Beschrijving Selecteer de ster naast **Azure SQL** om deze te favoriet en voeg deze toe als een item in de linkernavigatiebalk. 
1. Selecteer **+ toevoegen** om de **optie pagina SQL-implementatie selecteren** te openen. U kunt aanvullende informatie over de verschillende data bases weer geven door details weer geven te selecteren op de tegel data bases.
1. Selecteer **maken** in de tegel **SQL Managed instances** . 

    ![SQL Managed instance selecteren](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. Vul op het tabblad **basis** van de pagina **Azure SQL Managed instance maken** de vereiste velden in voor het configureren van uw secundaire SQL Managed instance. 

   In de volgende tabel ziet u de waarden die nodig zijn voor het secundaire SQL Managed instance:
 
    | **Veld** | Waarde |
    | --- | --- |
    | **Abonnement** |  Het abonnement waarin uw primaire door SQL beheerde exemplaar is. |
    | **Resourcegroep**| De resource groep waar uw primaire SQL Managed instance zich bevindt. |
    | **Naam van door SQL beheerd exemplaar** | De naam van het nieuwe, secundaire SQL-beheerde exemplaar, zoals`sql-mi-secondary`  | 
    | **Regio**| De locatie voor uw secundaire SQL-beheerde exemplaar.  |
    | **Aanmelding van SQL Managed instance-beheerder** | De aanmelding die u wilt gebruiken voor uw nieuwe secundaire SQL-beheerde exemplaar, zoals `azureuser` . |
    | **Wachtwoord** | Een complex wacht woord dat wordt gebruikt door de beheerders aanmelding voor het nieuwe secundaire SQL-beheerde exemplaar.  |
    | &nbsp; | &nbsp; |

1. Selecteer op het tabblad **netwerken** , voor de **Virtual Network**, het virtuele netwerk dat u hebt gemaakt voor het secundaire SQL-beheerde exemplaar uit de vervolg keuzelijst.

   ![Secundaire MI-netwerken](./media/failover-group-add-instance-tutorial/networking-settings-for-secondary-mi.png)

1. Klik op het tabblad **extra instellingen** voor **geo-replicatie**op **Ja** als u een _secundaire failover wilt gebruiken_. Selecteer het primaire beheerde exemplaar van SQL in de vervolg keuzelijst. 
    1. Zorg ervoor dat de sortering en tijd zone overeenkomen met die van het primaire SQL Managed instance. Het primaire SQL beheerde exemplaar dat in deze zelf studie wordt gemaakt, gebruikt de standaard waarde voor `SQL_Latin1_General_CP1_CI_AS` sortering en `(UTC) Coordinated Universal Time` tijd zone. 

   ![Secundaire MI-netwerken](./media/failover-group-add-instance-tutorial/secondary-mi-failover.png)

1. Selecteer **controleren + maken** om de instellingen voor uw secundaire door SQL beheerd exemplaar te controleren. 
1. Selecteer **maken** om uw secundaire SQL Managed instance te maken. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Maak het secundaire SQL-beheerde exemplaar met behulp van Power shell. 

   ```powershell-interactive
   # Configure secondary virtual network
   Write-host "Configuring secondary virtual network..."
   
   $SecondaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $drlocation `
                         -Name $secondaryVNet `
                         -AddressPrefix $secondaryAddressPrefix
   
   Add-AzVirtualNetworkSubnetConfig `
                         -Name $secondaryMiSubnetName `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -AddressPrefix $secondaryMiSubnetAddress `
                       | Set-AzVirtualNetwork
   $SecondaryVirtualNetwork
   
   # Configure secondary SQL Managed Instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure secondary route table MI management service
   Write-host "Configuring secondary route table MI management service..."
   
   $secondaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'secondaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryRouteTableMiManagementService
   
   # Configure the secondary network security group
   Write-host "Configuring secondary network security group..."
   
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -Name $secondaryMiSubnetName `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NetworkSecurityGroup $secondaryNSGMiManagementService `
                         -RouteTable $secondaryRouteTableMiManagementService `
                       | Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryToMIManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $secondaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $secondaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "secondaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Secondary network security group configured successfully."
   
   # Create secondary SQL Managed Instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary SQL Managed Instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $secondaryInstance `
                     -ResourceGroupName $resourceGroupName `
                     -Location $drLocation `
                     -SubnetId $secondaryMiSubnetConfigId `
                     -AdministratorCredential $mycreds `
                     -StorageSizeInGB $maxStorage `
                     -VCore $vCores `
                     -Edition $edition `
                     -ComputeGeneration $computeGeneration `
                     -LicenseType $license `
                     -DnsZonePartner $primaryManagedInstanceId.Id
   Write-host "Secondary SQL Managed Instance created successfully."
   ```

In dit gedeelte van de zelf studie worden de volgende Power shell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een Azure-resourcegroep.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Hiermee maakt u een virtueel netwerk.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnet configuratie toe aan een virtueel netwerk. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hiermee haalt u een virtueel netwerk op in een resourcegroep. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hiermee wordt een subnet in een virtueel netwerk opgehaald. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Hiermee maakt u een netwerkbeveiligingsgroep. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Hiermee maakt u een route tabel. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Hiermee wordt een subnet-configuratie voor een virtueel netwerk bijgewerkt.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Hiermee wordt een virtueel netwerk bijgewerkt.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hiermee wordt een netwerk beveiligings groep opgehaald. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hiermee voegt u een configuratie van een netwerk beveiligings regel toe aan een netwerk beveiligings groep. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Hiermee wordt een netwerk beveiligings groep bijgewerkt.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Hiermee voegt u een route naar een route tabel toe. |
| [Get-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Hiermee werkt u een route tabel bij.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Hiermee maakt u een beheerd exemplaar van Azure SQL.  |

---

## <a name="4---create-primary-gateway"></a>4-primaire gateway maken 

Voor twee SQL Managed instances om deel te nemen aan een failovergroep moet er ExpressRoute of een gateway zijn geconfigureerd tussen de virtuele netwerken van de twee SQL Managed instances om netwerk communicatie toe te staan. Als u ervoor kiest om [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) te configureren in plaats van twee VPN-gateways te verbinden, gaat u verder met [stap 7](#7---create-a-failover-group).  

In dit artikel worden de stappen beschreven voor het maken van de twee VPN-gateways en het verbinden ervan, maar u kunt door gaan met het maken van de failovergroep als u in plaats daarvan ExpressRoute hebt geconfigureerd. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Maak de gateway voor het virtuele netwerk van uw primaire SQL-beheerd exemplaar met behulp van de Azure Portal. 


1. Ga in het [Azure Portal](https://portal.azure.com)naar de resource groep en selecteer de **virtuele netwerk** resource voor uw primaire SQL Managed instance. 
1. Selecteer **subnetten** onder **instellingen** en selecteer vervolgens om een nieuw **Gateway-subnet**toe te voegen. De standaard waarden behouden. 

   ![Gateway toevoegen voor primair door SQL beheerd exemplaar](./media/failover-group-add-instance-tutorial/add-subnet-gateway-primary-vnet.png)

1. Nadat de subnet gateway is gemaakt, selecteert u **een resource maken** in het navigatie deel venster links en typt u `Virtual network gateway` in het zoekvak. Selecteer de bron van de **virtuele netwerk gateway** die is gepubliceerd door **micro soft**. 

   ![Een nieuwe gateway voor het virtuele netwerk maken](./media/failover-group-add-instance-tutorial/create-virtual-network-gateway.png)

1. Vul de vereiste velden in om de gateway te configureren voor uw primaire SQL Managed instance. 

   In de volgende tabel ziet u de waarden die nodig zijn voor de gateway voor het primaire SQL Managed instance:
 
    | **Veld** | Waarde |
    | --- | --- |
    | **Abonnement** |  Het abonnement waarin uw primaire door SQL beheerde exemplaar is. |
    | **Naam** | De naam van de gateway van uw virtuele netwerk, zoals `primary-mi-gateway` . | 
    | **Regio** | De regio waar uw primaire SQL Managed instance zich bevindt. |
    | **Gatewaytype** | Selecteer **VPN**. |
    | **VPN-type** | **Op route gebaseerd** selecteren |
    | **SKU**| De standaard waarde van wijzigen `VpnGw1` . |
    | **Locatie**| De locatie waar uw primaire SQL beheerde instantie en primair virtuele netwerk zich bevindt.   |
    | **Virtueel netwerk**| Selecteer het virtuele netwerk dat is gemaakt in sectie 2, zoals `vnet-sql-mi-primary` . |
    | **Openbaar IP-adres**| Selecteer **Nieuw maken**. |
    | **Naam openbaar IP-adres**| Voer een naam in voor uw IP-adres, zoals `primary-gateway-IP` . |
    | &nbsp; | &nbsp; |

1. Wijzig de andere waarden als standaard en selecteer vervolgens **controleren + maken** om de instellingen voor de gateway van uw virtuele netwerk te controleren.

   ![Primaire gateway-instellingen](./media/failover-group-add-instance-tutorial/settings-for-primary-gateway.png)

1. Selecteer **maken** om de nieuwe virtuele netwerk gateway te maken. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Maak de gateway voor het virtuele netwerk van uw primaire SQL beheerd exemplaar met behulp van Power shell. 

   ```powershell-interactive
   # Create primary gateway
   Write-host "Adding GatewaySubnet to primary VNet..."
   Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $primaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $primaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName
   $primaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $primaryVirtualNetwork
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $primaryGWPublicIP = New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $location -AllocationMethod Dynamic
   $primaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -Subnet $primaryGatewaySubnet -PublicIpAddress $primaryGWPublicIP
   
   $primaryGateway = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $resourceGroupName `
       -Location $location -IpConfigurations $primaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $primaryGateway
   ```

In dit gedeelte van de zelf studie worden de volgende Power shell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hiermee haalt u een virtueel netwerk op in een resourcegroep. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnet configuratie toe aan een virtueel netwerk. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Hiermee wordt een virtueel netwerk bijgewerkt.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hiermee wordt een subnet in een virtueel netwerk opgehaald. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Hiermee maakt u een openbaar IP-adres.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Hiermee maakt u een IP-configuratie voor een Virtual Network gateway |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Hiermee maakt u een Virtual Network gateway |


---


## <a name="5---create-secondary-gateway"></a>5-secundaire gateway maken 
In deze stap maakt u de gateway voor het virtuele netwerk van uw secundaire SQL-beheerd exemplaar met behulp van de Azure Portal, 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Herhaal de stappen in de vorige sectie om het subnet van het virtuele netwerk en de gateway voor het secundaire SQL-beheerde exemplaar te maken met behulp van de Azure Portal. Vul de vereiste velden in om de gateway te configureren voor uw secundaire SQL Managed instance. 

   In de volgende tabel ziet u de waarden die nodig zijn voor de gateway voor het secundaire SQL Managed instance:

   | **Veld** | Waarde |
   | --- | --- |
   | **Abonnement** |  Het abonnement waarin uw secundaire SQL Managed instance zich bevindt. |
   | **Naam** | De naam van de gateway van uw virtuele netwerk, zoals `secondary-mi-gateway` . | 
   | **Regio** | De regio waar uw secundaire SQL Managed instance zich bevindt. |
   | **Gatewaytype** | Selecteer **VPN**. |
   | **VPN-type** | **Op route gebaseerd** selecteren |
   | **SKU**| De standaard waarde van wijzigen `VpnGw1` . |
   | **Locatie**| De locatie waar uw secundaire SQL Managed instance en secundaire virtuele netwerk zich bevindt.   |
   | **Virtueel netwerk**| Selecteer het virtuele netwerk dat is gemaakt in sectie 2, zoals `vnet-sql-mi-secondary` . |
   | **Openbaar IP-adres**| Selecteer **Nieuw maken**. |
   | **Naam openbaar IP-adres**| Voer een naam in voor uw IP-adres, zoals `secondary-gateway-IP` . |
   | &nbsp; | &nbsp; |

   ![Instellingen van secundaire gateway](./media/failover-group-add-instance-tutorial/settings-for-secondary-gateway.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Maak de gateway voor het virtuele netwerk van het secundaire SQL-beheerde exemplaar met behulp van Power shell. 

   ```powershell-interactive
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   
   Write-host "Adding GatewaySubnet to secondary VNet..."
   Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $secondaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $secondaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName
   $secondaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $secondaryVirtualNetwork
   $drLocation = $secondaryVirtualNetwork.Location
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $secondaryGWPublicIP = New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $drLocation -AllocationMethod Dynamic
   $secondaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -Subnet $secondaryGatewaySubnet -PublicIpAddress $secondaryGWPublicIP
   
   $secondaryGateway = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $resourceGroupName `
       -Location $drLocation -IpConfigurations $secondaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   $secondaryGateway
   ```

In dit gedeelte van de zelf studie worden de volgende Power shell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hiermee haalt u een virtueel netwerk op in een resourcegroep. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnet configuratie toe aan een virtueel netwerk. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Hiermee wordt een virtueel netwerk bijgewerkt.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hiermee wordt een subnet in een virtueel netwerk opgehaald. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Hiermee maakt u een openbaar IP-adres.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Hiermee maakt u een IP-configuratie voor een Virtual Network gateway |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Hiermee maakt u een Virtual Network gateway |

---


## <a name="6---connect-the-gateways"></a>6-de gateways verbinden
In deze stap maakt u een bidirectionele verbinding tussen de twee gateways van de twee virtuele netwerken. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Verbind de twee gateways met behulp van de Azure Portal. 


1. Selecteer **een resource maken** op basis van de [Azure Portal](https://portal.azure.com).
1. Typ `connection` in het zoekvak en druk vervolgens op ENTER om te zoeken. Hiermee gaat u naar de **verbindings** bron, gepubliceerd door micro soft.
1. Selecteer **maken** om uw verbinding te maken. 
1. Selecteer op het tabblad **basis beginselen** de volgende waarden en selecteer vervolgens **OK**. 
    1. Selecteer `VNet-to-VNet` voor het **verbindings type**. 
    1. Selecteer uw abonnement in de vervolgkeuzelijst. 
    1. Selecteer de resource groep voor uw SQL Managed instance in de vervolg keuzelijst. 
    1. Selecteer de locatie van uw primaire SQL-beheerde exemplaar in de vervolg keuzelijst 
1. Op het tabblad **instellingen** selecteert of voert u de volgende waarden in en selecteert u **OK**:
    1. Kies de primaire netwerk gateway voor de **eerste virtuele netwerk gateway**, zoals `Primary-Gateway` .  
    1. Kies de secundaire netwerk gateway voor de **tweede virtuele netwerk gateway**, zoals `Secondary-Gateway` . 
    1. Schakel het selectie vakje in naast **bidirectionele connectiviteit instellen**. 
    1. Wijzig de standaard naam van de primaire verbinding of geef deze de gewenste waarde. 
    1. Geef een **gedeelde sleutel (PSK)** op voor de verbinding, zoals `mi1m2psk` . 

   ![Gateway verbinding maken](./media/failover-group-add-instance-tutorial/create-gateway-connection.png)

1. Controleer op het tabblad **samen vatting** de instellingen voor uw bidirectionele verbinding en selecteer vervolgens **OK** om de verbinding te maken. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verbind de twee gateways met behulp van Power shell. 

   ```powershell-interactive
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway to secondary gateway..."
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $primaryGateway -VirtualNetworkGateway2 $secondaryGateway -Location $location `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway to primary gateway..."
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $secondaryGateway -VirtualNetworkGateway2 $primaryGateway -Location $drLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

In dit gedeelte van de zelf studie wordt gebruikgemaakt van de volgende Power shell-cmdlet:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Hiermee maakt u een verbinding tussen de twee virtuele netwerk gateways.   |

---


## <a name="7---create-a-failover-group"></a>7-een failovergroep maken
In deze stap maakt u de failovergroep en voegt u beide SQL Managed instances toe aan de groep. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Maak de failover-groep met behulp van de Azure Portal. 


1. Selecteer **Azure SQL** in het linkermenu van de [Azure Portal](https://portal.azure.com). Als **Azure SQL** niet voor komt in de lijst, selecteert u **alle services**en typt u vervolgens Azure SQL in het zoekvak. Beschrijving Selecteer de ster naast **Azure SQL** om deze te favoriet en voeg deze toe als een item in de linkernavigatiebalk. 
1. Selecteer het primaire SQL-beheerde exemplaar dat u in de eerste sectie hebt gemaakt, zoals `sql-mi-primary` . 
1. Ga onder **instellingen**naar **failover-groepen voor instanties** en kies vervolgens **groep toevoegen** om de pagina **failover-groep voor instanties** te openen. 

   ![Een failovergroep toevoegen](./media/failover-group-add-instance-tutorial/add-failover-group.png)

1. Typ op de pagina **failovergroep voor instanties** de naam van de failovergroep, bijvoorbeeld `failovergrouptutorial` en kies vervolgens het secundaire SQL-beheerde exemplaar, zoals `sql-mi-secondary` in de vervolg keuzelijst. Selecteer **maken** om uw failovergroep te maken. 

   ![Failovergroep maken](./media/failover-group-add-instance-tutorial/create-failover-group.png)

1. Zodra de implementatie van de failovergroep is voltooid, wordt u teruggeleid naar de pagina **failover-groep** . 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Maak de failover-groep met behulp van Power shell. 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

In dit gedeelte van de zelf studie wordt gebruikgemaakt van de volgende Power shell-cmdlet:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Hiermee maakt u een nieuwe failovergroep voor Azure SQL Managed instance.  |


---


## <a name="8---test-failover"></a>8-testfailover
In deze stap wordt uw failover-groep overschreven naar de secundaire server en wordt er een failback uitgevoerd met behulp van de Azure Portal. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Testfailover met behulp van de Azure Portal. 


1. Navigeer naar uw _secundaire_ SQL Managed instance binnen de [Azure Portal](https://portal.azure.com) en selecteer **failover-groepen voor instanties** onder instellingen. 
1. Controleer welk SQL Managed instance het primaire is en welk SQL Managed instance het secundaire exemplaar is. 
1. Selecteer **failover** en selecteer vervolgens **Ja** in de waarschuwing over TDS-sessies die worden losgekoppeld. 

   ![Failover-groep](./media/failover-group-add-instance-tutorial/failover-mi-failover-group.png)

1. Controleer welk SQL Managed instance het primaire exemplaar is en welk SQL Managed instance het secundaire exemplaar is. Als failover is geslaagd, moeten de twee exemplaren geschakelde rollen hebben. 

   ![SQL Managed instances heeft rollen geswitcheerd na een failover](./media/failover-group-add-instance-tutorial/mi-switched-after-failover.png)

1. Ga naar het nieuwe _tweede_ beheerde exemplaar van SQL en selecteer **failover** eenmaal opnieuw om de primaire instantie weer naar de primaire rol te laten mislukken. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Testfailover met behulp van Power shell. 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Failover the primary SQL Managed Instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


Herstel de failovergroep terug naar de primaire server:

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail primary SQL Managed Instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

In dit gedeelte van de zelf studie worden de volgende Power shell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Hiermee worden failover-groepen voor SQL Managed instance opgehaald of weer gegeven.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Hiermee wordt een failover uitgevoerd van een failovergroep voor een SQL-beheerd exemplaar. | 

---



## <a name="clean-up-resources"></a>Resources opschonen
U kunt resources opschonen door eerst het SQL Managed instance, het virtuele cluster, vervolgens de resterende resources en ten slotte de resource groep te verwijderen. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Navigeer naar uw resource groep in de [Azure Portal](https://portal.azure.com). 
1. Selecteer de SQL Managed instance (s) en selecteer vervolgens **verwijderen**. Typ `yes` in het tekstvak om te bevestigen dat u de resource wilt verwijderen en selecteer vervolgens **verwijderen**. Dit proces kan enige tijd in beslag nemen en totdat het is voltooid, kunt u het *virtuele cluster* of andere afhankelijke resources niet verwijderen. Controleer het verwijderen op het tabblad activiteit om te bevestigen dat uw SQL Managed instance is verwijderd. 
1. Wanneer het beheerde exemplaar van SQL wordt verwijderd, verwijdert u het *virtuele cluster* door het te selecteren in de resource groep en vervolgens **verwijderen**te kiezen. Typ `yes` in het tekstvak om te bevestigen dat u de resource wilt verwijderen en selecteer vervolgens **verwijderen**. 
1. Alle resterende resources verwijderen. Typ `yes` in het tekstvak om te bevestigen dat u de resource wilt verwijderen en selecteer vervolgens **verwijderen**. 
1. Verwijder de resource groep door **resource groep verwijderen**te selecteren, typ de naam van de resource groep `myResourceGroup` en selecteer vervolgens **verwijderen**. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

U moet de resource groep twee keer verwijderen. Als u de resource groep de eerste keer verwijdert, worden de SQL Managed instance en Virtual clusters verwijderd, maar wordt het fout bericht vervolgens niet meer weer gegeven `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.` . Voer de opdracht Remove-AzResourceGroup een tweede keer uit om eventuele resterende resources en de resource groep te verwijderen.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing SQL Managed Instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resouce group..."
```

In dit gedeelte van de zelf studie wordt gebruikgemaakt van de volgende Power shell-cmdlet:

| Opdracht | Opmerkingen |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resource groep. |

---

## <a name="full-script"></a>Volledige script

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add SQL Managed Instance to a failover group")]

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een Azure-resourcegroep.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Hiermee maakt u een virtueel netwerk.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnet configuratie toe aan een virtueel netwerk. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hiermee haalt u een virtueel netwerk op in een resourcegroep. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hiermee wordt een subnet in een virtueel netwerk opgehaald. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Hiermee maakt u een netwerkbeveiligingsgroep. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Hiermee maakt u een route tabel. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Hiermee wordt een subnet-configuratie voor een virtueel netwerk bijgewerkt.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Hiermee wordt een virtueel netwerk bijgewerkt.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hiermee wordt een netwerk beveiligings groep opgehaald. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hiermee voegt u een configuratie van een netwerk beveiligings regel toe aan een netwerk beveiligings groep. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Hiermee wordt een netwerk beveiligings groep bijgewerkt.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Hiermee voegt u een route naar een route tabel toe. |
| [Get-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Hiermee werkt u een route tabel bij.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Hiermee maakt u een beheerd exemplaar van Azure SQL.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Retourneert informatie over Azure SQL Managed data base instance. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Hiermee maakt u een openbaar IP-adres.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Hiermee maakt u een IP-configuratie voor een Virtual Network gateway |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Hiermee maakt u een Virtual Network gateway |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Hiermee maakt u een verbinding tussen de twee virtuele netwerk gateways.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Hiermee maakt u een nieuwe failovergroep voor Azure SQL Managed instance.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Hiermee worden failover-groepen voor SQL Managed instance opgehaald of weer gegeven.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Hiermee wordt een failover uitgevoerd van een failovergroep voor een SQL-beheerd exemplaar. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resource groep. | 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Er zijn geen scripts beschikbaar voor de Azure Portal.

---

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een failovergroep geconfigureerd tussen twee SQL Managed instances. U hebt geleerd hoe u:

> [!div class="checklist"]
> - Een primair SQL-beheerd exemplaar maken
> - Maak een secundair SQL Managed instance als onderdeel van een [failovergroep](../database/auto-failover-group-overview.md). 
> - Testfailover

Ga naar de volgende Snelstartgids over het maken van verbinding met uw SQL Managed instance en het herstellen van een Data Base naar uw SQL Managed instance: 

> [!div class="nextstepaction"]
> [Verbinding maken met uw SQL Managed instance](connect-vm-instance-configure.md) 
>  [Een Data Base herstellen naar een beheerd exemplaar van SQL](restore-sample-database-quickstart.md)


