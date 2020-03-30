---
title: Geforceerde tunneling configureren voor site-to-site-verbindingen
description: Hoe u al het internetgebonden verkeer omleiden of 'forceren' terug naar uw on-premises locatie.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/01/2018
ms.author: cherylmc
ms.openlocfilehash: fc35654403bbe1375d4188476b11fd0453f74345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244626"
---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Geforceerde tunneling met het implementatiemodel van Azure Resource Manager configureren

Met geforceerde tunneling kunt u alle voor internet bestemde verkeer geforceerd terugsturen naar uw on-premises locatie via een site-naar-site-VPN-tunnel voor inspectie en controle. Dit is een kritieke beveiligingsvereiste voor het meeste IT-beleid voor bedrijven. Zonder gedwongen tunneling, internetgebonden verkeer van uw VM's in Azure, wordt altijd rechtstreeks vanuit de Azure-netwerkinfrastructuur naar het internet gesloopt, zonder de optie om het verkeer te inspecteren of te controleren. Ongeautoriseerde internettoegang kan mogelijk leiden tot openbaarmaking van informatie of andere vormen van inbreuken op de beveiliging.



[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

In dit artikel u gedwongen tunneling configureren voor virtuele netwerken die zijn gemaakt met behulp van het implementatiemodel Resource Manager. Geforceerde tunneling kan worden geconfigureerd met PowerShell, niet via de portal. Als u gedwongen tunneling voor het klassieke implementatiemodel wilt configureren, selecteert u klassiek artikel in de volgende vervolgkeuzelijst:

> [!div class="op_single_selector"]
> * [PowerShell - Klassiek](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell - Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="about-forced-tunneling"></a>Over gedwongen tunneling

Het volgende diagram illustreert hoe gedwongen tunneling werkt. 

![Gedwongen tunneling](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

In het bovenstaande voorbeeld wordt het Frontend-subnet niet geforceerd getunneld. De workloads in het Frontend-subnet kunnen verzoeken van klanten via internet rechtstreeks blijven accepteren en beantwoorden. De Mid-tier en Backend subnetten worden gedwongen getunneld. Alle uitgaande verbindingen van deze twee subnetten naar het internet worden via een van de S2S VPN-tunnels geforceerd of omgeleid naar een on-premises site.

Hiermee u internettoegang vanaf uw virtuele machines of cloudservices in Azure beperken en inspecteren, terwijl u uw vereiste multi-tier servicearchitectuur blijft inschakelen. Als er geen internetgerichte workloads in uw virtuele netwerken zijn, u ook gedwongen tunneling toepassen op de hele virtuele netwerken.

## <a name="requirements-and-considerations"></a>Eisen en overwegingen

Geforceerde tunneling in Azure wordt geconfigureerd via door gebruikers gedefinieerde routes van virtuele netwerken. Het omleiden van verkeer naar een on-premises site wordt uitgedrukt als een standaardroute naar de Azure VPN-gateway. Zie [Door gebruikers gedefinieerde routes en IP-forwarding](../virtual-network/virtual-networks-udr-overview.md)voor meer informatie over door de gebruiker gedefinieerde routering en virtuele netwerken.

* Elk virtueel netwerksubnet heeft een ingebouwde systeemrouteringstabel. De tabel systeemroutering heeft de volgende drie groepen routes:
  
  * **Lokale VNet-routes:** Rechtstreeks naar de bestemming VM's in hetzelfde virtuele netwerk.
  * **On-premises routes:** Naar de Azure VPN-gateway.
  * **Standaardroute:** Rechtstreeks naar het internet. Pakketten die bestemd zijn voor de privé-IP-adressen die niet onder de vorige twee routes vallen, worden verwijderd.
* Deze procedure maakt gebruik van door de gebruiker gedefinieerde routes (UDR) om een routeringstabel te maken om een standaardroute toe te voegen en vervolgens de routeringstabel te koppelen aan uw VNet-subnet(s) om gedwongen tunneling op die subnetten mogelijk te maken.
* Gedwongen tunneling moet worden gekoppeld aan een VNet dat een routegebaseerde VPN-gateway heeft. U moet een 'standaardsite' instellen tussen de lokale sites met een andere locatie die zijn aangesloten op het virtuele netwerk. Ook moet het on-premises VPN-apparaat worden geconfigureerd met 0.0.0.0/0 als verkeerskiezer. 
* ExpressRoute gedwongen tunneling is niet geconfigureerd via dit mechanisme, maar in plaats daarvan, wordt ingeschakeld door reclame voor een standaard route via de ExpressRoute BGP peering sessies. Zie voor meer informatie de [ExpressRoute Documentation](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="configuration-overview"></a>Configuratieoverzicht

Met de volgende procedure u een resourcegroep en een VNet maken. U maakt vervolgens een VPN-gateway en configureert gedwongen tunneling. In deze procedure heeft het virtuele netwerk 'MultiTier-VNet' drie subnetten: 'Frontend', 'Midtier' en 'Backend', met vier cross-premises verbindingen: 'DefaultSiteHQ' en drie Branches.

In de procedurestappen wordt de 'DefaultSiteHQ' ingesteld als de standaardsiteverbinding voor gedwongen tunneling en worden de subnetten 'Midtier' en 'Backend' geconfigureerd om gedwongen tunneling te gebruiken.

## <a name="before-you-begin"></a><a name="before"></a>Voordat u begint

Installeer de meest recente versie van de PowerShell-cmdlets van Azure Resource Manager. Zie [How to install and configure Azure PowerShell](/powershell/azure/overview) (Azure PowerShell installeren en configureren) voor meer informatie over het installeren van de PowerShell-cmdlets.

> [!IMPORTANT]
> Het installeren van de nieuwste versie van de PowerShell-cmdlets is vereist. Anders u validatiefouten ontvangen bij het uitvoeren van een aantal van de cmdlets.
>
>

### <a name="to-log-in"></a>Inloggen

[!INCLUDE [To log in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="configure-forced-tunneling"></a>Geforceerde tunneling configureren

> [!NOTE]
> U ziet mogelijk waarschuwingen met de tekst "Het type uitvoerobject van deze cmdlet wordt in een toekomstige release gewijzigd". Dit is verwacht gedrag en u deze waarschuwingen veilig negeren.
>
>


1. Maak een resourcegroep.

   ```powershell
   New-AzResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
   ```
2. Maak een virtueel netwerk en geef subnetten op.

   ```powershell 
   $s1 = New-AzVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
   $s2 = New-AzVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
   $s3 = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
   $s4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
   $vnet = New-AzVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
   ```
3. Maak de lokale netwerkgateways.

   ```powershell
   $lng1 = New-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
   $lng2 = New-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
   $lng3 = New-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
   $lng4 = New-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
   ```
4. Maak de routetabel en routeregel.

   ```powershell
   New-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
   $rt = Get-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
   Add-AzRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
   Set-AzRouteTable -RouteTable $rt
   ```
5. Koppel de routetabel aan de subnetten Midtier en Backend.

   ```powershell
   $vnet = Get-AzVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
   Set-AzVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Maak de virtuele netwerkgateway. Deze stap duurt enige tijd om te voltooien, soms 45 minuten of meer, omdat u de gateway maakt en configureert. Als u ValidateSet-fouten ziet met betrekking tot de gatewaysku-waarde, controleert u of u de [nieuwste versie van de PowerShell-cmdlets](#before)hebt geïnstalleerd. De nieuwste versie van de PowerShell-cmdlets bevat de nieuwe gevalideerde waarden voor de nieuwste Gateway SKU's.

   ```powershell
   $pip = New-AzPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
   $gwsubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $ipconfig = New-AzVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
   New-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
   ```
7. Wijs een standaardsite toe aan de virtuele netwerkgateway. De **-GatewayDefaultSite** is de parameter cmdlet waarmee de geforceerde routeringsconfiguratie kan werken, dus zorg ervoor dat deze instelling goed wordt geconfigureerd. 

   ```powershell
   $LocalGateway = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
   $VirtualGateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
   ```
8. De SITE-to-Site VPN-verbindingen tot stand brengen.

   ```powershell
   $gateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   $lng1 = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
   $lng2 = Get-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
   $lng3 = Get-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
   $lng4 = Get-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
   New-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
   Get-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
   ```
