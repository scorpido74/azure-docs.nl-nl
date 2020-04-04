---
title: 'Zelfstudie: Een beheerde instantie toevoegen aan een failovergroep'
description: Meer informatie over het configureren van een failovergroep voor uw Azure SQL Database-beheerde exemplaar.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 08/27/2019
ms.openlocfilehash: 0f1a56fa6ea38acd8061180407eb47fe416b61e9
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631704"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>Zelfstudie: Een SQL-databasebeheerexemplaar toevoegen aan een failovergroep

Een SQL Database beheerde instantie toevoegen aan een failovergroep. In dit artikel leert u hoe u:

> [!div class="checklist"]
> - Een primair beheerde instantie maken
> - Maak een secundairbeheerde instantie als onderdeel van een [failovergroep](sql-database-auto-failover-group.md). 
> - Testfailover

  > [!NOTE]
  > - Wanneer u deze zelfstudie doorloopt, moet u ervoor zorgen dat u uw resources configureert met de [vereisten voor het instellen van failovergroepen voor beheerde bijvoorbeeld.](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets) 
  > - Het maken van een beheerde instantie kan een aanzienlijke hoeveelheid tijd in beslag nemen. Als gevolg hiervan kan deze tutorial enkele uren in beslag nemen. Zie [beheervan beheervan beheervan beheerstijden voor](sql-database-managed-instance.md#managed-instance-management-operations)meer informatie over de inprovisioningstijden . 
  > - Beheerde exemplaren die deelnemen aan een failovergroep vereisen [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) of twee verbonden VPN-gateways. Deze zelfstudie bevat stappen voor het maken en verbinden van de VPN-gateways. Sla deze stappen over als u ExpressRoute al hebt geconfigureerd. 


## <a name="prerequisites"></a>Vereisten

# <a name="portal"></a>[Portal](#tab/azure-portal)
Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien: 

- Een Azure-abonnement. [Maak een gratis account](https://azure.microsoft.com/free/) aan als je nog geen account hebt.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Als u de zelfstudie wilt voltooien, controleert u of u de volgende items hebt:

- Een Azure-abonnement. [Maak een gratis account](https://azure.microsoft.com/free/) aan als je nog geen account hebt.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)

---


## <a name="1---create-resource-group-and-primary-managed-instance"></a>1 - Resourcegroep en primaire beheerde instantie maken
In deze stap maakt u de resourcegroep en het primaire beheerde exemplaar voor uw failovergroep met behulp van de Azure-portal of PowerShell. 


# <a name="portal"></a>[Portal](#tab/azure-portal) 

Maak de resourcegroep en uw primaire beheerde instantie met behulp van de Azure-portal. 

1. Selecteer **Azure SQL** in het linkermenu van de Azure-portal. Als **Azure SQL** niet in de lijst staat, selecteert u Alle **services**en typt u Azure SQL in het zoekvak. (Optioneel) Selecteer de ster naast **Azure SQL** om deze favoriet te maken en voeg deze toe als een item in de linkernavigatie. 
1. Selecteer **+ Toevoegen** om de **optiepagina SQL-implementatie selecteren** te openen. U aanvullende informatie over de verschillende databases weergeven door Details weergeven op de tegel Databases te selecteren.
1. Selecteer **Maken** op de tegel **SQL managed instances.** 

    ![Beheerde instantie selecteren](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. Op de pagina **Azure SQL Database Managed Instance** maken, op het tabblad **Basisbeginselen**
    1. Selecteer **onder Projectdetails**uw **abonnement** in de vervolgkeuzelijst en kies vervolgens voor Nieuwe resourcegroep **maken.** Typ een naam voor uw resourcegroep, zoals `myResourceGroup`. 
    1. Geef onder **Beheerde instantiegegevens**de naam van uw beheerde instantie op en het gebied waar u uw beheerde instantie wilt implementeren. Laat de **Compute + storage** op standaardwaarden. 
    1. Geef **onder Administratoraccount**een beheerdersaanmelding op, zoals `azureuser`, en een complex beheerderswachtwoord. 

    ![Primaire MI maken](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. Laat de rest van de instellingen op standaardwaarden staan en selecteer **Controleren + maken** om de beheerde instantie-instellingen te controleren. 
1. Selecteer **Maken** om uw primaire beheerde instantie te maken. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Maak uw resourcegroep en de primaire beheerde instantie met PowerShell. 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for your managed instance
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
   
   
   
   # Set the managed instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for your managed instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the managed instance service tier, compute level, and license mode
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
   Write-host "Primary managed instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary managed instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary managed instance name is" $primaryInstance
   Write-host "Secondary managed instance name is" $secondaryInstance
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
   
   
   # Create primary managed instance
   
   Write-host "Creating primary managed instance..."
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
   Write-host "Primary managed instance created successfully."
   ```

In dit gedeelte van de zelfstudie worden de volgende PowerShell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een Azure-resourcegroep.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Hiermee maakt u een virtueel netwerk.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie toe aan een virtueel netwerk. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hiermee haalt u een virtueel netwerk op in een resourcegroep. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Krijgt een subnet in een virtueel netwerk. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Hiermee maakt u een netwerkbeveiligingsgroep. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Hiermee maakt u een routetabel. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Werkt een subnetconfiguratie voor een virtueel netwerk bij.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Werkt een virtueel netwerk bij.  |
| [Get-azNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Krijgt een netwerkbeveiligingsgroep. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hiermee voegt u een configuratie van de netwerkbeveiligingsregel toe aan een netwerkbeveiligingsgroep. |
| [Set-azNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Werkt een netwerkbeveiligingsgroep bij.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Hiermee voegt u een route toe aan een routetabel. |
| [Set-azroutetabel](/powershell/module/az.network/set-azroutetable) | Hiermee wordt een routetabel bijgewerkt.  |
| [Nieuw-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Hiermee maakt u een azure SQL Database-beheerde instantie.  |

---

## <a name="2---create-secondary-virtual-network"></a>2 - Secundair virtueel netwerk maken
Als u de Azure-portal gebruikt om uw beheerde instantie te maken, moet u het virtuele netwerk afzonderlijk maken omdat er een vereiste is dat het subnet van de primaire en secundaire beheerde instantie geen overlappende bereiken heeft. Als u PowerShell gebruikt om uw beheerde instantie te configureren, gaat u verder naar stap 3. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 
Voer de volgende stappen uit om het subnetbereik van uw primaire virtuele netwerk te verifiëren:
1. Navigeer in de [Azure-portal](https://portal.azure.com)naar uw brongroep en selecteer het virtuele netwerk voor uw primaire instantie. 
1. Selecteer **Subnetten** onder **Instellingen** en noteer het **adresbereik**. Het subnetadresbereik van het virtuele netwerk voor de secundaire beheerde instantie kan dit niet overlappen. 


   ![Primair subnet](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

Voer de volgende stappen uit om een virtueel netwerk te maken:

1. Selecteer in de [Azure-portal](https://portal.azure.com)De optie **Een bron maken** en zoeken naar een virtueel *netwerk*. 
1. Selecteer de optie **Virtueel netwerk** dat door Microsoft is gepubliceerd en selecteer **Maken** op de volgende pagina. 
1. Vul de vereiste velden in om het virtuele netwerk voor uw secundaire beheerde instantie te configureren en selecteer **Vervolgens Maken**. 

   In de volgende tabel worden de waarden weergegeven die nodig zijn voor het secundaire virtuele netwerk:

    | **Veld** | Waarde |
    | --- | --- |
    | **Naam** |  De naam voor het virtuele netwerk dat moet worden `vnet-sql-mi-secondary`gebruikt door de secundaire beheerde instantie, zoals . |
    | **Adresruimte** | De adresruimte voor uw virtuele `10.128.0.0/16`netwerk, zoals . | 
    | **Abonnement** | Het abonnement waarbij uw primaire beheerde instantie en resourcegroep zich bevinden. |
    | **Regio** | De locatie waar u uw secundaire beheerde instantie implementeert. |
    | **Subnet** | De naam voor je subnet. `default`is standaard voor u beschikbaar. |
    | **Adresbereik**| Het adresbereik voor uw subnet. Dit moet anders zijn dan het subnetadresbereik dat wordt gebruikt `10.128.0.0/24`door het virtuele netwerk van uw primaire beheerde instantie, zoals .  |
    | &nbsp; | &nbsp; |

    ![Secundaire virtuele netwerkwaarden](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Deze stap is alleen nodig als u de Azure-portal gebruikt om uw beheerde instantie te implementeren. Ga door naar stap 3 als u PowerShell gebruikt. 

---

## <a name="3---create-a-secondary-managed-instance"></a>3 - Een secundair beheerde instantie maken
In deze stap maakt u een secundairbeheerde instantie in de Azure-portal, waarmee u ook de netwerken tussen de twee beheerde instanties configureert. 

Uw tweede beheerde instantie moet:
- Wees leeg. 
- Hebben een ander subnet en IP-bereik dan de primaire beheerde instantie. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Maak de secundaire beheerde instantie met behulp van de Azure-portal. 

1. Selecteer **Azure SQL** in het linkermenu van de Azure-portal. Als **Azure SQL** niet in de lijst staat, selecteert u Alle **services**en typt u Azure SQL in het zoekvak. (Optioneel) Selecteer de ster naast **Azure SQL** om deze favoriet te maken en voeg deze toe als een item in de linkernavigatie. 
1. Selecteer **+ Toevoegen** om de **optiepagina SQL-implementatie selecteren** te openen. U aanvullende informatie over de verschillende databases weergeven door Details weergeven op de tegel Databases te selecteren.
1. Selecteer **Maken** op de tegel **SQL managed instances.** 

    ![Beheerde instantie selecteren](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. Vul op het tabblad **Basisbeginselen** van de pagina **Beheerde instantie van Azure SQL Database Managed Instance** de vereiste velden in om uw secundaire beheerde instantie te configureren. 

   In de volgende tabel worden de waarden weergegeven die nodig zijn voor de secundaire beheerde instantie:
 
    | **Veld** | Waarde |
    | --- | --- |
    | **Abonnement** |  Het abonnement waar uw primaire beheerde instantie zich bevindt. |
    | **Resourcegroep**| De resourcegroep waar uw primaire beheerde instantie zich bevindt. |
    | **Naam van het beheerde exemplaar** | De naam van uw nieuwe secundaire beheerde instantie, zoals`sql-mi-secondary`  | 
    | **Regio**| De locatie voor uw secundaire beheerde instantie.  |
    | **Beheerdersaanmeldgegevens voor het beheerde exemplaar** | De login die u wilt gebruiken voor uw `azureuser`nieuwe secundaire beheerde instantie, zoals . |
    | **Wachtwoord** | Een complex wachtwoord dat wordt gebruikt door de beheerder login voor de nieuwe secundaire beheerde instantie.  |
    | &nbsp; | &nbsp; |

1. Selecteer onder het tabblad **Netwerken** voor het **virtuele netwerk**het virtuele netwerk dat u hebt gemaakt voor de secundaire beheerde instantie in de vervolgkeuzelijst.

   ![Secundaire MI-netwerken](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. Kies onder het tabblad **Extra instellingen** voor **Geo-replicatie**de optie **Ja** om te gebruiken _als secundaire failover_. Selecteer de primaire beheerde instantie in de vervolgkeuzelijst. 
    1. Zorg ervoor dat de collatie en tijdzone overeenkomen met die van de primaire beheerde instantie. De primaire beheerde instantie die in `SQL_Latin1_General_CP1_CI_AS` deze zelfstudie is gemaakt, heeft de standaardcollatie en de `(UTC) Coordinated Universal Time` tijdzone gebruikt. 

   ![Secundaire MI-netwerken](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. Selecteer **Controleren + maken** om de instellingen voor uw secundaire beheerde instantie te controleren. 
1. Selecteer **Maken** om uw secundaire beheerde instantie te maken. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Maak de secundaire beheerde instantie met PowerShell. 

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
   
   # Configure secondary managed instance subnet
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
   
   # Create secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary managed instance..."
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
   Write-host "Secondary managed instance created successfully."
   ```

In dit gedeelte van de zelfstudie worden de volgende PowerShell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een Azure-resourcegroep.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Hiermee maakt u een virtueel netwerk.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie toe aan een virtueel netwerk. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hiermee haalt u een virtueel netwerk op in een resourcegroep. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Krijgt een subnet in een virtueel netwerk. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Hiermee maakt u een netwerkbeveiligingsgroep. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Hiermee maakt u een routetabel. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Werkt een subnetconfiguratie voor een virtueel netwerk bij.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Werkt een virtueel netwerk bij.  |
| [Get-azNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Krijgt een netwerkbeveiligingsgroep. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hiermee voegt u een configuratie van de netwerkbeveiligingsregel toe aan een netwerkbeveiligingsgroep. |
| [Set-azNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Werkt een netwerkbeveiligingsgroep bij.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Hiermee voegt u een route toe aan een routetabel. |
| [Set-azroutetabel](/powershell/module/az.network/set-azroutetable) | Hiermee wordt een routetabel bijgewerkt.  |
| [Nieuw-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Hiermee maakt u een azure SQL Database-beheerde instantie.  |

---

## <a name="4---create-primary-gateway"></a>4 - Primaire gateway maken 
Voor twee beheerde exemplaren die deelnemen aan een failovergroep, moet er ExpressRoute of een gateway zijn geconfigureerd tussen de virtuele netwerken van de twee beheerde instanties om netwerkcommunicatie mogelijk te maken. Als u ervoor kiest [expressroute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) te configureren in plaats van twee VPN-gateways aan te sluiten, gaat u verder naar [stap 7.](#7---create-a-failover-group)  

In dit artikel vindt u stappen om de twee VPN-gateways te maken en deze te verbinden, maar u doorgaan met het maken van de failovergroep als u ExpressRoute hebt geconfigureerd. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Maak de gateway voor het virtuele netwerk van uw primaire beheerde instantie met behulp van de Azure-portal. 


1. Ga in de [Azure-portal](https://portal.azure.com)naar uw resourcegroep en selecteer de **virtuele netwerkbron** voor uw primaire beheerde instantie. 
1. Selecteer **Subnetten** onder **Instellingen** en selecteer vervolgens om een nieuw **gateway-subnet**toe te voegen. Laat de standaardwaarden achter. 

   ![Gateway toevoegen voor primaire beheerde instantie](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Zodra de subnetgateway is gemaakt, selecteert u Een resource `Virtual network gateway` **maken** in het linkernavigatiedeelvenster en typt u vervolgens het zoekvak in. Selecteer de **virtuele netwerkgatewaybron** die door **Microsoft is**gepubliceerd. 

   ![Een nieuwe virtuele netwerkgateway maken](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Vul de vereiste velden in om de gateway te configureren van uw primaire beheerde instantie. 

   In de volgende tabel worden de waarden weergegeven die nodig zijn voor de gateway voor de primaire beheerde instantie:
 
    | **Veld** | Waarde |
    | --- | --- |
    | **Abonnement** |  Het abonnement waar uw primaire beheerde instantie zich bevindt. |
    | **Naam** | De naam voor uw virtuele `primary-mi-gateway`netwerkgateway, zoals . | 
    | **Regio** | Het gebied waar uw primaire beheerde instantie zich bevindt. |
    | **Gatewaytype** | Selecteer **VPN**. |
    | **VPN-type** | Selecteer **Route-gebaseerd** |
    | **Sku**| Standaard laten `VpnGw1`staan van . |
    | **Locatie**| De locatie waar uw primaire beheerde instantie en het primaire virtuele netwerk zich bevinden.   |
    | **Virtueel netwerk**| Selecteer het virtuele netwerk dat is gemaakt `vnet-sql-mi-primary`in sectie 2, zoals . |
    | **Openbaar IP-adres**| Selecteer **Nieuw maken**. |
    | **Naam van openbaar IP-adres**| Voer een naam in voor `primary-gateway-IP`uw IP-adres, zoals . |
    | &nbsp; | &nbsp; |

1. Laat de andere waarden als standaard staan en selecteer **Controleren + maken** om de instellingen voor uw virtuele netwerkgateway te controleren.

   ![Primaire gateway-instellingen](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Selecteer **Maken** om uw nieuwe virtuele netwerkgateway te maken. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Maak de gateway voor het virtuele netwerk van uw primaire beheerde instantie met PowerShell. 

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

In dit gedeelte van de zelfstudie worden de volgende PowerShell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hiermee haalt u een virtueel netwerk op in een resourcegroep. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie toe aan een virtueel netwerk. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Werkt een virtueel netwerk bij.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Krijgt een subnet in een virtueel netwerk. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Hiermee maakt u een openbaar IP-adres.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Maakt een IP-configuratie voor een virtuele netwerkgateway |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Maakt een virtuele netwerkgateway |


---


## <a name="5---create-secondary-gateway"></a>5 - Secundaire gateway maken 
Maak in deze stap de gateway voor het virtuele netwerk van uw secundaire beheerde instantie met behulp van de Azure-portal, 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Herhaal met de Azure-portal de stappen in de vorige sectie om het virtuele netwerksubnet en de gateway voor de secundaire beheerde instantie te maken. Vul de vereiste velden in om de gateway voor uw secundaire beheerde instantie te configureren. 

   In de volgende tabel worden de waarden weergegeven die nodig zijn voor de gateway voor de secundaire beheerde instantie:

   | **Veld** | Waarde |
   | --- | --- |
   | **Abonnement** |  Het abonnement waarbij uw secundaire beheerde instantie zich bevindt. |
   | **Naam** | De naam voor uw virtuele `secondary-mi-gateway`netwerkgateway, zoals . | 
   | **Regio** | Het gebied waar uw secundaire beheerde instantie zich bevindt. |
   | **Gatewaytype** | Selecteer **VPN**. |
   | **VPN-type** | Selecteer **Route-gebaseerd** |
   | **Sku**| Standaard laten `VpnGw1`staan van . |
   | **Locatie**| De locatie waar uw secundaire beheerde instantie en secundair virtueel netwerk zich bevinden.   |
   | **Virtueel netwerk**| Selecteer het virtuele netwerk dat is gemaakt `vnet-sql-mi-secondary`in sectie 2, zoals . |
   | **Openbaar IP-adres**| Selecteer **Nieuw maken**. |
   | **Naam van openbaar IP-adres**| Voer een naam in voor `secondary-gateway-IP`uw IP-adres, zoals . |
   | &nbsp; | &nbsp; |

   ![Secundaire gateway-instellingen](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Maak de gateway voor het virtuele netwerk van de secundaire beheerde instantie met PowerShell. 

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

In dit gedeelte van de zelfstudie worden de volgende PowerShell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hiermee haalt u een virtueel netwerk op in een resourcegroep. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie toe aan een virtueel netwerk. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Werkt een virtueel netwerk bij.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Krijgt een subnet in een virtueel netwerk. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Hiermee maakt u een openbaar IP-adres.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Maakt een IP-configuratie voor een virtuele netwerkgateway |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Maakt een virtuele netwerkgateway |

---


## <a name="6---connect-the-gateways"></a>6 - Sluit de gateways aan
Maak in deze stap een bidirectionele verbinding tussen de twee gateways van de twee virtuele netwerken. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Sluit de twee gateways aan via de Azure-portal. 


1. Selecteer **Een resource maken** vanuit de [Azure-portal](https://portal.azure.com).
1. Typ `connection` in het zoekvak en druk op Enter om te zoeken, waardoor u naar de **verbindingsbron** gaat, gepubliceerd door Microsoft.
1. Selecteer **Maken** om uw verbinding te maken. 
1. Selecteer op het tabblad **Basisbeginselen** de volgende waarden en selecteer **OK**. 
    1. Selecteer `VNet-to-VNet` voor het **type Verbinding**. 
    1. Selecteer uw abonnement in de vervolgkeuzelijst. 
    1. Selecteer de resourcegroep voor uw beheerde instantie in de vervolgkeuzelijst. 
    1. De locatie van uw primaire beheerde instantie selecteren in de vervolgkeuzelijst 
1. Selecteer of voer op het tabblad **Instellingen** de volgende waarden in en selecteer **OK:**
    1. Kies de primaire netwerkgateway voor de eerste `Primary-Gateway`virtuele **netwerkgateway,** zoals .  
    1. Kies de secundaire netwerkgateway voor de `Secondary-Gateway`tweede virtuele **netwerkgateway,** zoals . 
    1. Schakel het selectievakje naast **Bidirectionele connectiviteit instellen**in. 
    1. Laat de standaardnaam van de primaire verbinding over of wijzig deze tot een waarde naar keuze. 
    1. Geef een **gedeelde sleutel (PSK)** op `mi1m2psk`voor de verbinding, zoals . 

   ![Gatewayverbinding maken](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. Controleer op het tabblad **Overzicht** de instellingen voor uw bidirectionele verbinding en selecteer **OK** om uw verbinding te maken. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Sluit de twee gateways aan met PowerShell. 

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

In dit gedeelte van de zelfstudie wordt de volgende PowerShell-cmdlet gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Hiermee maakt u een verbinding tussen de twee virtuele netwerkgateways.   |

---


## <a name="7---create-a-failover-group"></a>7 - Een failovergroep maken
In deze stap maakt u de failovergroep en voegt u beide beheerde instanties toe. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Maak de failovergroep met behulp van de Azure-portal. 


1. Selecteer **Azure SQL** in het linkermenu van de [Azure-portal.](https://portal.azure.com) Als **Azure SQL** niet in de lijst staat, selecteert u Alle **services**en typt u Azure SQL in het zoekvak. (Optioneel) Selecteer de ster naast **Azure SQL** om deze favoriet te maken en voeg deze toe als een item in de linkernavigatie. 
1. Selecteer de primaire beheerde instantie die u `sql-mi-primary`in de eerste sectie hebt gemaakt, zoals . 
1. Navigeer **onder Instellingen**naar **Instantiefailovergroepen** en kies vervolgens de **groep Toevoegen** om de pagina **Failovergroep instantie** te openen. 

   ![Een failovergroep toevoegen](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. Typ op de pagina **Failovergroep instance** de naam van `failovergrouptutorial` uw failovergroep, zoals `sql-mi-secondary` en kies vervolgens de secundaire beheerde instantie, zoals in de vervolgkeuzelijst. Selecteer **Maken** om de failovergroep te maken. 

   ![Failovergroep maken](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Zodra de implementatie van failovergroepen is voltooid, wordt u teruggezet naar de **groeppagina Failover.** 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Maak de failovergroep met PowerShell. 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

In dit gedeelte van de zelfstudie wordt de volgende PowerShell-cmdlet gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [Nieuw-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Hiermee maakt u een nieuwe failovergroep met Azure SQL Database beheerde instantie.  |


---


## <a name="8---test-failover"></a>8 - Test failover
In deze stap mislukt u uw failovergroep naar de secundaire server en mislukt u vervolgens terug met de Azure-portal. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Test failover met behulp van de Azure-portal. 


1. Navigeer naar uw _secundaire_ beheerde instantie in de [Azure-portal](https://portal.azure.com) en selecteer **Instantiefailovergroepen** onder instellingen. 
1. Controleer welke beheerde instantie de primaire instantie is en welke beheerde instantie de secundaire instantie is. 
1. Selecteer **Failover** en selecteer **Vervolgens Ja** op de waarschuwing dat de verbinding met TDS-sessies wordt verbroken. 

   ![Fail over de failovergroep](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Controleer welke manged instantie de primaire is en welke instantie de secundaire is. Als het niet lukt, moeten de twee instanties van rol zijn gewisseld. 

   ![Beheerde exemplaren zijn van rol gewisseld na failover](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Ga naar de nieuwe _secundaire_ beheerde instantie en selecteer **Failover** nogmaals om de primaire instantie terug naar de primaire rol te laten mislukken. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Test failover met PowerShell. 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


Failovergroep terugzetten naar de primaire server:

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

In dit gedeelte van de zelfstudie worden de volgende PowerShell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Hiermee worden beheerde failovergroepen voor verschillende instantie weergegeven of vermeldt.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Hiermee wordt een failover van een beheerde failovergroep uitgevoerd. | 

---



## <a name="clean-up-resources"></a>Resources opschonen
Ruim resources op door eerst de beheerde instantie te verwijderen, vervolgens het virtuele cluster, vervolgens de resterende resources en ten slotte de resourcegroep. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Navigeer naar uw brongroep in de [Azure-portal.](https://portal.azure.com) 
1. Selecteer de beheerde instantie(s) en selecteer **Verwijderen**. Typ `yes` in het tekstvak om te bevestigen dat u de bron wilt verwijderen en selecteer **Verwijderen**. Dit proces kan enige tijd in beslag nemen op de achtergrond en totdat het klaar is, u het *virtuele cluster* of andere afhankelijke bronnen niet verwijderen. Controleer de verwijdering op het tabblad Activiteit om te bevestigen dat uw beheerde instantie is verwijderd. 
1. Zodra de beheerde instantie is verwijderd, verwijdert u het *virtuele cluster* door het te selecteren in uw brongroep en vervolgens **Verwijderen**te kiezen . Typ `yes` in het tekstvak om te bevestigen dat u de bron wilt verwijderen en selecteer **Verwijderen**. 
1. Verwijder de resterende bronnen. Typ `yes` in het tekstvak om te bevestigen dat u de bron wilt verwijderen en selecteer **Verwijderen**. 
1. Verwijder de brongroep door **resourcegroep verwijderen**te selecteren, `myResourceGroup`de naam van de resourcegroep in te typen en vervolgens **Verwijderen**te selecteren . 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

U moet de brongroep twee keer verwijderen. Als u de resourcegroep de eerste keer verwijdert, worden de beheerde instantie en virtuele clusters verwijderd, maar wordt deze vervolgens mislukt met het foutbericht `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`. Voer de opdracht Verwijderen-AzResourceGroep een tweede keer uit om resterende resources en de resourcegroep te verwijderen.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing managed instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resouce group..."
```

In dit gedeelte van de zelfstudie wordt de volgende PowerShell-cmdlet gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep. |

---

## <a name="full-script"></a>Volledige script

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een Azure-resourcegroep.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Hiermee maakt u een virtueel netwerk.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie toe aan een virtueel netwerk. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hiermee haalt u een virtueel netwerk op in een resourcegroep. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Krijgt een subnet in een virtueel netwerk. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Hiermee maakt u een netwerkbeveiligingsgroep. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Hiermee maakt u een routetabel. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Werkt een subnetconfiguratie voor een virtueel netwerk bij.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Werkt een virtueel netwerk bij.  |
| [Get-azNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Krijgt een netwerkbeveiligingsgroep. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hiermee voegt u een configuratie van de netwerkbeveiligingsregel toe aan een netwerkbeveiligingsgroep. |
| [Set-azNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Werkt een netwerkbeveiligingsgroep bij.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Hiermee voegt u een route toe aan een routetabel. |
| [Set-azroutetabel](/powershell/module/az.network/set-azroutetable) | Hiermee wordt een routetabel bijgewerkt.  |
| [Nieuw-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Hiermee maakt u een azure SQL Database-beheerde instantie.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Retourneert informatie over Azure SQL Managed Database Instance. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Hiermee maakt u een openbaar IP-adres.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Maakt een IP-configuratie voor een virtuele netwerkgateway |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Maakt een virtuele netwerkgateway |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Hiermee maakt u een verbinding tussen de twee virtuele netwerkgateways.   |
| [Nieuw-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Hiermee maakt u een nieuwe failovergroep met Azure SQL Database beheerde instantie.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Hiermee worden beheerde failovergroepen voor verschillende instantie weergegeven of vermeldt.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Hiermee wordt een failover van een beheerde failovergroep uitgevoerd. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep. | 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Er zijn geen scripts beschikbaar voor de Azure-portal.

---

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een failovergroep geconfigureerd tussen twee beheerde exemplaren. U hebt geleerd hoe u:

> [!div class="checklist"]
> - Een primair beheerde instantie maken
> - Maak een secundairbeheerde instantie als onderdeel van een [failovergroep](sql-database-auto-failover-group.md). 
> - Testfailover

Ga naar de volgende snelle start over hoe u verbinding maken met uw beheerde instantie en hoe u een database herstellen naar uw beheerde instantie: 

> [!div class="nextstepaction"]
> [Verbinding maken met uw beheerde instantie](sql-database-managed-instance-configure-vm.md)
> [Een database herstellen naar een beheerde instantie](sql-database-managed-instance-get-started-restore.md)


