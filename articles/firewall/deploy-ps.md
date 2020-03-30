---
title: Azure Firewall implementeren en configureren met Azure PowerShell
description: In dit artikel leert u hoe u Azure Firewall implementeert en configureert met de Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 4/10/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 7f48012ca1f97c2e28380d95da37863c4bc17f63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73831841"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-powershell"></a>Azure Firewall implementeren en configureren met Azure PowerShell

Het beheren van toegang tot uitgaande netwerken is een belangrijk onderdeel van een algemeen netwerkbeveiligingsabonnement. U bijvoorbeeld de toegang tot websites beperken. U ook de uitgaande IP-adressen en poorten beperken die toegankelijk zijn.

Een van de manieren waarop u de toegang tot uitgaande netwerken kunt beheren vanaf een Azure-subnet is met Azure Firewall. Met Azure Firewall kunt u het volgende configureren:

* Toepassingsregels die volledig gekwalificeerde domeinnamen (FQDN's) definiëren waartoe toegang kan worden verkregen via een subnet.
* Netwerkregels die een bronadres, protocol, doelpoort en doeladres definiëren.

Netwerkverkeer is onderhevig aan de geconfigureerde firewallregels wanneer u het routeert naar de firewall als standaardgateway van het subnet.

Voor dit artikel maakt u een vereenvoudigde enkele VNet met drie subnetten voor eenvoudige implementatie. Voor productie-implementaties wordt een [hub- en spaakmodel](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) aanbevolen, waarbij de firewall zich in zijn eigen VNet bevindt. De workloadservers bevinden zich in peered VNets in dezelfde regio met een of meer subnetten.

* **AzureFirewallSubnet** – De firewall bevindt zich in dit subnet.
* **Workload-SN** – De workloadserver bevindt zich in dit subnet. Het netwerkverkeer van dit subnet gaat via de firewall.
* **Jump-SN** – De "jumpserver" bevindt zich in dit subnet. De jumpserver heeft een openbaar IP-adres waarmee u verbinding kunt maken via een extern bureaublad. Van daaruit kunt u vervolgens (via een ander extern bureaublad) verbinding maken met de workloadserver.

![Netwerkinfrastructuur van zelfstudie](media/tutorial-firewall-rules-portal/Tutorial_network.png)

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een testnetwerkomgeving instellen
> * Een firewall implementeren
> * Een standaardroute maken
> * Een toepassingsregel configureren om toegang te krijgen tot www.google.com
> * Een netwerkregel configureren om toegang tot externe DNS-servers toe te staan
> * De firewall testen

Als u dat liever hebt, u deze procedure voltooien met behulp van de [Azure-portal.](tutorial-firewall-deploy-portal.md)

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Deze procedure vereist dat u PowerShell lokaal uitvoert. U moet de Azure PowerShell-module hebben geïnstalleerd. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-Az-ps). Nadat u de versie van PowerShell hebt gecontroleerd, voert u `Connect-AzAccount` uit om een verbinding op te zetten met Azure.

## <a name="set-up-the-network"></a>Netwerk instellen

Maak eerst een resourcegroep met de resources die nodig zijn om de firewall te implementeren. Maak vervolgens een VNet, subnetten en testservers.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

De resourcegroep bevat alle resources voor de implementatie.

```azurepowershell
New-AzResourceGroup -Name Test-FW-RG -Location "East US"
```

### <a name="create-a-vnet"></a>Een VNet maken

Dit virtuele netwerk heeft drie subnetten:

> [!NOTE]
> De grootte van het subnet AzureFirewallSubnet is /26. Zie [Veelgestelde vragen](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size)over azure firewall voor meer informatie over de subnetgrootte.

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name AzureFirewallSubnet -AddressPrefix 10.0.1.0/26
$Worksub = New-AzVirtualNetworkSubnetConfig -Name Workload-SN -AddressPrefix 10.0.2.0/24
$Jumpsub = New-AzVirtualNetworkSubnetConfig -Name Jump-SN -AddressPrefix 10.0.3.0/24
```
Maak nu het virtuele netwerk:

```azurepowershell
$testVnet = New-AzVirtualNetwork -Name Test-FW-VN -ResourceGroupName Test-FW-RG `
-Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $FWsub, $Worksub, $Jumpsub
```

### <a name="create-virtual-machines"></a>Virtuele machines maken

Maak nu de virtuele jump- en workloadmachines en plaats ze in de toepasselijke subnetten.
Wanneer u daarom wordt gevraagd, typt u een gebruikersnaam en wachtwoord voor de virtuele machine.

Maak de Virtuele Srv-Jump machine.

```azurepowershell
New-AzVm `
    -ResourceGroupName Test-FW-RG `
    -Name "Srv-Jump" `
    -Location "East US" `
    -VirtualNetworkName Test-FW-VN `
    -SubnetName Jump-SN `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

Maak een virtuele werkbelastingmachine zonder openbaar IP-adres.
Wanneer u daarom wordt gevraagd, typt u een gebruikersnaam en wachtwoord voor de virtuele machine.

```azurepowershell
#Create the NIC
$NIC = New-AzNetworkInterface -Name Srv-work -ResourceGroupName Test-FW-RG `
 -Location "East US" -Subnetid $testVnet.Subnets[1].Id 

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName Srv-Work -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Srv-Work -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName Test-FW-RG -Location "East US" -VM $VirtualMachine -Verbose
```

