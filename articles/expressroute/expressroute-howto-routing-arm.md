---
title: 'Peering configureren voor een circuit-ExpressRoute: Power shell: Azure | Microsoft Docs'
description: Dit artikel begeleidt u stapsgewijs door de procedure voor het maken en inrichten van de persoonlijke, openbare en Microsoft-peering van een ExpressRoute-circuit. In dit artikel leest u hoe u de status controleert en peerings voor uw circuit bijwerkt of verwijdert.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: 3f2c768fe7998cc166e3102619e7183c96d1ccbb
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965363"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Peering voor een ExpressRoute-circuit maken en wijzigen met behulp van Power shell

Dit artikel helpt u bij het maken en beheren van routerings configuratie voor een ExpressRoute-circuit in het Resource Manager-implementatie model met behulp van Power shell. U kunt ook de status van peerings voor een ExpressRoute-circuit controleren, bijwerken of verwijderen en de inrichting ongedaan maken. Als u een andere methode wilt gebruiken om met uw circuit te werken, selecteert u een artikel in de volgende lijst:

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Video-persoonlijke peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video-open bare peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video-micro soft-peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-routing-classic.md)
> 


Deze instructies zijn alleen van toepassing op circuits die zijn gemaakt met serviceproviders die services met Laag-2-connectiviteit aanbieden. Als u een service provider gebruikt die Managed Layer 3-services biedt (meestal een IPVPN, zoals MPLS), zal uw connectiviteits provider route ring voor u configureren en beheren.

> [!IMPORTANT]
> Op dit moment bieden we nog geen peerings aan die door serviceproviders worden geconfigureerd via de beheerportal van de service. Deze mogelijkheid zal binnenkort worden ingeschakeld. Neem contact op met uw service provider voordat u BGP-peerings configureert.
> 
> 

U kunt een, twee of alle drie de peerings (Azure privé, Azure openbaar en Microsoft) voor een ExpressRoute-circuit configureren. U kunt peerings configureren in elke gewenste volgorde. U moet er echter wel voor zorgen dat u de configuratie van elke peering een voor een voltooit. Voor meer informatie over routerings domeinen en peerings, Zie [ExpressRoute-routerings domeinen](expressroute-circuit-peerings.md).

## <a name="configuration-prerequisites"></a>Configuratievereisten

* Zorg dat u de pagina met [vereisten](expressroute-prerequisites.md), de pagina over [routeringsvereisten](expressroute-routing.md) en de pagina over [werkstromen](expressroute-workflows.md) hebt gelezen voordat u begint met de configuratie.
* U moet een actief ExpressRoute-circuit hebben. Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-arm.md) en laat het circuit inschakelen door de connectiviteitsprovider voordat u verder gaat. Het ExpressRoute-circuit moet zich in een ingerichte en ingeschakelde status bevindt, zodat u de cmdlets in dit artikel kunt uitvoeren.

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="msft"></a>Micro soft-peering

In deze sectie leert u hoe u de micro soft-peering-configuratie voor een ExpressRoute-circuit maakt, ontvangt, bijwerkt en verwijdert.

> [!IMPORTANT]
> Micro soft-peering van ExpressRoute-circuits die zijn geconfigureerd vóór 1 augustus 2017, heeft alle service voorvoegsels die worden geadverteerd via de micro soft-peering, zelfs als er geen route filters zijn gedefinieerd. Micro soft-peering van ExpressRoute-circuits die zijn geconfigureerd op of na 1 augustus 2017, heeft geen voor voegsels die worden geadverteerd totdat een route filter aan het circuit is gekoppeld. Zie [Configure a route filter for micro soft peering](how-to-routefilter-powershell.md)(Engelstalig) voor meer informatie.
> 
> 

### <a name="to-create-microsoft-peering"></a>Microsoft-peering maken

