---
title: 'Zelf studie: een door SQL beheerd exemplaar toevoegen aan een failovergroep'
titleSuffix: Azure SQL Managed Instance
description: In deze zelf studie leert u hoe u een failovergroep maakt tussen een primair en secundair Azure SQL-beheerd exemplaar.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
ms.date: 08/27/2019
ms.openlocfilehash: a6f400db2a068953080e734148e024b575e3e1e9
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89070850"
---
# <a name="tutorial-add-sql-managed-instance-to-a-failover-group"></a>Zelf studie: een door SQL beheerd exemplaar toevoegen aan een failovergroep
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Beheerde exemplaren van een Azure SQL Managed instance toevoegen aan een failovergroep. In dit artikel leert u het volgende:

> [!div class="checklist"]
> - Maak een primair beheerd exemplaar.
> - Maak een secundair beheerd exemplaar als onderdeel van een [failovergroep](../database/auto-failover-group-overview.md). 
> - Testfailover.

  > [!NOTE]
  > - Wanneer u deze zelf studie doorloopt, moet u ervoor zorgen dat u uw resources configureert met de [vereisten voor het instellen van failover-groepen voor SQL Managed instance](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets). 
  > - Het maken van een beheerd exemplaar kan een aanzienlijke hoeveelheid tijd in beslag nemen. Als gevolg hiervan kan het enkele uren duren voordat deze zelf studie is voltooid. Zie [SQL Managed instance Management Operations](sql-managed-instance-paas-overview.md#management-operations)(Engelstalig) voor meer informatie over het inrichten van tijden. 
  > - Voor beheerde instanties die deel uitmaken van een failovergroep zijn [Azure ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) of twee verbonden VPN-gateways vereist. Globale VNet-peering wordt niet ondersteund. Deze zelf studie bevat stappen voor het maken en koppelen van de VPN-gateways. Sla deze stappen over als u ExpressRoute al hebt geconfigureerd. 


## <a name="prerequisites"></a>Vereisten

# <a name="portal"></a>[Portal](#tab/azure-portal)
Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien: 

- Een Azure-abonnement. [Maak een gratis account](https://azure.microsoft.com/free/) als u er nog geen hebt.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Zorg ervoor dat u over de volgende items beschikt om de zelf studie te volt ooien:

- Een Azure-abonnement. [Maak een gratis account](https://azure.microsoft.com/free/) als u er nog geen hebt.
- [Azure PowerShell](/powershell/azure/)

---


## <a name="create-a-resource-group-and-primary-managed-instance"></a>Een resource groep en primair beheerd exemplaar maken

In deze stap maakt u de resource groep en het primaire beheerde exemplaar voor uw failover-groep met behulp van de Azure Portal of Power shell. 


# <a name="portal"></a>[Portal](#tab/azure-portal) 

Maak de resource groep en het primaire beheerde exemplaar met behulp van de Azure Portal. 

1. Selecteer **Azure SQL** in het menu aan de linkerzijde van de Azure Portal. Als **Azure SQL** niet voor komt in de lijst, selecteert u **alle services**en typt u `Azure SQL` in het zoekvak. Beschrijving Selecteer de ster naast **Azure SQL** om deze te favoriet en voeg deze toe als een item in de linkernavigatiebalk. 
1. Selecteer **+ toevoegen** om de **optie pagina SQL-implementatie selecteren** te openen. U kunt aanvullende informatie over de verschillende data bases weer geven door **Details weer geven** te selecteren op de tegel **data bases** .
1. Selecteer **maken** in de tegel **SQL Managed instances** . 

    ![SQL Managed instance selecteren](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. Op de pagina **Azure SQL Managed instance maken** op het tabblad **basis beginselen** :
    1. Selecteer uw **abonnement** in de vervolg keuzelijst onder **Project Details**en kies vervolgens nieuwe resource groep **maken** . Typ een naam voor de resource groep, zoals `myResourceGroup` . 
    1. Geef onder **Details van SQL Managed instance**de naam op van uw beheerde exemplaar en de regio waar u uw beheerde exemplaar wilt implementeren. De **berekening en opslag** op standaard waarden laten staan. 
    1. Geef onder **Administrator-account**een beheerders aanmelding op, zoals `azureuser` en een complex beheerders wachtwoord. 

    ![Primair beheerd exemplaar maken](./media/failover-group-add-instance-tutorial/primary-sql-mi-values.png)

1. Laat de overige instellingen op de standaard waarden staan en selecteer **controleren + maken** om de instellingen van uw SQL Managed instance te controleren. 
1. Selecteer **maken** om uw primaire beheerde instantie te maken. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Maak de resource groep en het primaire beheerde exemplaar met behulp van Power shell. 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for SQL Managed Instance
   $resourceGroupName = "myResourceGroup-$randomIdentifier"
   $location = "eastus"
   $drLocation = "eastus2"
   
   # Set the networking values for your primary managed instance
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
   
   
   # Set the networking values for your secondary managed instance
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
   
   
   
   # Set the SQL Managed Instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for SQL Managed Instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the SQL Managed Instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server license that can be used for AHB discount
   
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
   
   # Set the subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create the resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure the primary virtual network
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
   
   
   # Configure the primary managed instance subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure the network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure the route table management service
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
   
   
   # Create the primary managed instance
   
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
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie aan een virtueel netwerk toe. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hiermee haalt u een virtueel netwerk op in een resourcegroep. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hiermee haalt u een subnet in een virtueel netwerk op. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Hiermee maakt u een netwerkbeveiligingsgroep. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Hiermee maakt u een routetabel. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie aan een virtueel netwerk toe.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Hiermee werkt u een virtueel netwerk bij.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hiermee haalt u een netwerkbeveiligingsgroep op. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hiermee voegt u de configuratie van een netwerkbeveiligingsregel toe aan een netwerkbeveiligingsgroep. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Hiermee werkt u een netwerkbeveiligingsgroep bij.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Hiermee voegt u een route toe aan een routetabel. |
| [Get-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Hiermee werkt u een routetabel bij.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Hiermee maakt u een beheerd exemplaar.  |

---

## <a name="create-secondary-virtual-network"></a>Secundair virtueel netwerk maken

Als u de Azure Portal gebruikt om uw beheerde exemplaar te maken, moet u het virtuele netwerk afzonderlijk maken, omdat er een vereiste is dat het subnet van het primaire en het secundaire beheerde exemplaar geen overlappende bereiken hebben. Als u Power shell gebruikt om uw beheerde exemplaar te configureren, gaat u verder met stap 3. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Voer de volgende stappen uit om het subnet-bereik van het primaire virtuele netwerk te controleren:

1. Navigeer in het [Azure Portal](https://portal.azure.com)naar de resource groep en selecteer het virtuele netwerk voor uw primaire exemplaar.  
2. Selecteer **subnetten** onder **instellingen** en noteer het **adres bereik**. Het adres bereik van het subnet van het virtuele netwerk voor het secundaire beheerde exemplaar kan dit niet overlappen. 


   ![Primair subnet](./media/failover-group-add-instance-tutorial/verify-primary-subnet-range.png)

Voer de volgende stappen uit om een virtueel netwerk te maken:

1. Selecteer in de [Azure Portal](https://portal.azure.com) **een resource maken** en zoek naar *virtueel netwerk*. 
1. Selecteer de **Virtual Network** -optie die door micro soft is gepubliceerd en selecteer vervolgens **maken** op de volgende pagina. 
1. Vul de vereiste velden in om het virtuele netwerk voor uw secundaire beheerde exemplaar te configureren en selecteer vervolgens **maken**. 

   In de volgende tabel ziet u de waarden die nodig zijn voor het secundaire virtuele netwerk:

    | **Veld** | Waarde |
    | --- | --- |
    | **Naam** |  De naam van het virtuele netwerk dat moet worden gebruikt door het secundaire beheerde exemplaar, zoals `vnet-sql-mi-secondary` . |
    | **Adresruimte** | De adres ruimte voor het virtuele netwerk, zoals `10.128.0.0/16` . | 
    | **Abonnement** | Het abonnement waar uw primaire beheerde instantie en resource groep zich bevinden. |
    | **Regio** | De locatie waar u uw secundaire beheerde exemplaar gaat implementeren. |
    | **Subnet** | De naam voor het subnet. `default` is standaard voor u bestemd. |
    | **Adresbereik**| Het adres bereik voor uw subnet. Dit moet verschillen van het adres bereik van het subnet dat wordt gebruikt door het virtuele netwerk van uw primaire beheerde instantie, zoals `10.128.0.0/24` .  |
    | &nbsp; | &nbsp; |

    ![Secundaire waarden van virtueel netwerk](./media/failover-group-add-instance-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Deze stap is alleen nodig als u de Azure Portal gebruikt voor het implementeren van een door SQL beheerd exemplaar. Ga verder met stap 3 als u Power shell gebruikt. 

---

## <a name="create-a-secondary-managed-instance"></a>Een secundair beheerd exemplaar maken
In deze stap maakt u een tweede beheerde instantie in de Azure Portal, waarmee ook de netwerken tussen de twee beheerde instanties worden geconfigureerd. 

Uw tweede beheerde exemplaar moet:
- Leeg zijn. 
- Hebben een ander subnet en IP-bereik dan het primaire beheerde exemplaar. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Maak het secundaire beheerde exemplaar met behulp van de Azure Portal. 

1. Selecteer **Azure SQL** in het menu aan de linkerzijde van de Azure Portal. Als **Azure SQL** niet voor komt in de lijst, selecteert u **alle services**en typt u `Azure SQL` in het zoekvak. Beschrijving Selecteer de ster naast **Azure SQL** om deze te favoriet en voeg deze toe als een item in de linkernavigatiebalk. 
1. Selecteer **+ toevoegen** om de **optie pagina SQL-implementatie selecteren** te openen. U kunt aanvullende informatie over de verschillende data bases weer geven door **Details weer geven** te selecteren op de tegel **data bases** .
1. Selecteer **maken** in de tegel **SQL Managed instances** . 

    ![SQL Managed instance selecteren](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. Vul op het tabblad **basis** van de pagina **Azure SQL Managed instance maken** de vereiste velden in voor het configureren van uw secundaire beheerde exemplaar. 

   In de volgende tabel ziet u de waarden die nodig zijn voor het secundaire beheerde exemplaar:
 
    | **Veld** | Waarde |
    | --- | --- |
    | **Abonnement** |  Het abonnement waar uw primaire beheerde instantie zich bevindt. |
    | **Resourcegroep**| De resource groep waar uw primaire beheerde instantie zich bevindt. |
    | **Naam van door SQL beheerd exemplaar** | De naam van het nieuwe, secundaire beheerde exemplaar, zoals `sql-mi-secondary` .  | 
    | **Regio**| De locatie voor uw secundaire beheerde exemplaar.  |
    | **Aanmelding van SQL Managed instance-beheerder** | De aanmelding die u wilt gebruiken voor uw nieuwe, secundaire beheerde exemplaar, zoals `azureuser` . |
    | **Wachtwoord** | Een complex wacht woord dat wordt gebruikt door de beheerders aanmelding voor het nieuwe secundaire beheerde exemplaar.  |
    | &nbsp; | &nbsp; |

1. Selecteer op het tabblad **netwerken** , voor de **Virtual Network**, het virtuele netwerk dat u hebt gemaakt voor het secundaire beheerde exemplaar in de vervolg keuzelijst.

   ![Secundaire MI-netwerken](./media/failover-group-add-instance-tutorial/networking-settings-for-secondary-mi.png)

1. Klik op het tabblad **extra instellingen** , voor **geo-replicatie**, op **Ja** als u een _secundaire failover wilt gebruiken_. Selecteer de primaire beheerde instantie in de vervolg keuzelijst. 
    
   Zorg ervoor dat de sortering en tijd zone overeenkomen met die van het primaire beheerde exemplaar. Het primaire beheerde exemplaar dat in deze zelf studie wordt gemaakt, gebruikt de standaard waarde voor `SQL_Latin1_General_CP1_CI_AS` sortering en `(UTC) Coordinated Universal Time` tijd zone. 

   ![Secundaire beheerde instantie netwerken](./media/failover-group-add-instance-tutorial/secondary-mi-failover.png)

1. Selecteer **controleren + maken** om de instellingen voor uw secundaire beheerde exemplaar te controleren. 
1. Selecteer **maken** om uw secundaire beheerde instantie te maken. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Maak het secundaire beheerde exemplaar met behulp van Power shell. 

   ```powershell-interactive
   # Configure the secondary virtual network
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
   
   # Configure the secondary managed instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure the secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure the secondary route table MI management service
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
   
   # Create the secondary managed instance
   
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
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie aan een virtueel netwerk toe. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hiermee haalt u een virtueel netwerk op in een resourcegroep. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hiermee haalt u een subnet in een virtueel netwerk op. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Hiermee maakt u een netwerkbeveiligingsgroep. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Hiermee maakt u een routetabel. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie aan een virtueel netwerk toe.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Hiermee werkt u een virtueel netwerk bij.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hiermee haalt u een netwerkbeveiligingsgroep op. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hiermee voegt u de configuratie van een netwerkbeveiligingsregel toe aan een netwerkbeveiligingsgroep. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Hiermee werkt u een netwerkbeveiligingsgroep bij.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Hiermee voegt u een route toe aan een routetabel. |
| [Get-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Hiermee werkt u een routetabel bij.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Hiermee maakt u een beheerd exemplaar.  |

---

## <a name="create-a-primary-gateway"></a>Een primaire gateway maken 

Voor twee beheerde instanties om deel te nemen aan een failovergroep moet er een ExpressRoute of een gateway zijn geconfigureerd tussen de virtuele netwerken van de twee beheerde instanties om netwerk communicatie toe te staan. Als u ervoor kiest om [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) te configureren in plaats van twee VPN-gateways te verbinden, gaat u verder met [stap 7](#create-a-failover-group).  

In dit artikel worden de stappen beschreven voor het maken van de twee VPN-gateways en het verbinden ervan, maar u kunt door gaan met het maken van de failovergroep als u in plaats daarvan ExpressRoute hebt geconfigureerd. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Maak de gateway voor het virtuele netwerk van uw primaire beheerde exemplaar met behulp van de Azure Portal. 


1. Ga in het [Azure Portal](https://portal.azure.com)naar de resource groep en selecteer de **virtuele netwerk** resource voor uw primaire beheerde exemplaar. 
1. Selecteer **subnetten** onder **instellingen** en selecteer vervolgens om een nieuw **Gateway-subnet**toe te voegen. De standaard waarden behouden. 

   ![Gateway toevoegen voor primair beheerde exemplaar](./media/failover-group-add-instance-tutorial/add-subnet-gateway-primary-vnet.png)

1. Nadat de subnet gateway is gemaakt, selecteert u **een resource maken** in het navigatie deel venster links en typt u `Virtual network gateway` in het zoekvak. Selecteer de bron van de **virtuele netwerk gateway** die is gepubliceerd door **micro soft**. 

   ![Een nieuwe gateway voor het virtuele netwerk maken](./media/failover-group-add-instance-tutorial/create-virtual-network-gateway.png)

1. Vul de vereiste velden in om de gateway te configureren voor uw primaire beheerde exemplaar. 

   In de volgende tabel ziet u de waarden die nodig zijn voor de gateway voor het primaire beheerde exemplaar:
 
    | **Veld** | Waarde |
    | --- | --- |
    | **Abonnement** |  Het abonnement waar uw primaire beheerde instantie zich bevindt. |
    | **Name** | De naam van de gateway van uw virtuele netwerk, zoals `primary-mi-gateway` . | 
    | **Regio** | De regio waar uw primaire beheerde instantie zich bevindt. |
    | **Gatewaytype** | Selecteer **VPN**. |
    | **VPN-type** | Selecteer **Op route gebaseerd**. |
    | **SKU**| De standaard waarde van wijzigen `VpnGw1` . |
    | **Virtueel netwerk**| Selecteer het virtuele netwerk dat is gemaakt in sectie 2, zoals `vnet-sql-mi-primary` . |
    | **Openbaar IP-adres**| Selecteer **Nieuw maken**. |
    | **Naam openbaar IP-adres**| Voer een naam in voor uw IP-adres, zoals `primary-gateway-IP` . |
    | &nbsp; | &nbsp; |

1. Wijzig de andere waarden als standaard en selecteer vervolgens **controleren + maken** om de instellingen voor de gateway van uw virtuele netwerk te controleren.

   ![Primaire gateway-instellingen](./media/failover-group-add-instance-tutorial/settings-for-primary-gateway.png)

1. Selecteer **maken** om de nieuwe virtuele netwerk gateway te maken. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Maak de gateway voor het virtuele netwerk van uw primaire beheerde exemplaar met behulp van Power shell. 

   ```powershell-interactive
   # Create the primary gateway
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
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie aan een virtueel netwerk toe. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Hiermee werkt u een virtueel netwerk bij.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hiermee haalt u een subnet in een virtueel netwerk op. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Hiermee maakt u een openbaar IP-adres.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Hiermee maakt u een IP-configuratie voor een virtuele netwerk gateway. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Hiermee maakt u een virtuele netwerk gateway. |


---


## <a name="create-secondary-gateway"></a>Secundaire gateway maken 
In deze stap maakt u de gateway voor het virtuele netwerk van uw secundaire beheerde exemplaar met behulp van de Azure Portal. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Herhaal de stappen in de vorige sectie om het subnet van het virtuele netwerk en de gateway voor het secundaire beheerde exemplaar te maken met behulp van de Azure Portal. Vul de vereiste velden in om de gateway te configureren voor uw secundaire beheerde exemplaar. 

   In de volgende tabel ziet u de waarden die nodig zijn voor de gateway voor het secundaire beheerde exemplaar:

   | **Veld** | Waarde |
   | --- | --- |
   | **Abonnement** |  Het abonnement waar uw secundaire beheerde instantie zich bevindt. |
   | **Name** | De naam van de gateway van uw virtuele netwerk, zoals `secondary-mi-gateway` . | 
   | **Regio** | De regio waar uw secundaire beheerde instantie zich bevindt. |
   | **Gatewaytype** | Selecteer **VPN**. |
   | **VPN-type** | Selecteer **Op route gebaseerd**. |
   | **SKU**| De standaard waarde van wijzigen `VpnGw1` . |
   | **Virtueel netwerk**| Selecteer het virtuele netwerk voor het secundaire beheerde exemplaar, zoals `vnet-sql-mi-secondary` . |
   | **Openbaar IP-adres**| Selecteer **Nieuw maken**. |
   | **Naam openbaar IP-adres**| Voer een naam in voor uw IP-adres, zoals `secondary-gateway-IP` . |
   | &nbsp; | &nbsp; |

   ![Instellingen van secundaire gateway](./media/failover-group-add-instance-tutorial/settings-for-secondary-gateway.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Maak de gateway voor het virtuele netwerk van het secundaire beheerde exemplaar met behulp van Power shell. 

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
   
   Write-host "Creating secondary gateway..."
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
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie aan een virtueel netwerk toe. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Hiermee werkt u een virtueel netwerk bij.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hiermee haalt u een subnet in een virtueel netwerk op. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Hiermee maakt u een openbaar IP-adres.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Hiermee maakt u een IP-configuratie voor een virtuele netwerk gateway. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Hiermee maakt u een virtuele netwerk gateway. |

---


## <a name="connect-the-gateways"></a>De gateways verbinden
In deze stap maakt u een bidirectionele verbinding tussen de twee gateways van de twee virtuele netwerken. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Verbind de twee gateways met behulp van de Azure Portal. 


1. Selecteer **een resource maken** op basis van de [Azure Portal](https://portal.azure.com).
1. Typ `connection` in het zoekvak en druk vervolgens op ENTER om te zoeken. Hiermee gaat u naar de **verbindings** bron, gepubliceerd door micro soft.
1. Selecteer **maken** om uw verbinding te maken. 
1. Selecteer op de pagina **basis beginselen** de volgende waarden en selecteer vervolgens **OK**. 
    1. Selecteer `VNet-to-VNet` voor het **verbindings type**. 
    1. Selecteer uw abonnement in de vervolgkeuzelijst. 
    1. Selecteer de resource groep voor SQL Managed instance in de vervolg keuzelijst. 
    1. Selecteer de locatie van uw primaire beheerde instantie in de vervolg keuzelijst. 
1. Selecteer op de pagina **instellingen** de volgende waarden of voer deze in en selecteer **OK**:
    1. Kies de primaire netwerk gateway voor de **eerste virtuele netwerk gateway**, zoals `primaryGateway` .  
    1. Kies de secundaire netwerk gateway voor de **tweede virtuele netwerk gateway**, zoals `secondaryGateway` . 
    1. Schakel het selectie vakje in naast **bidirectionele connectiviteit instellen**. 
    1. Wijzig de standaard naam van de primaire verbinding of geef deze de gewenste waarde. 
    1. Geef een **gedeelde sleutel (PSK)** op voor de verbinding, zoals `mi1m2psk` . 
    1. Selecteer **OK** om uw wijzigingen op te slaan. 

    ![Gateway verbinding maken](./media/failover-group-add-instance-tutorial/create-gateway-connection.png)

    

1. Controleer op de pagina **controleren en maken** de instellingen voor uw bidirectionele verbinding en selecteer vervolgens **OK** om de verbinding te maken. 


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
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Hiermee maakt u een verbinding tussen de twee virtuele netwerkgateways.   |

---


## <a name="create-a-failover-group"></a>Een failovergroep maken
In deze stap maakt u de failovergroep en voegt u beide beheerde exemplaren toe aan de groep. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Maak de failover-groep met behulp van de Azure Portal. 


1. Selecteer **Azure SQL** in het linkermenu van de [Azure Portal](https://portal.azure.com). Als **Azure SQL** niet voor komt in de lijst, selecteert u **alle services**en typt u `Azure SQL` in het zoekvak. Beschrijving Selecteer de ster naast **Azure SQL** om deze te favoriet en voeg deze toe als een item in de linkernavigatiebalk. 
1. Selecteer de primaire beheerde instantie die u in de eerste sectie hebt gemaakt, zoals `sql-mi-primary` . 
1. Navigeer onder **instellingen**naar **failover-groepen voor instanties** en kies vervolgens **groep toevoegen** om de pagina **failover-groep voor instanties** te openen. 

   ![Een failovergroep toevoegen](./media/failover-group-add-instance-tutorial/add-failover-group.png)

1. Typ op de pagina **failovergroep voor instanties** de naam van de failovergroep, zoals `failovergrouptutorial` . Kies vervolgens het secundaire beheerde exemplaar, zoals `sql-mi-secondary` in de vervolg keuzelijst. Selecteer **maken** om uw failovergroep te maken. 

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
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Hiermee maakt u een nieuwe failovergroep voor Azure SQL Managed Instance.  |


---


## <a name="test-failover"></a>Testfailover
In deze stap wordt uw failover-groep overschreven naar de secundaire server en wordt er een failback uitgevoerd met behulp van de Azure Portal. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Testfailover met behulp van de Azure Portal. 


1. Navigeer naar uw _secundaire_ beheerde exemplaar binnen de [Azure Portal](https://portal.azure.com) en selecteer **failover-groepen voor instanties** onder instellingen. 
1. Controleer welk beheerde exemplaar het primaire is en welk Managed instance het secundaire exemplaar is. 
1. Selecteer **failover** en selecteer vervolgens **Ja** in de waarschuwing over TDS-sessies die worden losgekoppeld. 

   ![Failover-groep](./media/failover-group-add-instance-tutorial/failover-mi-failover-group.png)

1. Controleer welk beheerde exemplaar het primaire is en welk beheerde exemplaar het secundaire is. Als de failover is geslaagd, moeten de twee exemplaren geschakelde rollen hebben. 

   ![Beheerde instanties hebben geswitcheerde rollen na een failover](./media/failover-group-add-instance-tutorial/mi-switched-after-failover.png)

1. Ga naar het nieuwe _secundaire_ beheerde exemplaar en selecteer eenmalig **failover** opnieuw om de primaire instantie terug te laten werken naar de primaire rol. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Testfailover met behulp van Power shell. 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Fail over the primary managed instance to the secondary role
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
   
   # Fail the primary managed instance back to the primary role
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
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Hiermee haalt u failovergroepen voor Azure SQL Managed Instance op of vermeldt u deze.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Hiermee voert u een nieuwe failover uit voor een failovergroep voor SQL Managed Instance. | 

---



## <a name="clean-up-resources"></a>Resources opschonen
U kunt resources opschonen door eerst de beheerde instanties, het virtuele cluster, vervolgens de resterende resources en ten slotte de resource groep te verwijderen. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Navigeer in [Azure Portal](https://portal.azure.com) naar uw resourcegroep. 
1. Selecteer het beheerde exemplaar of de beheerde exemplaren en selecteer vervolgens **Verwijderen**. Typ `yes` in het tekstvak om te bevestigen dat u de resource wilt verwijderen en selecteer vervolgens **Verwijderen**. Het kan enige tijd duren voordat dit proces op de achtergrond is voltooid. Zolang het nog niet is voltooid, kunt u het *virtuele cluster* of andere afhankelijke resources niet verwijderen. Controleer de verwijdering op het tabblad **activiteit** om te bevestigen dat uw beheerde exemplaar is verwijderd. 
1. Wanneer het beheerde exemplaar is verwijderd, verwijdert u het *virtuele cluster* door dit te selecteren in uw resourcegroep en vervolgens **Verwijderen** te kiezen. Typ `yes` in het tekstvak om te bevestigen dat u de resource wilt verwijderen en selecteer vervolgens **Verwijderen**. 
1. Verwijder eventuele andere resources. Typ `yes` in het tekstvak om te bevestigen dat u de resource wilt verwijderen en selecteer vervolgens **Verwijderen**. 
1. Verwijder de resourcegroep door **Resourcegroep verwijderen** te selecteren, de naam van de resourcegroep, `myResourceGroup`, in te voeren en vervolgens **Verwijderen** te selecteren. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

U moet de resourcegroep twee keer verwijderen. Als u de resource groep de eerste keer verwijdert, worden de beheerde exemplaren en virtuele clusters verwijderd, maar wordt het fout bericht niet meer weer gegeven `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'` . Voer de opdracht Remove-AzResourceGroup een tweede keer uit om eventuele resterende resources en de resourcegroep te verwijderen.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing SQL Managed Instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resource group..."
```

In dit gedeelte van de zelf studie wordt gebruikgemaakt van de volgende Power shell-cmdlet:

| Opdracht | Opmerkingen |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep. |

---

## <a name="full-script"></a>Volledige script

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add SQL Managed Instance to a failover group")]

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een Azure-resourcegroep.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Hiermee maakt u een virtueel netwerk.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie aan een virtueel netwerk toe. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hiermee haalt u een virtueel netwerk op in een resourcegroep. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hiermee haalt u een subnet in een virtueel netwerk op. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Hiermee maakt u een netwerkbeveiligingsgroep. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Hiermee maakt u een routetabel. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie aan een virtueel netwerk toe.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Hiermee werkt u een virtueel netwerk bij.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hiermee haalt u een netwerkbeveiligingsgroep op. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hiermee voegt u de configuratie van een netwerkbeveiligingsregel toe aan een netwerkbeveiligingsgroep. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Hiermee werkt u een netwerkbeveiligingsgroep bij.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Hiermee voegt u een route toe aan een routetabel. |
| [Get-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Hiermee werkt u een routetabel bij.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Hiermee maakt u een beheerd exemplaar.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Hiermee retourneert u informatie over een met Azure SQL beheerd exemplaar. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Hiermee maakt u een openbaar IP-adres.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Hiermee maakt u een IP-configuratie voor een virtuele netwerk gateway. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Hiermee maakt u een virtuele netwerk gateway. |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Hiermee maakt u een verbinding tussen de twee virtuele netwerkgateways.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Hiermee maakt u een nieuwe failover-groep voor een SQL-beheerd exemplaar.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Hiermee haalt u failovergroepen voor Azure SQL Managed Instance op of vermeldt u deze.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Hiermee voert u een nieuwe failover uit voor een failovergroep voor SQL Managed Instance. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep. | 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Er zijn geen scripts beschikbaar voor de Azure Portal.

---

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een failovergroep geconfigureerd tussen twee beheerde exemplaren. U hebt geleerd hoe u:

> [!div class="checklist"]
> - Maak een primair beheerd exemplaar.
> - Maak een secundair beheerd exemplaar als onderdeel van een [failovergroep](../database/auto-failover-group-overview.md). 
> - Testfailover.

Ga naar de volgende Snelstartgids over het maken van verbinding met een SQL Managed instance en het herstellen van een Data Base naar een SQL Managed instance: 

> [!div class="nextstepaction"]
> [Verbinding maken met een SQL-beheerd exemplaar](connect-vm-instance-configure.md) 
>  [Een Data Base herstellen naar een beheerd exemplaar van SQL](restore-sample-database-quickstart.md)