## <a name="deploy-the-firewall"></a>De firewall implementeren

Implementeer de firewall nu in het virtuele netwerk.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName Test-FW-RG `
  -Location "East US" -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name Test-FW01 -ResourceGroupName Test-FW-RG -Location "East US" -VirtualNetworkName Test-FW-VN -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP
```

Noteer het privé-IP-adres. U zult het later gebruiken wanneer u de standaardroute maakt.

## <a name="create-a-default-route"></a>Een standaardroute maken

Een tabel maken, waarbij de bgp-routepropagatie is uitgeschakeld

```azurepowershell
$routeTableDG = New-AzRouteTable `
  -Name Firewall-rt-table `
  -ResourceGroupName Test-FW-RG `
  -location "East US" `
  -DisableBgpRoutePropagation

#Create a route
 Add-AzRouteConfig `
  -Name "DG-Route" `
  -RouteTable $routeTableDG `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $testVnet `
  -Name Workload-SN `
  -AddressPrefix 10.0.2.0/24 `
  -RouteTable $routeTableDG | Set-AzVirtualNetwork
```

## <a name="configure-an-application-rule"></a>Een toepassingsregel configureren

De toepassingsregel geeft uitgaande toegang tot www.google.com.

```azurepowershell
$AppRule1 = New-AzFirewallApplicationRule -Name Allow-Google -SourceAddress 10.0.2.0/24 `
  -Protocol http, https -TargetFqdn www.google.com

$AppRuleCollection = New-AzFirewallApplicationRuleCollection -Name App-Coll01 `
  -Priority 200 -ActionType Allow -Rule $AppRule1

$Azfw.ApplicationRuleCollections = $AppRuleCollection

Set-AzFirewall -AzureFirewall $Azfw
```

Azure Firewall bevat een ingebouwde regelverzameling voor infrastructuur-FQDN’s die standaard zijn toegestaan. Deze FQDN’s zijn specifiek voor het platform en kunnen niet voor andere doeleinden worden gebruikt. Zie [FQDN's voor infrastructuur](infrastructure-fqdns.md) voor meer informatie.

## <a name="configure-a-network-rule"></a>Een netwerkregel configureren

De netwerkregel biedt uitgaande toegang tot twee IP-adressen in poort 53 (DNS).

```azurepowershell
$NetRule1 = New-AzFirewallNetworkRule -Name "Allow-DNS" -Protocol UDP -SourceAddress 10.0.2.0/24 `
   -DestinationAddress 209.244.0.3,209.244.0.4 -DestinationPort 53

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 200 `
   -Rule $NetRule1 -ActionType "Allow"

$Azfw.NetworkRuleCollections = $NetRuleCollection

Set-AzFirewall -AzureFirewall $Azfw
```

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Het primaire en secundaire DNS-adres voor de netwerkinterface **Srv-Work** wijzigen

Configureer de primaire en secundaire DNS-adressen van de server voor testdoeleinden in deze procedure. Dit is geen algemene Azure Firewall-vereiste.

```azurepowershell
$NIC.DnsSettings.DnsServers.Add("209.244.0.3")
$NIC.DnsSettings.DnsServers.Add("209.244.0.4")
$NIC | Set-AzNetworkInterface
```

## <a name="test-the-firewall"></a>De firewall testen

Test nu de firewall om te bevestigen dat het werkt zoals verwacht.

1. Let op het privé-IP-adres voor de **virtuele srv-work-machine:**

   ```
   $NIC.IpConfigurations.PrivateIpAddress
   ```

1. Sluit een extern bureaublad aan op de virtuele machine **van Srv-Jump** en meld u aan. Open van daaruit een externe desktopverbinding met het **privé-IP-adres van Srv-Work** en meld u aan.

3. Open **op SRV-Work**een PowerShell-venster en voer de volgende opdrachten uit:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Beide opdrachten moeten antwoorden retourneren, waaruit blijkt dat uw DNS-query's via de firewall komen.

1. Voer de volgende opdrachten uit:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   De `www.google.com` aanvragen moeten slagen `www.microsoft.com` en de aanvragen moeten mislukken. Dit toont aan dat uw firewallregels werken zoals verwacht.

Dus nu heb je geverifieerd dat de firewall regels werken:

* Kunt u DNS-namen omzetten met behulp van de geconfigureerde externe DNS-server.
* Kunt u bladeren naar de enige toegestane FQDN, maar niet naar andere.

## <a name="clean-up-resources"></a>Resources opschonen

U uw firewallbronnen behouden voor de volgende zelfstudie of, indien dit niet meer nodig is, de brongroep **Test-FW-RG** verwijderen om alle firewallgerelateerde bronnen te verwijderen:

```azurepowershell
Remove-AzResourceGroup -Name Test-FW-RG
```

## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Azure Firewall-logboeken bewaken](./tutorial-diagnostics.md)
