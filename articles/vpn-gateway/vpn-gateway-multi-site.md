---
title: 'Een VNet verbinden met meerdere sites via VPN Gateway: Classic'
description: Verbind meerdere lokale on-premises sites met een klassiek virtueel netwerk met behulp van een VPN-gateway.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/11/2020
ms.author: yushwang
ms.openlocfilehash: a95cd6ea85a16b0e0bf5f67f5dfc20d57f11463b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198089"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Een Site-to-Site-verbinding toevoegen aan een VNet met een bestaande VPN-gatewayverbinding (klassiek)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klassiek)](vpn-gateway-multi-site.md)
>
>

In dit artikel u powershell gebruiken om Site-to-Site -verbindingen toe te voegen aan een VPN-gateway met een bestaande verbinding. Dit type verbinding wordt vaak aangeduid als een "multi-site" configuratie. De stappen in dit artikel zijn van toepassing op virtuele netwerken die zijn gemaakt met behulp van het klassieke implementatiemodel (ook wel Servicebeheer genoemd). Deze stappen zijn niet van toepassing op samenbestaande verbindingsconfiguraties van ExpressRoute/Site-to-Site.

### <a name="deployment-models-and-methods"></a>Implementatiemodellen en -methoden

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

We werken deze tabel bij als er nieuwe artikelen en extra tools beschikbaar komen voor deze configuratie. Wanneer een artikel beschikbaar is, koppelen we er rechtstreeks naar vanuit deze tabel.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>Over verbinding maken

U meerdere on-premises sites verbinden met één virtueel netwerk. Dit is vooral aantrekkelijk voor het bouwen van hybride cloudoplossingen. Het maken van een verbinding met meerdere plaatsen met uw Azure-virtuele netwerkgateway is vergelijkbaar met het maken van andere site-to-site-verbindingen. In feite u een bestaande Azure VPN-gateway gebruiken, zolang de gateway dynamisch is (routegebaseerd).

Als u al een statische gateway hebt aangesloten op uw virtuele netwerk, u het gatewaytype wijzigen in dynamisch zonder dat u het virtuele netwerk hoeft opnieuw op te bouwen om plaats te bieden aan meerdere servers. Voordat u het routetype wijzigt, moet u ervoor zorgen dat uw on-premises VPN-gateway routegebaseerde VPN-configuraties ondersteunt.

![diagram met meerdere plaatsen](./media/vpn-gateway-multi-site/multisite.png "meerdere site's")

## <a name="points-to-consider"></a>Punten om in overweging te nemen

**U de portal niet gebruiken om wijzigingen aan te brengen in dit virtuele netwerk.** U moet wijzigingen aanbrengen in het netwerkconfiguratiebestand in plaats van de portal te gebruiken. Als u wijzigingen aanbrengt in de portal, overschrijven ze uw referentie-instellingen voor meerdere plaatsen voor dit virtuele netwerk.

U moet zich comfortabel voelen met het netwerkconfiguratiebestand tegen de tijd dat u de procedure met meerdere plaatsen hebt voltooid. Als u echter meerdere mensen hebt die aan uw netwerkconfiguratie werken, moet u ervoor zorgen dat iedereen op de hoogte is van deze beperking. Dit betekent niet dat je het portaal helemaal niet gebruiken. U het voor al het andere gebruiken, behalve het aanbrengen van configuratiewijzigingen in dit specifieke virtuele netwerk.

## <a name="before-you-begin"></a>Voordat u begint

Controleer voordat u met de configuratie begint of u het volgende hebt:

