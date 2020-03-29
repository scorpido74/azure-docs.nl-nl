---
title: 'Azure ExpressRoute: peering configureren: klassiek'
description: Dit artikel begeleidt u stapsgewijs door de procedure voor het maken en inrichten van de persoonlijke, openbare en Microsoft-peering van een ExpressRoute-circuit. In dit artikel leest u hoe u de status controleert en peerings voor uw circuit bijwerkt of verwijdert.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: cherylmc
ms.openlocfilehash: 05602538f206032d924b39a7dd8f4325c48a5224
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931379"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>Peering maken en wijzigen voor een ExpressRoute-circuit (klassiek)
> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-routing-portal-resource-manager.md)
> * [Powershell](expressroute-howto-routing-arm.md)
> * [Azure-CLI](howto-routing-cli.md)
> * [Video - Privé-peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - Openbaar peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video - Microsoft-peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-routing-classic.md)
> 

In dit artikel u de stappen doorlopen om peering/routing-configuratie voor een ExpressRoute-circuit te maken en te beheren met PowerShell en het klassieke implementatiemodel. In de volgende stappen ziet u ook hoe u de status van een peering voor een ExpressRoute-circuit controleert en peerings bijwerkt of verwijdert en de inrichting ervan ongedaan maakt. U één, twee of alle drie de peeringen (Azure private, Azure public en Microsoft) configureren voor een ExpressRoute-circuit. U kunt peerings configureren in elke gewenste volgorde. U moet er echter wel voor zorgen dat u de configuratie van elke peering een voor een voltooit. 

Deze instructies zijn alleen van toepassing op circuits die zijn gemaakt met serviceproviders die Layer 2-connectiviteitsservices aanbieden. Als u een serviceprovider gebruikt die beheerde Layer 3-services aanbiedt (meestal een IPVPN, zoals MPLS), configureert en beheert uw connectiviteitsprovider de routering voor u.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Over Azure-implementatiemodellen**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Configuratievereisten

* Zorg dat u de pagina met [vereisten](expressroute-prerequisites.md), de pagina over [routeringsvereisten](expressroute-routing.md) en de pagina over [werkstromen](expressroute-workflows.md) hebt gelezen voordat u begint met de configuratie.
* U moet een actief ExpressRoute-circuit hebben. Volg de instructies om [een ExpressRoute-circuit](expressroute-howto-circuit-classic.md) te maken en laat het circuit door uw connectiviteitsprovider worden ingeschakeld voordat u verdergaat. Het ExpressRoute-circuit moet zijn ingericht en zijn ingeschakeld om de hieronder beschreven cmdlets te kunnen uitvoeren.

### <a name="download-the-latest-powershell-cmdlets"></a>Download de nieuwste PowerShell-cmdlets

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="azure-private-peering"></a>Persoonlijke Azure-peering

In deze sectie vindt u instructies voor het maken, verkrijgen, bijwerken en verwijderen van de configuratie voor een persoonlijke Azure-peering voor een ExpressRoute-circuit. 

### <a name="to-create-azure-private-peering"></a>Persoonlijke Azure-peering maken

1. **Maak een ExpressRoute-circuit.**

   Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-classic.md) en laat het circuit inrichten door de connectiviteitsprovider. Als uw connectiviteitsprovider beheerde Laag-3-services biedt, kunt u de connectiviteitsprovider vragen om persoonlijke Azure-peering voor u in te schakelen. In dat geval hoeft u de instructies in de volgende secties niet te volgen. Als uw connectiviteitsprovider routing niet voor u beheert, volgt u onderstaande instructies wanneer u het circuit hebt gemaakt.
2. **Controleer het ExpressRoute-circuit om er zeker van te zijn dat het is ingericht.**
   
   Controleer of het ExpressRoute-circuit is ingericht en ook ingeschakeld.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   retourneren:

   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Zorg ervoor dat het circuit wordt weergegeven als ingericht en ingeschakeld. Als dit niet het is, werkt u samen met uw connectiviteitsprovider om uw circuit in de vereiste status en status te krijgen.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **Configureer persoonlijke Azure-peering voor het circuit.**

   Zorg ervoor dat u de volgende items hebt voordat u verdergaat met de volgende stappen:
   
   * Een /30-subnet voor de primaire koppeling. Dit mag geen deel uitmaken van een adresruimte die is gereserveerd voor virtuele netwerken.
   * Een /30-subnet voor de secundaire koppeling. Dit mag geen deel uitmaken van een adresruimte die is gereserveerd voor virtuele netwerken.
   * Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of geen ander peering in het circuit dezelfde VLAN-id gebruikt.
   * AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken. U kunt een persoonlijk AS-nummer voor deze peering gebruiken. Controleer of u 65515 niet gebruikt.
   * Een MD5-hash, als u er een wilt gebruiken. **Optioneel**.
     
   U het volgende voorbeeld gebruiken om Azure private peering voor uw circuit te configureren:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
   ```    

   Als u een MD5-hash wilt gebruiken, gebruikt u het volgende voorbeeld om privé-peering voor uw circuit te configureren:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
   ```
     
   > [!IMPORTANT]
   > Controleer of u uw AS-nummer opgeeft als peering ASN, niet als klant ASN.
   > 

