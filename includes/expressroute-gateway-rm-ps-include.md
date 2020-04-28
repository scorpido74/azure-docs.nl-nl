---
title: bestand opnemen
description: bestand opnemen
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 02/21/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 922ac7eb6cb9676af65700a6a2fe7fbae35a0dc5
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67176568"
---
De stappen voor deze taak gebruiken een VNet op basis van de waarden in de volgende configuratie referentie lijst. Aanvullende instellingen en namen worden ook beschreven in deze lijst. Deze lijst wordt niet rechtstreeks in een van de stappen gebruikt, hoewel we variabelen toevoegen op basis van de waarden in deze lijst. U kunt de lijst kopiëren om deze als referentie te gebruiken, waarbij u de waarden vervangt door uw eigen waarde.

* Virtual Network naam = "TestVNet"
* Virtual Network adres ruimte = 192.168.0.0/16
* Resource groep = "TestRG"
* Subnet1 name = "front-end" 
* Subnet1-adres ruimte = "192.168.1.0/24"
* Subnetnaam gateway: "GatewaySubnet" u moet altijd de naam van een gateway-subnet *GatewaySubnet*.
* Adres ruimte gateway-subnet = "192.168.200.0/26"
* Regio = "VS-Oost"
* Gateway naam = "GW"
* IP-naam van Gateway = "GWIP"
* Naam van IP-configuratie van Gateway = "gwipconf"
* Type = "ExpressRoute" dit type is vereist voor een ExpressRoute-configuratie.
* Open bare IP-naam van Gateway = "gwpip"

## <a name="add-a-gateway"></a>Een gateway toevoegen
1. Maak verbinding met uw Azure-abonnement.

   [!INCLUDE [Sign in](expressroute-cloud-shell-connect.md)]
2. Declareer de variabelen voor deze oefening. Zorg ervoor dat u het voor beeld bewerkt met de instellingen die u wilt gebruiken.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
3. Sla het object van het virtuele netwerk op als een variabele.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
4. Voeg een gateway-subnet toe aan uw Virtual Network. Het gateway-subnet moet de naam ' GatewaySubnet ' hebben. U moet een gateway-subnet maken van/27 of groter (/26,/25, enzovoort).

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
5. Stel de configuratie in.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Sla het subnet van de gateway op als een variabele.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
7. Vraag een openbaar IP-adres aan. Het IP-adres wordt aangevraagd voordat de gateway wordt gemaakt. U kunt het IP-adres dat u wilt gebruiken niet opgeven. het wordt dynamisch toegewezen. U gebruikt dit IP-adres in de volgende configuratiesectie. De AllocationMethod moet dynamisch zijn.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
8. De configuratie voor uw gateway maken. De gatewayconfiguratie bepaalt welk subnet en openbaar IP-adres moeten worden gebruikt. In deze stap geeft u de configuratie op die wordt gebruikt bij het maken van de gateway. Met deze stap maakt u niet echt het gateway-object. Gebruik het voorbeeld hieronder om de gatewayconfiguratie te maken.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
9. Maak de gateway. In deze stap is de **-Gateway type** vooral belang rijk. U moet de waarde **ExpressRoute**gebruiken. Na het uitvoeren van deze cmdlets kan de gateway 45 minuten of langer duren om te maken.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```

## <a name="verify-the-gateway-was-created"></a>Controleren of de gateway is gemaakt
Gebruik de volgende opdrachten om te controleren of de gateway is gemaakt:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Het formaat van een gateway wijzigen
Er zijn een aantal [Gateway-sku's](../articles/expressroute/expressroute-about-virtual-network-gateways.md). U kunt de volgende opdracht gebruiken om de gateway-SKU op elk gewenst moment te wijzigen.

> [!IMPORTANT]
> Deze opdracht werkt niet voor de Ultra Performance-gateway. Als u uw gateway wilt wijzigen in een Ultra Performance-gateway, verwijdert u eerst de bestaande ExpressRoute-gateway en maakt u vervolgens een nieuwe Ultra Performance-gateway. Als u uw gateway wilt downgradeen van een Ultra Performance-gateway, verwijdert u eerst de Ultra Performance-gateway en maakt u vervolgens een nieuwe gateway.
> 
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>Een gateway verwijderen
Gebruik de volgende opdracht om een gateway te verwijderen:

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```
