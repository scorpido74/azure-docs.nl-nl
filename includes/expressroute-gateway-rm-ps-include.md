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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176568"
---
De stappen voor deze taak gebruiken een VNet op basis van de waarden in de volgende configuratiereferentielijst. Aanvullende instellingen en namen worden ook in deze lijst beschreven. We gebruiken deze lijst niet rechtstreeks in een van de stappen, hoewel we variabelen toevoegen op basis van de waarden in deze lijst. U de lijst kopiëren om als referentie te gebruiken en de waarden vervangen door die van u.

* Virtuele netwerknaam = "TestVNet"
* Adresruimte voor virtuele netwerken = 192.168.0.0/16
* Resourcegroep = "TestRG"
* Subnet1-naam = "FrontEnd" 
* Subnet1-adresruimte = "192.168.1.0/24"
* Gateway Subnet naam: "GatewaySubnet" U moet altijd een gateway subnet *GatewaySubnet*noemen .
* Gateway Subnet adresruimte = "192.168.200.0/26"
* Regio = "Oost-VS"
* Gateway Naam = "GW"
* IP-naam gateway = "GWIP"
* Naam van gateway-IP-configuratie = "gwipconf"
* Type = "ExpressRoute" Dit type is vereist voor een ExpressRoute-configuratie.
* Gateway Public IP-naam = "gwpip"

## <a name="add-a-gateway"></a>Een gateway toevoegen
1. Maak verbinding met uw Azure-abonnement.

   [!INCLUDE [Sign in](expressroute-cloud-shell-connect.md)]
2. Verklaar uw variabelen voor deze oefening. Zorg ervoor dat u het voorbeeld bewerkt om de instellingen weer te geven die u wilt gebruiken.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
3. Sla het virtuele netwerkobject op als variabele.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
4. Voeg een gatewaysubnet toe aan uw virtuele netwerk. Het gatewaysubnet moet "GatewaySubnet" heten. U moet een gatewaysubnet maken dat /27 of groter is (/26, /25, enz.).

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
5. Stel de configuratie in.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Sla het gateway-subnet op als variabele.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
7. Vraag een openbaar IP-adres aan. Het IP-adres wordt opgevraagd voordat de gateway wordt gemaakt. U het IP-adres dat u wilt gebruiken niet opgeven. het is dynamisch toegewezen. U gebruikt dit IP-adres in de volgende configuratiesectie. De toewijzingsmethode moet dynamisch zijn.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
8. Maak de configuratie voor uw gateway. De gatewayconfiguratie bepaalt welk subnet en openbaar IP-adres moeten worden gebruikt. In deze stap geeft u de configuratie op die wordt gebruikt wanneer u de gateway maakt. Met deze stap wordt het gatewayobject niet gemaakt. Gebruik het voorbeeld hieronder om de gatewayconfiguratie te maken.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
9. Maak de gateway. In deze stap is het **-GatewayType** bijzonder belangrijk. U moet de waarde **ExpressRoute**gebruiken. Na het uitvoeren van deze cmdlets kan het 45 minuten of langer duren voordat de gateway is gemaakt.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```

## <a name="verify-the-gateway-was-created"></a>Controleer of de gateway is gemaakt
Gebruik de volgende opdrachten om te controleren of de gateway is gemaakt:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Het formaat van een gateway wijzigen
Er zijn een aantal [Gateway SKU's.](../articles/expressroute/expressroute-about-virtual-network-gateways.md) U de volgende opdracht gebruiken om de Gateway SKU op elk gewenst moment te wijzigen.

> [!IMPORTANT]
> Deze opdracht werkt niet voor UltraPerformance-gateway. Als u uw gateway wilt wijzigen naar een UltraPerformance-gateway, verwijdert u eerst de bestaande ExpressRoute-gateway en maakt u vervolgens een nieuwe UltraPerformance-gateway. Als u uw gateway wilt downgraden van een UltraPerformance-gateway, verwijdert u eerst de UltraPerformance-gateway en maakt u vervolgens een nieuwe gateway.
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