### <a name="to-view-azure-private-peering-details"></a>De details van persoonlijke Azure-peering weergeven

U configuratiedetails bekijken met de volgende cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

retourneren:

```
AdvertisedPublicPrefixes       : 
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 10.0.0.0/30
RoutingRegistryName            : 
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 10.0.0.4/30
State                          : Enabled
VlanId                         : 100
```

### <a name="to-update-azure-private-peering-configuration"></a>De configuratie van persoonlijke Azure-peering bijwerken

Met de volgende cmdlet kunt u elk deel van de configuratie bijwerken. In het volgende voorbeeld wordt de VLAN-ID van het circuit bijgewerkt van 100 naar 500.

```powershell
Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"
```

### <a name="to-delete-azure-private-peering"></a>Persoonlijke Azure-peering verwijderen

U kunt een peeringconfiguratie verwijderen door de volgende cmdlet uit te voeren. U moet ervoor zorgen dat alle virtuele netwerken zijn losgekoppeld van het ExpressRoute-circuit voordat u deze cmdlet uitvoert.

```powershell
Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

## <a name="azure-public-peering"></a>Openbare Azure-peering

In deze sectie vindt u instructies voor het maken, verkrijgen, bijwerken en verwijderen van de configuratie voor een openbare Azure-peering voor een ExpressRoute-circuit.

> [!NOTE]
> Azure public peering is afgeschaft voor nieuwe circuits.
>

### <a name="to-create-azure-public-peering"></a>Openbare Azure-peering maken

1. **Een ExpressRoute-circuit maken**

   Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-classic.md) en laat het circuit inrichten door de connectiviteitsprovider. Als uw connectiviteitsprovider beheerde Laag-3-services biedt, kunt u de connectiviteitsprovider vragen om openbare Azure-peering voor u in te schakelen. In dat geval hoeft u de instructies in de volgende secties niet te volgen. Als uw connectiviteitsprovider routing niet voor u beheert, volgt u onderstaande instructies wanneer u het circuit hebt gemaakt.
2. **Controleer het ExpressRoute-circuit om te controleren of het is ingericht**

   Controleer eerst of het ExpressRoute-circuit is ingericht en is ingeschakeld.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   retourneren:

   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Controleer of het circuit wordt weergegeven als Ingericht en ingeschakeld. Als dit niet het is, werkt u samen met uw connectiviteitsprovider om uw circuit in de vereiste status en status te krijgen.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
4. **Azure public peering configureren voor het circuit**
   
   Zorg ervoor dat u de volgende informatie hebt voordat u verdergaat:
   
   * Een /30-subnet voor de primaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn.
   * Een /30-subnet voor de secundaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn.
   * Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of geen ander peering in het circuit dezelfde VLAN-id gebruikt.
   * AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken.
   * Een MD5-hash, als u er een wilt gebruiken. **Optioneel**.

   > [!IMPORTANT]
   > Zorg ervoor dat u uw AS-nummer opgeeft als peering ASN en niet als klant ASN.
   >  
     
   U het volgende voorbeeld gebruiken om Azure public peering voor uw circuit te configureren:

   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
   ```
     
   Als u een MD5-hash wilt gebruiken, gebruikt u het volgende voorbeeld om uw circuit te configureren:
     
   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
   ```
     
### <a name="to-view-azure-public-peering-details"></a>De details van openbare Azure-peering weergeven

Als u configuratiegegevens wilt weergeven, gebruikt u de volgende cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

retourneren:

```powershell
AdvertisedPublicPrefixes       : 
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 131.107.0.0/30
RoutingRegistryName            : 
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 131.107.0.4/30
State                          : Enabled
VlanId                         : 200
```

### <a name="to-update-azure-public-peering-configuration"></a>De configuratie van openbare Azure-peering bijwerken

Met de volgende cmdlet kunt u elk deel van de configuratie bijwerken. In dit voorbeeld wordt de VLAN ID van het circuit bijgewerkt van 200 naar 600.

```powershell
Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"
```

Controleer of het circuit wordt weergegeven als Ingericht en ingeschakeld. 
### <a name="to-delete-azure-public-peering"></a>Openbare Azure-peering verwijderen

U uw peeringconfiguratie verwijderen door de volgende cmdlet uit te voeren:

```powershell
Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

