---
title: bestand opnemen
description: bestand opnemen
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 12/13/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 70ac106995324c758bde942d12191a01e3457e6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175769"
---
> [!NOTE]
> Deze voorbeelden zijn niet van toepassing op s2S/ExpressRoute naast elkaar bestaanconfiguraties.
> Zie Naast elkaar bestaande verbindingen configureren voor meer informatie over het werken met gateways in een naast elkaar bestaande [configuratie.](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Een gateway toevoegen

Wanneer u een gateway toevoegt aan een virtueel netwerk met behulp van het klassieke resourcemodel, wijzigt u het netwerkconfiguratiebestand rechtstreeks voordat u de gateway maakt. De waarden in de onderstaande voorbeelden moeten aanwezig zijn in het bestand om een gateway te maken. Als er voorheen een gateway aan uw virtuele netwerk was gekoppeld, zijn sommige van deze waarden al aanwezig. Wijzig het bestand om de onderstaande waarden weer te geven.

### <a name="download-the-network-configuration-file"></a>Het netwerkconfiguratiebestand downloaden

1. Download het netwerkconfiguratiebestand met de stappen in het artikel [over netwerkconfiguratiebestanden.](../articles/virtual-network/virtual-networks-using-network-configuration-file.md) Open het bestand met een teksteditor.
2. Voeg een lokale netwerksite toe aan het bestand. U elk geldig adresvoorvoegsel gebruiken. U elk geldig IP-adres voor de VPN-gateway toevoegen. De adreswaarden in deze sectie worden niet gebruikt voor ExpressRoute-bewerkingen, maar zijn vereist voor bestandsvalidatie. In het voorbeeld is 'branch1' de naam van de site. U een andere naam gebruiken, maar zorg ervoor dat u dezelfde waarde gebruikt in het gatewaygedeelte van het bestand.

   ```
   <VirtualNetworkConfiguration>
    <Dns />
    <LocalNetworkSites>
      <LocalNetworkSite name="branch1">
        <AddressSpace>
          <AddressPrefix>165.3.1.0/27</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>3.2.1.4</VPNGatewayAddress>
    </LocalNetworkSite>
   ```
3. Navigeer naar de VirtualNetworkSites en wijzig de velden.

   * Controleer of het gatewaysubnet bestaat voor uw virtuele netwerk. Als dit niet het zo is, u er op dit moment een toevoegen. De naam moet "GatewaySubnet" zijn.
   * Controleer of het gedeelte Gateway van het bestand bestaat. Als dat niet zo is, voeg het dan toe. Dit is nodig om het virtuele netwerk te koppelen aan de lokale netwerksite (die het netwerk vertegenwoordigt waarmee u verbinding maakt).
   * Controleer of het verbindingstype = Dedicated. Dit is vereist voor ExpressRoute-verbindingen.

   ```
   </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myAzureVNET" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="default">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.1.0/27</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="branch1">
              <Connection type="Dedicated" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
   </VirtualNetworkConfiguration>
   </NetworkConfiguration>
   ```
4. Sla het bestand op en upload het naar Azure.

### <a name="create-the-gateway"></a>De gateway maken

Gebruik de onderstaande opdracht om een gateway te maken. Vervang alle waarden voor je eigen waarden.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Controleer of de gateway is gemaakt

Gebruik de onderstaande opdracht om te controleren of de gateway is gemaakt. Met deze opdracht wordt ook de gateway-id opgehaald, die u nodig hebt voor andere bewerkingen.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Het formaat van een gateway wijzigen

Er zijn een aantal [Gateway SKU's.](../articles/expressroute/expressroute-about-virtual-network-gateways.md) U de volgende opdracht gebruiken om de Gateway SKU op elk gewenst moment te wijzigen.

> [!IMPORTANT]
> Deze opdracht werkt niet voor UltraPerformance-gateway. Als u uw gateway wilt wijzigen naar een UltraPerformance-gateway, verwijdert u eerst de bestaande ExpressRoute-gateway en maakt u vervolgens een nieuwe UltraPerformance-gateway. Als u uw gateway wilt downgraden van een UltraPerformance-gateway, verwijdert u eerst de UltraPerformance-gateway en maakt u vervolgens een nieuwe gateway.
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Een gateway verwijderen

De onderstaande opdracht gebruiken om een gateway te verwijderen

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```