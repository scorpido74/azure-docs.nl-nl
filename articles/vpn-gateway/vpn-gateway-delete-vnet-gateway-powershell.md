---
title: 'Azure VPN-gateway: een gateway verwijderen: PowerShell'
description: Een virtuele netwerkgateway verwijderen met PowerShell in het implementatiemodel Resource Manager.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 02/07/2019
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: f351f14796ec736bd5525f139a518c9a0dd3d19f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162103"
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Een virtuele netwerkgateway verwijderen met PowerShell
> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Powershell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klassiek)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Er zijn een paar verschillende benaderingen die u nemen wanneer u een virtuele netwerkgateway voor een VPN-gateway-configuratie wilt verwijderen.

- Als u alles wilt verwijderen en opnieuw wilt beginnen, zoals in het geval van een testomgeving, u de brongroep verwijderen. Wanneer u een brongroep verwijdert, worden alle bronnen binnen de groep verwijderd. Dit is de methode wordt alleen aanbevolen als u geen van de resources in de resourcegroep wilt behouden. U niet selectief verwijderen slechts een paar middelen met behulp van deze aanpak.

- Als u een deel van de resources in uw resourcegroep wilt behouden, wordt het verwijderen van een virtuele netwerkgateway iets ingewikkelder. Voordat u de virtuele netwerkgateway verwijderen, moet u eerst bronnen verwijderen die afhankelijk zijn van de gateway. Welke stappen u volgt, is afhankelijk van het type verbindingen dat u hebt gemaakt en de afhankelijke resources voor elke verbinding.

## <a name="before-beginning"></a>Voordat u begint



### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Download de nieuwste Azure Resource Manager PowerShell-cmdlets.

Download en installeer de nieuwste versie van de PowerShell-cmdlets van Azure Resource Manager. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview)voor meer informatie over het downloaden en installeren van PowerShell-cmdlets.

### <a name="2-connect-to-your-azure-account"></a>2. Maak verbinding met uw Azure-account.

Open de PowerShell-console en maak verbinding met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

```powershell
Connect-AzAccount
```

Controleer de abonnementen voor het account.

```powershell
Get-AzSubscription
```

Als u meer dan één abonnement hebt, geeft u het abonnement op dat u wilt gebruiken.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="delete-a-site-to-site-vpn-gateway"></a><a name="S2S"></a>Een Site-to-Site VPN-gateway verwijderen

Als u een virtuele netwerkgateway voor een S2S-configuratie wilt verwijderen, moet u eerst elke bron verwijderen die betrekking heeft op de virtuele netwerkgateway. Resources moeten in een bepaalde volgorde worden verwijderd vanwege afhankelijkheden. Bij het werken met de onderstaande voorbeelden moeten sommige waarden worden opgegeven, terwijl andere waarden een uitvoerresultaat zijn. We gebruiken de volgende specifieke waarden in de voorbeelden voor demonstratiedoeleinden:

VNet-naam: VNet1<br>
Naam resourcegroep: RG1<br>
Naam virtuele netwerkgateway: GW1<br>

De volgende stappen zijn van toepassing op het implementatiemodel resourcebeheer.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Download de virtuele netwerkgateway die u wilt verwijderen.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Controleer of de virtuele netwerkgateway verbindingen heeft.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. Verwijder alle verbindingen.

Mogelijk wordt u gevraagd om het verwijderen van elk van de verbindingen te bevestigen.

```powershell
$Conns | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. Verwijder de virtuele netwerkgateway.

Mogelijk wordt u gevraagd de verwijdering van de gateway te bevestigen. Als u naast uw S2S-configuratie een P2S-configuratie hebt voor deze VNet, wordt de verbinding met de virtuele netwerkgateway automatisch verbroken door alle P2S-clients zonder waarschuwing los te koppelen.


```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Op dit moment is uw virtuele netwerkgateway verwijderd. U de volgende stappen gebruiken om resources te verwijderen die niet meer worden gebruikt.