## <a name="microsoft-peering"></a>Microsoft-peering

In deze sectie vindt u instructies voor het maken, verkrijgen, bijwerken en verwijderen van de configuratie voor een Microsoft-peering voor een ExpressRoute-circuit. 

### <a name="to-create-microsoft-peering"></a>Microsoft-peering maken

1. **Een ExpressRoute-circuit maken**
  
   Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-classic.md) en laat het circuit inrichten door de connectiviteitsprovider. Als uw connectiviteitsprovider beheerde Laag-3-services biedt, kunt u de connectiviteitsprovider vragen om persoonlijke Azure-peering voor u in te schakelen. In dat geval hoeft u de instructies in de volgende secties niet te volgen. Als uw connectiviteitsprovider routing niet voor u beheert, volgt u onderstaande instructies wanneer u het circuit hebt gemaakt.
2. **Controleer het ExpressRoute-circuit om te controleren of het is ingericht**

   Controleer of het circuit wordt weergegeven als Ingericht en ingeschakeld. 
   
   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   retourneren:
   
   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Controleer of het circuit wordt weergegeven als Ingericht en ingeschakeld. Als dit niet het is, werkt u samen met uw connectiviteitsprovider om uw circuit in de vereiste status en status te krijgen.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **Microsoft-peering configureren voor het circuit**
   
    Zorg ervoor dat u over de volgende informatie beschikt voordat u verder gaat.
   
   * Een /30-subnet voor de primaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn waarvan u eigenaar bent en dat is geregistreerd in een RIR/IRR.
   * Een /30-subnet voor de secundaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn waarvan u eigenaar bent en dat is geregistreerd in een RIR/IRR.
   * Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of geen ander peering in het circuit dezelfde VLAN-id gebruikt.
   * AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken.
   * Geadverteerde voorvoegsels: u moet een lijst verstrekken van alle voorvoegsels die u via de BGP-sessie wilt adverteren. Alleen openbare IP-adresvoorvoegsels worden geaccepteerd. U een door komma's gescheiden lijst verzenden als u van plan bent een set voorvoegsels te verzenden. Deze voorvoegsels moeten voor u zijn geregistreerd in een RIR/IRR.
   * Klant-ASN: als u voorvoegsels adverteert die niet zijn geregistreerd op het AS-nummer van de peering, kunt u het AS-nummer opgeven waarop ze zijn geregistreerd. **Optioneel**.
   * Naam van routeringsregister: u kunt het RIR/IRR opgeven waarbij het AS-nummer en de voorvoegsels zijn geregistreerd.
   * Een MD5-hash, als u er een wilt gebruiken. **Optionele.**
     
   Voer de volgende cmdlet uit om Microsoft-peering voor uw circuit te configureren:
 
   ```powershell
   New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
   ```

### <a name="to-view-microsoft-peering-details"></a>De details van Microsoft-peering weergeven

U configuratiedetails bekijken met de volgende cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```
retourneren:

```powershell
AdvertisedPublicPrefixes       : 123.0.0.0/30
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 2245
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 10.0.0.0/30
RoutingRegistryName            : ARIN
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 10.0.0.4/30
State                          : Enabled
VlanId                         : 300
```

### <a name="to-update-microsoft-peering-configuration"></a>Configuratie van Microsoft-peering bijwerken

U elk onderdeel van de configuratie bijwerken met de volgende cmdlet:

```powershell
Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
```

### <a name="to-delete-microsoft-peering"></a>Microsoft-peering verwijderen

U uw peeringconfiguratie verwijderen door de volgende cmdlet uit te voeren:

```powershell
Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```

## <a name="next-steps"></a>Volgende stappen

Koppel [vervolgens een VNet aan een ExpressRoute-circuit.](expressroute-howto-linkvnet-classic.md)

* Zie [ExpressRoute-werkstromen voor](expressroute-workflows.md)meer informatie over werkstromen.
* Voor meer informatie over circuitpeering raadpleegt u [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-circuits en -routeringsdomeinen).
