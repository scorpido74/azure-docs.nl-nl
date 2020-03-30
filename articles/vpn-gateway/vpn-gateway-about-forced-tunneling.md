---
title: 'Azure VPN-gateway: geconfigureerd voor gedwongen tunneling - Site-to-Site-verbindingen: klassiek'
description: Hoe u al het internetgebonden verkeer omleiden of 'forceren' terug naar uw on-premises locatie.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: fe06257127ff352f68fb27d3507cee0229e31498
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201574"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Geforceerde tunneling met het klassieke implementatiemodel configureren

Met geforceerde tunneling kunt u alle voor internet bestemde verkeer geforceerd terugsturen naar uw on-premises locatie via een site-naar-site-VPN-tunnel voor inspectie en controle. Dit is een kritieke beveiligingsvereiste voor het meeste IT-beleid voor bedrijven. Zonder gedwongen tunneling zal internetgebonden verkeer van uw VM's in Azure altijd rechtstreeks vanuit de Azure-netwerkinfrastructuur naar het internet worden doorgestoofd, zonder de optie om het verkeer te inspecteren of te controleren. Ongeautoriseerde internettoegang kan mogelijk leiden tot openbaarmaking van informatie of andere vormen van inbreuken op de beveiliging.

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

In dit artikel u door het configureren van gedwongen tunneling voor virtuele netwerken die zijn gemaakt met behulp van het klassieke implementatiemodel. Geforceerde tunneling kan worden geconfigureerd met PowerShell, niet via de portal. Als u gedwongen tunneling voor het implementatiemodel Resourcebeheer wilt configureren, selecteert u het artikel Resourcemanager in de volgende vervolgkeuzelijst:

> [!div class="op_single_selector"]
> * [PowerShell - Klassiek](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell - Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="requirements-and-considerations"></a>Eisen en overwegingen
Geforceerde tunneling in Azure wordt geconfigureerd via udr (Virtual Network User Fined Routes). Het omleiden van verkeer naar een on-premises site wordt uitgedrukt als een standaardroute naar de Azure VPN-gateway. In de volgende sectie wordt de huidige beperking van de routeringstabel en -routes voor een Azure Virtual Network weergegeven:

* Elk virtueel netwerksubnet heeft een ingebouwde systeemrouteringstabel. De tabel systeemroutering heeft de volgende drie groepen routes:

  * **Lokale VNet-routes:** Rechtstreeks naar de bestemming VM's in hetzelfde virtuele netwerk.
  * **On-premises routes:** Naar de Azure VPN-gateway.
  * **Standaardroute:** Rechtstreeks naar het internet. Pakketten die bestemd zijn voor de privé-IP-adressen die niet onder de vorige twee routes vallen, worden verwijderd.
* Met het vrijgeven van door de gebruiker gedefinieerde routes u een routeringstabel maken om een standaardroute toe te voegen en vervolgens de routeringstabel koppelen aan uw VNet-subnet(s) om gedwongen tunneling op die subnetten mogelijk te maken.
* U moet een 'standaardsite' instellen tussen de lokale sites met een andere locatie die zijn aangesloten op het virtuele netwerk.
* Gedwongen tunneling moet worden gekoppeld aan een VNet met een dynamische routing VPN-gateway (geen statische gateway).
* ExpressRoute gedwongen tunneling is niet geconfigureerd via dit mechanisme, maar in plaats daarvan, wordt ingeschakeld door reclame voor een standaard route via de ExpressRoute BGP peering sessies. Zie de [ExpressRoute Documentatie](https://azure.microsoft.com/documentation/services/expressroute/) voor meer informatie.

## <a name="configuration-overview"></a>Configuratieoverzicht
In het volgende voorbeeld wordt het Frontend-subnet niet geforceerd getunneld. De workloads in het Frontend-subnet kunnen verzoeken van klanten via internet rechtstreeks blijven accepteren en beantwoorden. De Mid-tier en Backend subnetten worden gedwongen getunneld. Alle uitgaande verbindingen van deze twee subnetten naar het internet worden via een van de S2S VPN-tunnels geforceerd of omgeleid naar een on-premises site.

Hiermee u internettoegang vanaf uw virtuele machines of cloudservices in Azure beperken en inspecteren, terwijl u uw vereiste multi-tier servicearchitectuur blijft inschakelen. U ook gedwongen tunneling toepassen op de hele virtuele netwerken als er geen internetgerichte workloads in uw virtuele netwerken zijn.

![Gedwongen tunneling](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="before-you-begin"></a>Voordat u begint
Controleer of u beschikt over de volgende items voordat u begint met de configuratie:

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial/).
* Een geconfigureerd virtueel netwerk. 
* [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="to-sign-in"></a>Inloggen

1. Open uw PowerShell-console met verhoogde rechten. Als u wilt overschakelen naar servicebeheer, gebruikt u deze opdracht:

   ```powershell
   azure config mode asm
   ```
2. Maak verbinding met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

   ```powershell
   Add-AzureAccount
   ```

## <a name="configure-forced-tunneling"></a>Geforceerde tunneling configureren
Met de volgende procedure u gedwongen tunneling voor een virtueel netwerk opgeven. De configuratiestappen komen overeen met het VNet-netwerkconfiguratiebestand.

```xml
<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>
```

In dit voorbeeld heeft het virtuele netwerk 'MultiTier-VNet' drie subnetten: 'Frontend', 'Midtier' en 'Backend'-subnetten, met vier cross premises-verbindingen: 'DefaultSiteHQ' en drie branches. 

De stappen stellen de 'DefaultSiteHQ' in als de standaardsiteverbinding voor gedwongen tunneling en configureren de Midtier- en Backend-subnetten om gedwongen tunneling te gebruiken.

1. Maak een routeringstabel. Gebruik de volgende cmdlet om uw routetabel te maken.

   ```powershell
   New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
   ```

2. Voeg een standaardroute toe aan de routeringstabel. 

   In het volgende voorbeeld wordt een standaardroute toegevoegd aan de routeringstabel die is gemaakt in stap 1. Houd er rekening mee dat de enige route die wordt ondersteund het bestemmingsvoorvoegsel van "0.0.0.0/0" naar de "VPNGateway" NextHop is.

   ```powershell
   Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
   ```

3. Koppel de routeringstabel aan de subnetten. 

   Nadat een routeringstabel is gemaakt en een route is toegevoegd, gebruikt u het volgende voorbeeld om de routetabel toe te voegen of te koppelen aan een VNet-subnet. In het voorbeeld wordt de routetabel "MyRouteTable" toegevoegd aan de subnetten Midtier en Backend van VNet MultiTier-VNet.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
   ```

4. Wijs een standaardsite toe voor gedwongen tunneling. 

   In de vorige stap hebben de voorbeeldcmdletscripts de routeringstabel gemaakt en de routetabel gekoppeld aan twee van de VNet-subnetten. De resterende stap is het selecteren van een lokale site tussen de multi-site verbindingen van het virtuele netwerk als de standaard site of tunnel.

   ```powershell
   $DefaultSite = @("DefaultSiteHQ")
   Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
   ```

## <a name="additional-powershell-cmdlets"></a>Extra PowerShell-cmdlets
### <a name="to-delete-a-route-table"></a>Een routetabel verwijderen

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>Een routetabel aanbieden

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>Een route uit een routetabel verwijderen

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>Een route uit een subnet verwijderen

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>De routetabel aaneengeven die is gekoppeld aan een subnet

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Een standaardsite verwijderen uit een VNet VPN-gateway

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```
