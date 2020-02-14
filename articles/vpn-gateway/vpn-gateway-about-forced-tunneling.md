---
title: 'Azure VPN Gateway: geforceerde tunneling configureren-site-naar-site-verbindingen: klassiek'
description: Het omleiden of 'afdwingen' alle internetverkeer terug naar uw on-premises locatie.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: fe06257127ff352f68fb27d3507cee0229e31498
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201574"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Geforceerde tunneling met het klassieke implementatiemodel configureren

Met geforceerde tunneling kunt u alle voor internet bestemde verkeer geforceerd terugsturen naar uw on-premises locatie via een site-naar-site-VPN-tunnel voor inspectie en controle. Dit is een essentiële vereiste voor de meeste bedrijven beleid. Zonder geforceerde tunnels wordt via internetverkeer van uw virtuele machines in Azure altijd van Azure network-infrastructuur rechtstreeks uit met het Internet, zonder de optie waarmee u kunt controleren of het verkeer controleren. Niet-geautoriseerde toegang tot Internet kan mogelijk leiden tot vrijgeven van informatie of andere typen schendingen van de beveiliging.

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

In dit artikel begeleidt u bij het configureren geforceerde tunneling voor virtuele netwerken die zijn gemaakt met het klassieke implementatiemodel. Geforceerde tunneling kan worden geconfigureerd met behulp van PowerShell, niet via de portal. Als u geforceerde tunneling wilt configureren voor het Resource Manager-implementatie model, selecteert u Resource Manager-artikel in de volgende vervolg keuzelijst:

> [!div class="op_single_selector"]
> * [PowerShell - Klassiek](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell - Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="requirements-and-considerations"></a>Vereisten en overwegingen
Geforceerde tunneling in Azure wordt geconfigureerd via het virtuele netwerk zelfgedefinieerde routes (UDR). Verkeer omleiden naar een on-premises site wordt uitgedrukt als een standaard-Route naar de Azure VPN-gateway. Het volgende gedeelte bevat de huidige beperking van de tabel en routes de routering voor een Azure-netwerk:

* Elk virtueel netwerksubnet heeft een ingebouwde, systeem-routeringstabel. De routeringstabel van het systeem heeft de volgende drie groepen van routes:

  * **Lokale VNet-routes:** Rechtstreeks naar de doel-Vm's in hetzelfde virtuele netwerk.
  * **On-premises routes:** Naar de Azure VPN-gateway.
  * **Standaard route:** Rechtstreeks op internet. Pakketten dat is bestemd voor het particuliere IP-adressen niet wordt gedekt door de vorige twee routes verwijderd.
* U kunt met de release van de gebruiker gedefinieerde routes maken een routeringstabel om toe te voegen een standaardroute en koppel vervolgens de routeringstabel naar de subnetten van uw VNet om in te schakelen geforceerde tunneling op deze subnetten.
* U moet een 'standaard-site"tussen de cross-premises lokale sites die zijn verbonden met het virtuele netwerk instellen.
* Geforceerde tunneling moet worden gekoppeld aan een VNet met een VPN-gateway voor dynamische routering (niet een statische gateway genoemd).
* ExpressRoute geforceerde tunneling is niet geconfigureerd via dit mechanisme, maar in plaats daarvan wordt ingeschakeld door kondigt een standaardroute via de ExpressRoute-BGP-peeringsessies. Raadpleeg de [ExpressRoute-documentatie](https://azure.microsoft.com/documentation/services/expressroute/) voor meer informatie.

## <a name="configuration-overview"></a>Configuratieoverzicht
In het volgende voorbeeld wordt tunnel de front-end subnet niet gedwongen. De werkbelastingen in het subnet Frontend kunnen blijven om te accepteren en rechtstreeks reageren op aanvragen van klanten via Internet. De middelste laag en back-end-subnetten zijn geforceerde tunnels. Uitgaande verbindingen vanuit deze twee subnetten met het Internet wordt afgedwongen of keert u terug naar een on-premises site via een van de S2S VPN-tunnels.

Hiermee kunt u om te beperken en controleren van toegang tot het Internet van uw virtuele machines of cloudservices in Azure, terwijl u verdergaat met het inschakelen van uw service met meerdere lagen architectuur die is vereist. Ook kunt u toepassen geforceerde tunneling naar de volledige virtuele netwerken als er geen internetverbinding werkbelastingen in uw virtuele netwerken.

![Geforceerde tunneling](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="before-you-begin"></a>Voordat u begint
Controleer of u beschikt over de volgende items voordat u begint met de configuratie:

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial/).
* Een geconfigureerde virtueel netwerk. 
* [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="to-sign-in"></a>Aanmelden

1. Open de Power shell-console met verhoogde bevoegdheden. Als u wilt overschakelen naar Service beheer, gebruikt u deze opdracht:

   ```powershell
   azure config mode asm
   ```
2. Maak verbinding met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

   ```powershell
   Add-AzureAccount
   ```

## <a name="configure-forced-tunneling"></a>Geforceerde tunneling configureren
De volgende procedure ziet u een geforceerde tunnels voor een virtueel netwerk opgeven. De configuratiestappen komen overeen met het configuratiebestand van de VNet-netwerk.

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

In dit voorbeeld is het virtuele netwerk MultiTier-VNet drie subnetten: 'Frontend', 'Midtier' en 'back-end-subnetten, met vier cross-premises verbindingen: 'DefaultSiteHQ' en drie vertakkingen. 

De stappen wordt de 'DefaultSiteHQ' ingesteld als de standaard-site-verbinding voor geforceerde tunneling en het Midtier configureren en back-end-subnetten gebruik van geforceerde tunneling.

1. Maak een routeringstabel. Gebruik de volgende cmdlet om de routetabel te maken.

   ```powershell
   New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
   ```

2. Een standaard-route toevoegen aan de routeringstabel. 

   Het volgende voorbeeld wordt een standaard-route toegevoegd aan de routeringstabel in stap 1 hebt gemaakt. Houd er rekening mee dat de route alleen wordt ondersteund, is het voorvoegsel voor bestemming '0.0.0.0/0' aan de 'VPN-gateway' volgende hop.

   ```powershell
   Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
   ```

3. De routeringstabel naar de subnetten koppelen. 

   Nadat een routeringstabel wordt gemaakt en een route toegevoegd, gebruikt u het volgende voorbeeld toe te voegen of de routetabel aan een VNet-subnet koppelen. Het voorbeeld wordt de routetabel 'MyRouteTable' toegevoegd aan het Midtier- en back-end-subnetten van VNet MultiTier-VNet.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
   ```

4. Een standaardsite toewijzen voor geforceerde tunneling. 

   In de vorige stap, de cmdlet voorbeeldscripts gemaakt van de routeringstabel en de routetabel op twee van de VNet-subnetten die zijn gekoppeld. De laatste stap is om te selecteren van een lokale site tussen de verbindingen van meerdere sites van het virtuele netwerk als de standaard-site of -tunnel.

   ```powershell
   $DefaultSite = @("DefaultSiteHQ")
   Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
   ```

## <a name="additional-powershell-cmdlets"></a>Andere PowerShell-cmdlets
### <a name="to-delete-a-route-table"></a>Een routetabel verwijderen

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>Om een routetabel weer te geven

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>Een route van een routetabel verwijderen

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>Een route van een subnet verwijderen

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Om de routetabel die zijn gekoppeld aan een subnet weer te geven

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Een standaard-site verwijderen uit een VNet-VPN-gateway

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```
