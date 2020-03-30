---
title: 'Een virtuele netwerkgateway verwijderen: Azure-klassieker'
description: Een virtuele netwerkgateway verwijderen met PowerShell in het klassieke implementatiemodel.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: cherylmc
ms.openlocfilehash: e7283f5e28edc6f7beaad3a2743aa155f6ea6e14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198646"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Een virtuele netwerkgateway verwijderen met PowerShell (klassiek)

> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Klassiek - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>

Met dit artikel u een VPN-gateway in het klassieke implementatiemodel verwijderen met PowerShell. Nadat de virtuele netwerkgateway is verwijderd, wijzigt u het netwerkconfiguratiebestand om elementen te verwijderen die u niet meer gebruikt.

## <a name="step-1-connect-to-azure"></a><a name="connect"></a>Stap 1: Verbinding maken met Azure

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Installeer de nieuwste PowerShell-cmdlets.

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="2-connect-to-your-azure-account"></a>2. Maak verbinding met uw Azure-account.

Open de PowerShell-console met verhoogde rechten en maak verbinding met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

1. Open uw PowerShell-console met verhoogde rechten. Als u wilt overschakelen naar servicebeheer, gebruikt u deze opdracht:

   ```powershell
   azure config mode asm
   ```
2. Maak verbinding met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

   ```powershell
   Add-AzureAccount
   ```

## <a name="step-2-export-and-view-the-network-configuration-file"></a><a name="export"></a>Stap 2: Het netwerkconfiguratiebestand exporteren en weergeven

Maak een map op de computer en exporteer vervolgens het netwerkconfiguratiebestand naar de map. U gebruikt dit bestand om zowel de huidige configuratie-informatie weer te geven als om de netwerkconfiguratie te wijzigen.

In dit voorbeeld wordt het netwerkconfiguratiebestand geëxporteerd naar C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Open het bestand met een teksteditor en bekijk de naam voor uw klassieke VNet. Wanneer u een VNet maakt in de Azure-portal, is de volledige naam die Azure gebruikt niet zichtbaar in de portal. Een VNet met de naam 'ClassicVNet1' in de Azure-portal kan bijvoorbeeld een veel langere naam hebben in het netwerkconfiguratiebestand. De naam lijkt misschien op: 'Group ClassicRG1 ClassicVNet1'. Virtuele netwerknamen worden vermeld als **'VirtualNetworkSite name='**. Gebruik de namen in het netwerkconfiguratiebestand bij het uitvoeren van uw PowerShell-cmdlets.

## <a name="step-3-delete-the-virtual-network-gateway"></a><a name="delete"></a>Stap 3: De virtuele netwerkgateway verwijderen

Wanneer u een virtuele netwerkgateway verwijdert, worden alle verbindingen met het VNet via de gateway verbroken. Als P2S-clients zijn verbonden met het VNet, worden deze zonder waarschuwing verbroken.

In dit voorbeeld wordt de virtuele netwerkgateway verwijderd. Zorg ervoor dat u de volledige naam van het virtuele netwerk uit het netwerkconfiguratiebestand gebruikt.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

Als dit lukt, wordt het rendement weergegeven:

```
Status : Successful
```

## <a name="step-4-modify-the-network-configuration-file"></a><a name="modify"></a>Stap 4: Het netwerkconfiguratiebestand wijzigen

Wanneer u een virtuele netwerkgateway verwijdert, wijzigt de cmdlet het netwerkconfiguratiebestand niet. U moet het bestand wijzigen om de elementen te verwijderen die niet meer worden gebruikt. Met de volgende secties u het netwerkconfiguratiebestand wijzigen dat u hebt gedownload.

### <a name="local-network-site-references"></a><a name="lnsref"></a>Verwijzingen naar lokale netwerksite

Als u sitereferentiegegevens wilt verwijderen, voert u configuratiewijzigingen aan in **ConnectionsToLocalNetwork/LocalNetworkSiteRef**. Als u een lokale siteverwijzing verwijdert, wordt Azure geactiveerd om een tunnel te verwijderen. Afhankelijk van de configuratie die u hebt gemaakt, wordt er mogelijk geen **LocalNetworkSiteRef** weergegeven.

```
<Gateway>
   <ConnectionsToLocalNetwork>
     <LocalNetworkSiteRef name="D1BFC9CB_Site2">
       <Connection type="IPsec" />
     </LocalNetworkSiteRef>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

Voorbeeld:

```
<Gateway>
   <ConnectionsToLocalNetwork>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

### <a name="local-network-sites"></a><a name="lns"></a>Lokale netwerksites

Verwijder alle lokale sites die u niet meer gebruikt. Afhankelijk van de configuratie die u hebt gemaakt, is het mogelijk dat u geen **LocalNetworkSite** hebt vermeld.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
  <LocalNetworkSite name="Site3">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>57.179.18.164</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

In dit voorbeeld hebben we alleen Site3 verwijderd.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

### <a name="client-addresspool"></a><a name="clientaddresss"></a>Clientadrespool

Als je een P2S-verbinding met je VNet hebt, heb je een **VPNClientAddressPool.** Verwijder de groepen clientadres die overeenkomen met de virtuele netwerkgateway die u hebt verwijderd.

```
<Gateway>
    <VPNClientAddressPool>
      <AddressPrefix>10.1.0.0/24</AddressPrefix>
    </VPNClientAddressPool>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

Voorbeeld:

```
<Gateway>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

### <a name="gatewaysubnet"></a><a name="gwsub"></a>GatewaySubnet

Verwijder het **GatewaySubnet** dat overeenkomt met het VNet.

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
   <Subnet name="GatewaySubnet">
     <AddressPrefix>10.11.1.0/29</AddressPrefix>
   </Subnet>
 </Subnets>
```

Voorbeeld:

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
 </Subnets>
```

## <a name="step-5-upload-the-network-configuration-file"></a><a name="upload"></a>Stap 5: Het netwerkconfiguratiebestand uploaden

Sla uw wijzigingen op en upload het netwerkconfiguratiebestand naar Azure. Zorg ervoor dat u het bestandspad wijzigt indien nodig voor uw omgeving.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Als deze succesvol is, wordt in het rendement iets weergegeven dat lijkt op dit voorbeeld:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
```