* Compatibele VPN-hardware voor elke on-premises locatie. Controleer [Over VPN-apparaten voor virtuele netwerkconnectiviteit](vpn-gateway-about-vpn-devices.md) om te controleren of het apparaat dat u wilt gebruiken iets is waarvan bekend is dat het compatibel is.
* Een extern gericht openbaar IPv4 IP-adres voor elk VPN-apparaat. Het IP-adres kan niet achter een NAT worden geplaatst. Dit is vereiste.
* Iemand die bedreven is in het configureren van uw VPN-hardware. Je moet een goed begrip hebben van hoe je je VPN-apparaat configureren, of werken met iemand die dat wel doet.
* De IP-adresbereiken die u wilt gebruiken voor uw virtuele netwerk (als u er nog geen hebt gemaakt).
* De IP-adresbereiken voor elk van de lokale netwerksites waarmee u verbinding maakt. U moet ervoor zorgen dat de IP-adresbereiken voor elk van de lokale netwerksites waarmee u verbinding wilt maken, elkaar niet overlappen. Anders weigert de portal of de REST API de configuratie die wordt geüpload.<br>Als u bijvoorbeeld twee lokale netwerksites hebt die beide het IP-adresbereik 10.2.3.0/24 bevatten en u een pakket hebt met een doeladres 10.2.3.3, weet Azure niet naar welke site u het pakket wilt verzenden omdat de adresbereiken elkaar overlappen. Om routeringsproblemen te voorkomen, staat Azure u niet toe een configuratiebestand te uploaden met overlappende bereiken.

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="1-create-a-site-to-site-vpn"></a>1. Een site-to-site VPN maken
Als je al een Site-to-Site VPN hebt met een dynamische routing gateway, geweldig! U doorgaan met [Het exporteren van de instellingen voor virtuele netwerkconfiguratie](#export). Zo niet, ga dan als volgt te werk:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Als u al een virtueel netwerk van site-naar-site hebt, maar een statische (op beleid gebaseerde) routeringsgateway heeft:
1. Wijzig uw gatewaytype in dynamische routering. Een multi-site VPN vereist een dynamische (ook wel route-gebaseerde) routing gateway. Als u het gatewaytype wilt wijzigen, moet u eerst de bestaande gateway verwijderen en vervolgens een nieuwe gateway maken.
2. Configureer uw nieuwe gateway en maak uw VPN-tunnel. Zie Voor instructies Voor instructies [het SKU- en VPN-type opgeven](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Zorg ervoor dat u het routeringstype opgeeft als 'Dynamisch'.

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Als u geen virtueel netwerk van site-naar-site hebt:
1. Maak uw site-to-site virtuele netwerk met behulp van deze instructies: [Maak een virtueel netwerk met een Site-to-Site VPN-verbinding](vpn-gateway-site-to-site-create.md).  
2. Een dynamische routeringgateway configureren met behulp van deze instructies: [Een VPN-gateway configureren.](vpn-gateway-configure-vpn-gateway-mp.md) Selecteer dynamische **routering** voor uw gatewaytype.

## <a name="2-export-the-network-configuration-file"></a><a name="export"></a>2. Het netwerkconfiguratiebestand exporteren

Open uw PowerShell-console met verhoogde rechten. Als u wilt overschakelen naar servicebeheer, gebruikt u deze opdracht:

```powershell
azure config mode asm
```

Maak verbinding met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

```powershell
Add-AzureAccount
```

Exporteer uw Azure-netwerkconfiguratiebestand door de volgende opdracht uit te voeren. U de locatie van het bestand wijzigen om indien nodig naar een andere locatie te exporteren.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Het netwerkconfiguratiebestand openen
Open het netwerkconfiguratiebestand dat u in de laatste stap hebt gedownload. Gebruik elke xml-editor die u leuk vindt. Het bestand moet er hetzelfde uitzien als het volgende:

        <NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <LocalNetworkSites>
              <LocalNetworkSite name="Site1">
                <AddressSpace>
                  <AddressPrefix>10.0.0.0/16</AddressPrefix>
                  <AddressPrefix>10.1.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
              </LocalNetworkSite>
              <LocalNetworkSite name="Site2">
                <AddressSpace>
                  <AddressPrefix>10.2.0.0/16</AddressPrefix>
                  <AddressPrefix>10.3.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
              </LocalNetworkSite>
            </LocalNetworkSites>
            <VirtualNetworkSites>
              <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
                <AddressSpace>
                  <AddressPrefix>10.20.0.0/16</AddressPrefix>
                  <AddressPrefix>10.21.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="FE">
                    <AddressPrefix>10.20.0.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="BE">
                    <AddressPrefix>10.20.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.20.2.0/29</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="Site1">
                      <Connection type="IPsec" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

## <a name="4-add-multiple-site-references"></a>4. Meerdere sitereferenties toevoegen
Wanneer u informatie over siteverwijzingen toevoegt of verwijdert, brengt u configuratiewijzigingen aan in de ConnectionsToLocalNetwork/LocalNetworkSiteRef. Als u een nieuwe lokale siteverwijzing toevoegt, wordt Azure geactiveerd om een nieuwe tunnel te maken. In het onderstaande voorbeeld is de netwerkconfiguratie voor een verbinding met één site. Sla het bestand op zodra u klaar bent met het aanbrengen van wijzigingen.

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

Als u extra sitereferenties wilt toevoegen (een configuratie met meerdere locaties maken), voegt u gewoon extra 'LocalNetworkSiteRef'-regels toe, zoals in het onderstaande voorbeeld wordt weergegeven:

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. Het netwerkconfiguratiebestand importeren
Het netwerkconfiguratiebestand importeren. Wanneer u dit bestand importeert met de wijzigingen, worden de nieuwe tunnels toegevoegd. De tunnels maken gebruik van de dynamische gateway die u eerder hebt gemaakt. U PowerShell gebruiken om het bestand te importeren.

## <a name="6-download-keys"></a>6. Downloadtoetsen
Zodra uw nieuwe tunnels zijn toegevoegd, gebruikt u de PowerShell-cmdlet 'Get-AzureVNetGatewayKey' om de vooraf gedeelde iPsec/IKE-sleutels voor elke tunnel te krijgen.

Bijvoorbeeld:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Als u dat liever hebt, u ook de Api Voor gedeelde sleutel rest van de *virtuele netwerkgateway* gebruiken om de vooraf gedeelde sleutels te krijgen.

## <a name="7-verify-your-connections"></a>7. Uw verbindingen verifiëren
Controleer de status van de tunnel met meerdere plaatsen. Nadat u de sleutels voor elke tunnel hebt gedownload, wilt u de verbindingen verifiëren. Gebruik 'Get-AzureVnetConnection' om een lijst met virtuele netwerktunnels te krijgen, zoals in het onderstaande voorbeeld wordt weergegeven. VNet1 is de naam van de VNet.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

Voorbeeld retour:

```
    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded
```

## <a name="next-steps"></a>Volgende stappen

Zie Over VPN-gateways voor meer informatie over [VPN-gateways.](vpn-gateway-about-vpngateways.md)
