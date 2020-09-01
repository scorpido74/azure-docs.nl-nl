---
title: Azure Firewall implementeren en configureren met behulp van Azure PowerShell
description: In dit artikel vindt u informatie over het implementeren en configureren van Azure Firewall met behulp van de Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 08/28/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: c720d7c261421ade9dfce01f0b116123dcab1e55
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89071700"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-powershell"></a>Azure Firewall implementeren en configureren met behulp van Azure PowerShell

Het beheren van toegang tot uitgaande netwerken is een belangrijk onderdeel van een algemeen netwerkbeveiligingsabonnement. U kunt bijvoorbeeld de toegang tot websites beperken. U kunt ook de toegang tot uitgaande IP-adressen en poorten beperken.

Een van de manieren waarop u de toegang tot uitgaande netwerken kunt beheren vanaf een Azure-subnet is met Azure Firewall. Met Azure Firewall kunt u het volgende configureren:

* Toepassingsregels die volledig gekwalificeerde domeinnamen (FQDN's) definiëren waartoe toegang kan worden verkregen via een subnet.
* Netwerkregels die een bronadres, protocol, doelpoort en doeladres definiëren.

Netwerkverkeer is onderhevig aan de geconfigureerde firewallregels wanneer u het routeert naar de firewall als standaardgateway van het subnet.

Voor dit artikel maakt u een vereenvoudigd single VNet met drie subnetten voor een eenvoudige implementatie. Voor productie-implementaties wordt een [hub en spoke-model](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) aanbevolen, waarbij de firewall zich in een eigen VNet bevindt. De werkbelastingservers bevinden zich in gepeerde VNets in dezelfde regio met een of meer subnetten.

* **AzureFirewallSubnet** – De firewall bevindt zich in dit subnet.
* **Workload-SN** – De workloadserver bevindt zich in dit subnet. Het netwerkverkeer van dit subnet gaat via de firewall.
* **Jump-SN** – De "jumpserver" bevindt zich in dit subnet. De jumpserver heeft een openbaar IP-adres waarmee u verbinding kunt maken via een extern bureaublad. Van daaruit kunt u vervolgens (via een ander extern bureaublad) verbinding maken met de workloadserver.

![Netwerkinfrastructuur van zelfstudie](media/tutorial-firewall-rules-portal/Tutorial_network.png)

In dit artikel leert u het volgende:


* Een testnetwerkomgeving instellen
* Een firewall implementeren
* Een standaardroute maken
* Een toepassingsregel configureren om toegang tot www.google.com toe te staan
* Een netwerkregel configureren om toegang tot externe DNS-servers toe te staan
* De firewall testen

Als u wilt, kunt u deze procedure volt ooien met behulp van de [Azure Portal](tutorial-firewall-deploy-portal.md).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor deze procedure moet u Power shell lokaal uitvoeren. U moet de module Azure PowerShell hebben geïnstalleerd. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-Az-ps). Nadat u de versie van PowerShell hebt gecontroleerd, voert u `Connect-AzAccount` uit om een verbinding op te zetten met Azure.

## <a name="set-up-the-network"></a>Het netwerk instellen

Maak eerst een resourcegroep met de resources die nodig zijn om de firewall te implementeren. Maak vervolgens een VNet, subnetten en testservers.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

De resource groep bevat alle resources voor de implementatie.

```azurepowershell
New-AzResourceGroup -Name Test-FW-RG -Location "East US"
```

### <a name="create-a-vnet"></a>Een VNet maken

Dit virtuele netwerk heeft drie subnetten:

> [!NOTE]
> De grootte van het subnet AzureFirewallSubnet is /26. Zie [Veelgestelde vragen over Azure Firewall](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size) voor meer informatie over de grootte van het subnet.

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

Maak de SRV-Jump-virtuele machine.

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

Maak een virtuele werk belasting machine zonder openbaar IP-adres.
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

Een tabel maken waarbij BGP-route doorgifte is uitgeschakeld

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

De toepassings regel staat uitgaande toegang tot www.google.com toe.

```azurepowershell
$AppRule1 = New-AzFirewallApplicationRule -Name Allow-Google -SourceAddress 10.0.2.0/24 `
  -Protocol http, https -TargetFqdn www.google.com

$AppRuleCollection = New-AzFirewallApplicationRuleCollection -Name App-Coll01 `
  -Priority 200 -ActionType Allow -Rule $AppRule1

$Azfw.ApplicationRuleCollections.Add($AppRuleCollection)

Set-AzFirewall -AzureFirewall $Azfw
```

Azure Firewall bevat een ingebouwde regelverzameling voor infrastructuur-FQDN’s die standaard zijn toegestaan. Deze FQDN’s zijn specifiek voor het platform en kunnen niet voor andere doeleinden worden gebruikt. Zie [FQDN's voor infrastructuur](infrastructure-fqdns.md) voor meer informatie.

## <a name="configure-a-network-rule"></a>Een netwerkregel configureren

De netwerk regel staat uitgaande toegang tot twee IP-adressen op poort 53 (DNS) toe.

```azurepowershell
$NetRule1 = New-AzFirewallNetworkRule -Name "Allow-DNS" -Protocol UDP -SourceAddress 10.0.2.0/24 `
   -DestinationAddress 209.244.0.3,209.244.0.4 -DestinationPort 53

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 200 `
   -Rule $NetRule1 -ActionType "Allow"

$Azfw.NetworkRuleCollections.Add($NetRuleCollection)

Set-AzFirewall -AzureFirewall $Azfw
```

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Het primaire en secundaire DNS-adres voor de netwerkinterface **Srv-Work** wijzigen

Voor test doeleinden in deze procedure configureert u de primaire en secundaire DNS-adressen van de server. Dit is geen algemene Azure Firewall-vereiste.

```azurepowershell
$NIC.DnsSettings.DnsServers.Add("209.244.0.3")
$NIC.DnsSettings.DnsServers.Add("209.244.0.4")
$NIC | Set-AzNetworkInterface
```

## <a name="test-the-firewall"></a>De firewall testen

Test nu de firewall om te controleren of deze werkt zoals verwacht.

1. Noteer het privé-IP-adres voor de virtuele machine **met SRV-werk** :

   ```
   $NIC.IpConfigurations.PrivateIpAddress
   ```

1. Verbind een extern bureaublad met de virtuele machine **Srv-Jump** en meld u aan. Open vanaf daar een verbinding met een extern bureau blad met het privé-IP-adres van **SRV-werk** en meld u aan.

3. Open op **SRV**een Power shell-venster en voer de volgende opdrachten uit:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Beide opdrachten moeten antwoorden retour neren, zodat u kunt zien dat uw DNS-query's via de firewall worden ontvangen.

1. Voer de volgende opdrachten uit:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   De `www.google.com` aanvragen moeten slagen en de `www.microsoft.com` aanvragen moeten mislukken. Dit laat zien dat uw firewall regels werken zoals verwacht.

Nu u hebt geverifieerd dat de firewallregels werken:

* Kunt u DNS-namen omzetten met behulp van de geconfigureerde externe DNS-server.
* Kunt u bladeren naar de enige toegestane FQDN, maar niet naar andere.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt uw firewall bronnen voor de volgende zelf studie houden, of als u deze niet meer nodig hebt, verwijdert u de resource groep **test-FW-RG** om alle firewall-gerelateerde resources te verwijderen:

```azurepowershell
Remove-AzResourceGroup -Name Test-FW-RG
```

## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Azure Firewall-logboeken bewaken](./tutorial-diagnostics.md)