1. Meld u aan en selecteer uw abonnement.

   Als u Power shell lokaal hebt geïnstalleerd, meldt u zich aan. Als u Azure Cloud Shell gebruikt, kunt u deze stap overs Laan.

   ```azurepowershell
   Connect-AzAccount
   ```

   Selecteer het abonnement dat u ExpressRoute-circuit wilt maken.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Maak een ExpressRoute-circuit.

   Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-arm.md) en laat het circuit inrichten door de connectiviteitsprovider. f uw connectiviteits provider biedt beheerde Layer 3-services, u kunt uw connectiviteits provider vragen om micro soft-peering voor u in te scha kelen. In dat geval hoeft u de instructies in de volgende secties niet te volgen. Als uw connectiviteits provider echter geen route ring voor u beheert, kunt u na het maken van het circuit uw configuratie voortzetten met de volgende stappen. 

3. Controleer het ExpressRoute-circuit om er zeker van te zijn dat het is ingericht en ook is ingeschakeld. Gebruik het volgende voorbeeld:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   Het antwoord is vergelijkbaar met het volgende voorbeeld:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
   ProvisioningState                : Succeeded
   Sku                              : {
                                       "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             : 
   ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. Configureer Microsoft-peering voor het circuit. Zorg ervoor dat u over de volgende informatie beschikt voordat u verder gaat.

   * Een/30 of/126-subnet voor de primaire koppeling. Dit moet een geldig openbaar IPv4-of IPv6-voor voegsel zijn dat eigendom is van u en dat is geregistreerd in een-of-IR.
   * Een/30 of/126-subnet voor de secundaire koppeling. Dit moet een geldig openbaar IPv4-of IPv6-voor voegsel zijn dat eigendom is van u en dat is geregistreerd in een-of-IR.
   * Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt.
   * AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken.
   * Geadverteerde voorvoegsels: u moet een lijst verstrekken van alle voorvoegsels die u via de BGP-sessie wilt adverteren. Alleen openbare IP-adresvoorvoegsels worden geaccepteerd. Als u van plan bent een aantal voor voegsels te verzenden, kunt u een door komma's gescheiden lijst verzenden. Deze voorvoegsels moeten voor u zijn geregistreerd in een RIR/IRR. Voor IPv4 BGP-sessies zijn geadverteerde voor voegsels voor IPv4 vereist en voor IPv6 BGP-sessies zijn door IPv6 geadverteerde voor voegsels vereist. 
   * Naam van routeringsregister: u kunt het RIR/IRR opgeven waarbij het AS-nummer en de voorvoegsels zijn geregistreerd.
   * Optioneel:
     * Klant-ASN: als u voorvoegsels adverteert die niet zijn geregistreerd op het AS-nummer van de peering, kunt u het AS-nummer opgeven waarop ze zijn geregistreerd.
     * Een MD5-hash, als u er een wilt gebruiken.

> [!IMPORTANT]
> Micro soft controleert of de opgegeven ' aangekondigde open bare voor voegsels ' en ' peer-ASN ' (of ' klant-ASN ') aan u zijn toegewezen in het Internet Routing REGI ster. Als u de open bare voor voegsels van een andere entiteit krijgt en de toewijzing niet is vastgelegd met het routerings register, wordt de automatische validatie niet voltooid en moet hand matig worden gevalideerd. Als de automatische validatie mislukt, ziet u ' AdvertisedPublicPrefixesState ' als ' validatie vereist ' bij de uitvoer van de opdracht ' Get-AzExpressRouteCircuitPeeringConfig ' (Zie ' micro soft-peering-Details ophalen ' hieronder). 
> 
> Als u het bericht validatie vereist ziet, verzamelt u de documenten die de open bare voor voegsels bevatten, worden toegewezen aan uw organisatie door de entiteit die wordt vermeld als eigenaar van de voor voegsels in het routerings register en deze documenten voor hand matige validatie indienen door een ondersteunings ticket openen zoals hieronder wordt weer gegeven. 
> 
>

   Gebruik het volgende voor beeld om micro soft-peering voor uw circuit te configureren:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

### <a name="getmsft"></a>Details van micro soft-peering ophalen

U kunt Configuratie Details ophalen met behulp van het volgende voor beeld:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="updatemsft"></a>De configuratie van micro soft-peering bijwerken

U kunt elk deel van de configuratie bijwerken met behulp van het volgende voor beeld:

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletemsft"></a>Micro soft-peering verwijderen

U kunt de peering-configuratie verwijderen door de volgende cmdlet uit te voeren:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="private"></a>Persoonlijke Azure-peering

In deze sectie leert u hoe u de persoonlijke Azure-peering-configuratie voor een ExpressRoute-circuit maakt, ontvangt, bijwerkt en verwijdert.

### <a name="to-create-azure-private-peering"></a>Persoonlijke Azure-peering maken

1. Importeer de PowerShell-module voor ExpressRoute.

   U moet het meest recente installatieprogramma installeren vanuit de [PowerShell Gallery](https://www.powershellgallery.com/) en de Azure Resource Manager-modules importeren in de PowerShell-sessie om de ExpressRoute-cmdlets te kunnen gebruiken. U moet PowerShell tevens uitvoeren als beheerder.

   ```azurepowershell-interactive
   Install-Module Az
   ```

   Alle AZ.\*-modules importeren binnen het bereik van de bekende semantische versie.

   ```azurepowershell-interactive
   Import-Module Az
   ```

   U kunt ook gewoon een SELECT-module importeren binnen het bereik van de bekende semantische versie.

   ```azurepowershell-interactive
   Import-Module Az.Network 
   ```

   Meld u aan bij uw account.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Selecteer het abonnement dat u ExpressRoute-circuit wilt maken.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Maak een ExpressRoute-circuit.

   Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-arm.md) en laat het circuit inrichten door de connectiviteitsprovider. Als uw connectiviteits provider beheerde Layer 3-services biedt, kunt u uw connectiviteits provider vragen om persoonlijke Azure-peering voor u in te scha kelen. In dat geval hoeft u de instructies in de volgende secties niet te volgen. Als uw connectiviteits provider echter geen route ring voor u beheert, kunt u na het maken van het circuit uw configuratie voortzetten met de volgende stappen.

3. Controleer het ExpressRoute-circuit om er zeker van te zijn dat het is ingericht en ook is ingeschakeld. Gebruik het volgende voorbeeld:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   Het antwoord is vergelijkbaar met het volgende voorbeeld:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
   ProvisioningState                : Succeeded
   Sku                              : {
                                       "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             : 
   ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. Configureer persoonlijke Azure-peering voor het circuit. Zorg ervoor dat u de volgende items hebt voordat u verdergaat met de volgende stappen:

   * Een /30-subnet voor de primaire koppeling. Het subnet mag geen deel uitmaken van een adres ruimte die is gereserveerd voor virtuele netwerken.
   * Een /30-subnet voor de secundaire koppeling. Het subnet mag geen deel uitmaken van een adres ruimte die is gereserveerd voor virtuele netwerken.
   * Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt.
   * AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken. U kunt een persoonlijk AS-nummer voor deze peering gebruiken. Zorg dat u niet 65515 gebruikt.
   * Optioneel:
     * Een MD5-hash, als u er een wilt gebruiken.

   Gebruik het volgende voor beeld om persoonlijke Azure-peering voor uw circuit te configureren:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Als u een MD5-hash wilt gebruiken, gebruikt u het volgende voor beeld:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Zorg dat u uw AS-nummer als peering-ASN opgeeft, niet als klant-ASN.
   > 
   >

### <a name="getprivate"></a>Details van persoonlijke Azure-peering ophalen

U kunt Configuratie Details ophalen met behulp van het volgende voor beeld:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="updateprivate"></a>De configuratie voor persoonlijke Azure-peering bijwerken

U kunt elk deel van de configuratie bijwerken met behulp van het volgende voor beeld. In dit voor beeld wordt de VLAN-ID van het circuit bijgewerkt van 100 naar 500.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deleteprivate"></a>Persoonlijke Azure-peering verwijderen

U kunt de peering-configuratie verwijderen door het volgende voor beeld uit te voeren:

> [!WARNING]
> U moet ervoor zorgen dat alle virtuele netwerken en ExpressRoute Global Reach verbindingen worden verwijderd voordat u dit voor beeld uitvoert. 
> 
> 

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="public"></a>Open bare Azure-peering

In deze sectie leert u hoe u de open bare Azure-peering-configuratie voor een ExpressRoute-circuit maakt, ontvangt, bijwerkt en verwijdert.

> [!Note]
> Open bare Azure-peering is afgeschaft voor nieuwe circuits. Zie [ExpressRoute peering](expressroute-circuit-peerings.md)(Engelstalig) voor meer informatie.
>

### <a name="to-create-azure-public-peering"></a>Openbare Azure-peering maken

1. Importeer de PowerShell-module voor ExpressRoute.

   U moet het meest recente installatieprogramma installeren vanuit de [PowerShell Gallery](https://www.powershellgallery.com/) en de Azure Resource Manager-modules importeren in de PowerShell-sessie om de ExpressRoute-cmdlets te kunnen gebruiken. U moet PowerShell tevens uitvoeren als beheerder.

   ```azurepowershell-interactive
   Install-Module Az
   ```

   Alle AZ.\*-modules importeren binnen het bereik van de bekende semantische versie.

   ```azurepowershell-interactive
   Import-Module Az
   ```

   U kunt ook gewoon een SELECT-module importeren binnen het bereik van de bekende semantische versie.

   ```azurepowershell-interactive
   Import-Module Az.Network
   ```

   Meld u aan bij uw account.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Selecteer het abonnement dat u ExpressRoute-circuit wilt maken.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Maak een ExpressRoute-circuit.

   Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-arm.md) en laat het circuit inrichten door de connectiviteitsprovider. Als uw connectiviteits provider beheerde Layer 3-services biedt, kunt u uw connectiviteits provider vragen om open bare Azure-peering voor u in te scha kelen. In dat geval hoeft u de instructies in de volgende secties niet te volgen. Als uw connectiviteits provider echter geen route ring voor u beheert, kunt u na het maken van het circuit uw configuratie voortzetten met de volgende stappen.

3. Controleer het ExpressRoute-circuit om te controleren of het is ingericht en ook is ingeschakeld. Gebruik het volgende voorbeeld:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   Het antwoord is vergelijkbaar met het volgende voorbeeld:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
   ProvisioningState                : Succeeded
   Sku                              : {
                                      "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             : 
   ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. Configureer openbare Azure-peering voor het circuit. Zorg ervoor dat u over de volgende informatie beschikt voordat u verder gaat.

   * Een /30-subnet voor de primaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn.
   * Een /30-subnet voor de secundaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn.
   * Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt.
   * AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken.
   * Optioneel:
     * Een MD5-hash, als u er een wilt gebruiken.

   Voer het volgende voor beeld uit om open bare Azure-peering voor uw circuit te configureren

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Als u een MD5-hash wilt gebruiken, gebruikt u het volgende voor beeld:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > Zorg dat u uw AS-nummer als peering-ASN opgeeft, niet als klant-ASN.
   > 
   >

### <a name="getpublic"></a>Details van open bare Azure-peering ophalen

U kunt Configuratie Details ophalen met behulp van de volgende cmdlet:

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="updatepublic"></a>De configuratie van open bare Azure-peering bijwerken

U kunt elk deel van de configuratie bijwerken met behulp van het volgende voor beeld. In dit voor beeld wordt de VLAN-ID van het circuit bijgewerkt van 200 naar 600.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletepublic"></a>Open bare Azure-peering verwijderen

U kunt de peering-configuratie verwijderen door het volgende voor beeld uit te voeren:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>Volgende stappen

Volgende stap, [Een VNet koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md).

* Voor meer informatie over ExpressRoute-werkstromen raadpleegt u [ExpressRoute workflows](expressroute-workflows.md) (ExpressRoute-werkstromen).
* Voor meer informatie over circuitpeering raadpleegt u [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-circuits en -routeringsdomeinen).
* Bekijk het [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Virtual Network-overzicht) voor meer informatie over het gebruik van virtuele netwerken.