### <a name="5-delete-the-local-network-gateways"></a>5 Verwijder de lokale netwerkgateways.

Download de lijst met de bijbehorende lokale netwerkgateways.

```powershell
$LNG=Get-AzLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Verwijder de lokale netwerkgateways. Mogelijk wordt u gevraagd om de verwijdering van elk van de lokale netwerkgateway te bevestigen.

```powershell
$LNG | ForEach-Object {Remove-AzLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. Verwijder de bronnen voor openbare IP-adres.

Download de IP-configuraties van de virtuele netwerkgateway.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Download de lijst met bronnen met openbare IP-adresbronnen die worden gebruikt voor deze virtuele netwerkgateway. Als de virtuele netwerkgateway actief actief was, ziet u twee openbare IP-adressen.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Verwijder de openbare IP-bronnen.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Verwijder het gatewaysubnet en stel de configuratie in.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vnet-to-vnet-vpn-gateway"></a><a name="v2v"></a>Een VNet-naar-VNet VPN-gateway verwijderen

Als u een virtuele netwerkgateway voor een V2V-configuratie wilt verwijderen, moet u eerst elke bron verwijderen die betrekking heeft op de virtuele netwerkgateway. Resources moeten in een bepaalde volgorde worden verwijderd vanwege afhankelijkheden. Bij het werken met de onderstaande voorbeelden moeten sommige waarden worden opgegeven, terwijl andere waarden een uitvoerresultaat zijn. We gebruiken de volgende specifieke waarden in de voorbeelden voor demonstratiedoeleinden:

VNet-naam: VNet1<br>
Naam resourcegroep: RG1<br>
Naam virtuele netwerkgateway: GW1<br>

De volgende stappen zijn van toepassing op het implementatiemodel resourcebeheer.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Download de virtuele netwerkgateway die u wilt verwijderen.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Controleer of de virtuele netwerkgateway verbindingen heeft.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Er kunnen andere verbindingen zijn met de virtuele netwerkgateway die deel uitmaken van een andere brongroep. Controleer op extra verbindingen in elke aanvullende brongroep. In dit voorbeeld controleren we op verbindingen van RG2. Voer dit uit voor elke resourcegroep die u hebt en die mogelijk een verbinding heeft met de virtuele netwerkgateway.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. Download de lijst met verbindingen in beide richtingen.

Omdat dit een VNet-to-VNet-configuratie is, hebt u de lijst met verbindingen in beide richtingen nodig.

```powershell
$ConnsL=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
In dit voorbeeld controleren we op verbindingen van RG2. Voer dit uit voor elke resourcegroep die u hebt en die mogelijk een verbinding heeft met de virtuele netwerkgateway.

```powershell
 $ConnsR=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. Verwijder alle verbindingen.

Mogelijk wordt u gevraagd om het verwijderen van elk van de verbindingen te bevestigen.

```powershell
$ConnsL | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. Verwijder de virtuele netwerkgateway.

Mogelijk wordt u gevraagd de verwijdering van de virtuele netwerkgateway te bevestigen. Als u naast uw V2V-configuratie Ook P2S-configuraties op uw V2V-configuratie hebt, wordt het verwijderen van de virtuele netwerkgateways automatisch alle P2S-clients zonder waarschuwing verbroken.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Op dit moment is uw virtuele netwerkgateway verwijderd. U de volgende stappen gebruiken om resources te verwijderen die niet meer worden gebruikt.

### <a name="6-delete-the-public-ip-address-resources"></a>6. De bronnen voor openbare IP-adres verwijderen

Download de IP-configuraties van de virtuele netwerkgateway.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Download de lijst met bronnen met openbare IP-adresbronnen die worden gebruikt voor deze virtuele netwerkgateway. Als de virtuele netwerkgateway actief actief was, ziet u twee openbare IP-adressen.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Verwijder de openbare IP-bronnen. Mogelijk wordt u gevraagd om het verwijderen van het openbare IP-adres te bevestigen.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Verwijder het gatewaysubnet en stel de configuratie in.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-point-to-site-vpn-gateway"></a><a name="deletep2s"></a>Een Point-to-Site VPN-gateway verwijderen

Als u een virtuele netwerkgateway voor een P2S-configuratie wilt verwijderen, moet u eerst elke bron verwijderen die betrekking heeft op de virtuele netwerkgateway. Resources moeten in een bepaalde volgorde worden verwijderd vanwege afhankelijkheden. Bij het werken met de onderstaande voorbeelden moeten sommige waarden worden opgegeven, terwijl andere waarden een uitvoerresultaat zijn. We gebruiken de volgende specifieke waarden in de voorbeelden voor demonstratiedoeleinden:

VNet-naam: VNet1<br>
Naam resourcegroep: RG1<br>
Naam virtuele netwerkgateway: GW1<br>

De volgende stappen zijn van toepassing op het implementatiemodel resourcebeheer.


>[!NOTE]
> Wanneer u de VPN-gateway verwijdert, worden alle verbonden clients zonder waarschuwing losgekoppeld van het VNet.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Download de virtuele netwerkgateway die u wilt verwijderen.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. Verwijder de virtuele netwerkgateway.

Mogelijk wordt u gevraagd de verwijdering van de virtuele netwerkgateway te bevestigen.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Op dit moment is uw virtuele netwerkgateway verwijderd. U de volgende stappen gebruiken om resources te verwijderen die niet meer worden gebruikt.

### <a name="3-delete-the-public-ip-address-resources"></a>3. De bronnen voor openbare IP-adres verwijderen

Download de IP-configuraties van de virtuele netwerkgateway.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Download de lijst met openbare IP-adressen die worden gebruikt voor deze virtuele netwerkgateway. Als de virtuele netwerkgateway actief actief was, ziet u twee openbare IP-adressen.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Verwijder de openbare IP's. Mogelijk wordt u gevraagd om het verwijderen van het openbare IP-adres te bevestigen.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. Verwijder het gatewaysubnet en stel de configuratie in.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="delete"></a>Een VPN-gateway verwijderen door de brongroep te verwijderen

Als u zich geen zorgen maakt over het behouden van een van uw resources in de resourcegroep en u wilt gewoon opnieuw beginnen, u een hele brongroep verwijderen. Dit is een snelle manier om alles te verwijderen. De volgende stappen zijn alleen van toepassing op het implementatiemodel resourcebeheer.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Ontvang een lijst met alle brongroepen in uw abonnement.

```powershell
Get-AzResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Zoek de resourcegroep die u wilt verwijderen.

Zoek de resourcegroep die u wilt verwijderen en bekijk de lijst met bronnen in die resourcegroep. In het voorbeeld is de naam van de resourcegroep RG1. Wijzig het voorbeeld om een lijst met alle bronnen op te halen.

```powershell
Find-AzResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. Controleer de bronnen in de lijst.

Wanneer de lijst wordt geretourneerd, controleert u deze om te controleren of u alle bronnen in de resourcegroep en de resourcegroep zelf wilt verwijderen. Als u een deel van de resources in de resourcegroep wilt behouden, gebruikt u de stappen in de eerdere secties van dit artikel om uw gateway te verwijderen.

### <a name="4-delete-the-resource-group-and-resources"></a>4. Verwijder de brongroep en resources.

Als u de resourcegroep en alle resourcein de resourcegroep wilt verwijderen, wijzigt u het voorbeeld en voert u het uitvoeren uit.

```powershell
Remove-AzResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. Controleer de status.

Het duurt enige tijd voordat Azure alle bronnen verwijdert. U de status van uw resourcegroep controleren met behulp van deze cmdlet.

```powershell
Get-AzResourceGroup -ResourceGroupName RG1
```

Het resultaat dat wordt geretourneerd toont 'Geslaagd'.

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
