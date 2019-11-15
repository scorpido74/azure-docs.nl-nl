---
title: 'Azure-ExpressRoute: een circuit wijzigen: Power shell'
description: Maken, inrichten, controleren, bijwerken, verwijderen en de inrichting van een ExpressRoute-circuit ongedaan maken.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 11/13/2019
ms.author: cherylmc
ms.openlocfilehash: 3ce1096129d7fb45901d80b0173035dcca47c3ee
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74080364"
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>Maken en aanpassen van een ExpressRoute-circuit met behulp van PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sjabloon](expressroute-howto-circuit-resource-manager-template.md)
> * [Video - Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-circuit-classic.md)
>

Dit artikel helpt u bij het maken van een ExpressRoute-circuit met behulp van PowerShell-cmdlets en het Azure Resource Manager-implementatiemodel. U kunt ook de status controleren, bijwerken, verwijderen of de inrichting van een circuit ongedaan maken.

## <a name="before-you-begin"></a>Voordat u begint

Voordat u begint, controleert u de [vereisten](expressroute-prerequisites.md) en [werkstromen](expressroute-workflows.md) voordat u begint met de configuratie.

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="create"></a>Maken en inrichten van een ExpressRoute-circuit
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Meld u aan bij uw Azure-account en selecteer uw abonnement

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. de lijst met ondersteunde providers, locaties en band breedten ophalen
Voordat u een ExpressRoute-circuit maken, moet u de lijst met ondersteunde connectiviteitsproviders, locaties en bandbreedte-opties.

De Power shell **-cmdlet Get-AzExpressRouteServiceProvider** retourneert deze informatie, die u in latere stappen gaat gebruiken:

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

Controleer als uw connectiviteitsprovider hier worden weergegeven. Noteer de volgende informatie, die u later nodig bij het maken van een circuit:

* Naam
* PeeringLocations
* BandwidthsOffered

U bent nu klaar om te maken van een ExpressRoute-circuit.

### <a name="3-create-an-expressroute-circuit"></a>3. een ExpressRoute-circuit maken
Als u nog een resourcegroep hebt, moet u een maken voordat u uw ExpressRoute-circuit maken. U kunt dit doen door het uitvoeren van de volgende opdracht uit:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

Het volgende voorbeeld ziet hoe u een 200 Mbps ExpressRoute-circuit via Equinix in Silicon Valley maakt. Als u verschillende instellingen en een andere provider gebruikt, vervangt u die informatie wanneer u uw aanvraag indienen. Gebruik het volgende voorbeeld om aan te vragen van een nieuwe servicesleutel:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Zorg ervoor dat u opgeeft dat de juiste SKU-laag en de SKU-serie:

* SKU-laag bepaalt of een ExpressRoute-circuit [lokaal](expressroute-faqs.md#expressroute-local), standaard of [Premium](expressroute-faqs.md#expressroute-premium)is. U kunt *lokaal*, *standaard* of *Premium*opgeven.
* SKU-serie, bepaalt de facturering. U kunt opgeven *Metereddata* voor een abonnement naar gebruik en *Unlimiteddata* voor een abonnement voor onbeperkt gebruik. Kunt u het type facturering van *Metereddata* naar *Unlimiteddata*, maar u kunt het type niet wijzigen *Unlimiteddata* naar *Metereddata*. Een *lokaal* circuit is altijd *Unlimiteddata*.

> [!IMPORTANT]
> Uw ExpressRoute-circuit wordt in rekening gebracht vanaf het moment dat een servicesleutel wordt uitgegeven. Zorg ervoor dat u deze bewerking uitvoeren wanneer de connectiviteitsprovider gereed is voor de inrichting van het circuit.
>
>

Het antwoord bevat de sleutel van de service. U kunt gedetailleerde beschrijvingen van alle parameters krijgen door de volgende opdracht uit:

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4. alle ExpressRoute-circuits weer geven
Voer de opdracht **Get-AzExpressRouteCircuit** uit om een lijst op te halen met alle ExpressRoute-circuits die u hebt gemaakt:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Het antwoord lijkt op het volgende voorbeeld:

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
    CircuitProvisioningState          : Enabled
    ServiceProviderProvisioningState  : NotProvisioned
    ServiceProviderNotes              :
    ServiceProviderProperties         : {
                                          "ServiceProviderName": "Equinix",
                                          "PeeringLocation": "Silicon Valley",
                                          "BandwidthInMbps": 200
                                        }
    ServiceKey                        : **************************************
    Peerings                          : []

U kunt deze informatie op elk gewenst moment ophalen met behulp van de `Get-AzExpressRouteCircuit` cmdlet. De oproep zonder parameters geeft een lijst van alle circuits. De sleutel van uw service wordt weergegeven in de *ServiceKey* veld:

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```


Het antwoord lijkt op het volgende voorbeeld:

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
    ServiceProviderProvisioningState : NotProvisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. de service sleutel naar uw connectiviteits provider verzenden voor het inrichten
*ServiceProviderProvisioningState* vindt u informatie over de huidige status van inrichting aan de service-provider. De status van de biedt status aan de kant van Microsoft. Zie voor meer informatie over het circuit inrichtingsstatussen [werkstromen](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Wanneer u een nieuwe ExpressRoute-circuit maakt, wordt het circuit is in de volgende status:

    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



Het circuit gewijzigd in de volgende status hebben de connectiviteitsprovider Bezig inschakelen voor u is:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Voordat u kunt een ExpressRoute-circuit gebruiken, moet deze de status van de volgende zijn:

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Controleer de status en de status van de circuit sleutel regel matig
De status en de status van de sleutel van het circuit controleren, laat u weten wanneer uw provider het circuit is ingeschakeld. Nadat het circuit is geconfigureerd, *ServiceProviderProvisioningState* wordt weergegeven als *ingerichte*, zoals wordt weergegeven in het volgende voorbeeld:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


Het antwoord lijkt op het volgende voorbeeld:

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

### <a name="7-create-your-routing-configuration"></a>7. de routerings configuratie maken
Zie voor stapsgewijze instructies, de [ExpressRoute-circuit routeringsconfiguratie](expressroute-howto-routing-arm.md) artikel maken en aanpassen-circuitpeerings.

> [!IMPORTANT]
> Deze instructies zijn alleen van toepassing op circuits die zijn gemaakt met serviceproviders die services op laag 2-connectiviteit aanbieden. Als u een serviceprovider die beheerde laag-3-services (meestal een IP VPN, zoals MPLS), uw connectiviteitsprovider configureert en beheert routering voor u.
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. een virtueel netwerk koppelen aan een ExpressRoute-circuit
Vervolgens moet u een virtueel netwerk koppelen aan uw ExpressRoute-circuit. Gebruik de [virtuele netwerken koppelen aan ExpressRoute-circuits](expressroute-howto-linkvnet-arm.md) artikel als u met het Resource Manager-implementatiemodel werkt.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Ophalen van de status van een ExpressRoute-circuit
U kunt deze informatie op elk gewenst moment ophalen met behulp van de cmdlet **Get-AzExpressRouteCircuit** . De oproep zonder parameters geeft een lijst van alle circuits.

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```


Het antwoord is vergelijkbaar met het volgende voorbeeld:

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


U kunt informatie over een specifieke ExpressRoute-circuit krijgen door de naam van de resourcegroep en de circuitnaam van het doorgegeven als parameter bij de aanroep:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


Het antwoord lijkt op het volgende voorbeeld:

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


U kunt gedetailleerde beschrijvingen van alle parameters krijgen door de volgende opdracht uit:

```azurepowershell-interactive
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify"></a>Een ExpressRoute-circuit wijzigen
U kunt bepaalde eigenschappen van een ExpressRoute-circuit wijzigen zonder gevolgen voor connectiviteit.

U kunt de volgende taken zonder uitvaltijd kunt doen:

* In- of uitschakelen van een premium-invoegtoepassing voor ExpressRoute voor uw ExpressRoute-circuit.
* De bandbreedte van uw ExpressRoute-circuit vergroten, mits er capaciteit beschikbaar is op de poort. Het downgraden van de bandbreedte van een circuit wordt niet ondersteund.
* De softwarelicentiecontrole abonnement van naar gebruik om onbeperkte gegevens te wijzigen. Wijzigen van de softwarelicentiecontrole plan van onbeperkte gegevens in naar gebruik wordt niet ondersteund.
* U kunt inschakelen en uitschakelen *klassieke bewerkingen toestaan*.

Zie voor meer informatie over limieten en beperkingen, de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>De add-on ExpressRoute premium inschakelen
U kunt de invoegtoepassing ExpressRoute premium inschakelen voor uw bestaande circuit met behulp van de volgende PowerShell-codefragment:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Het circuit heeft nu de ExpressRoute premium-invoegtoepassing-functies ingeschakeld. We beginnen facturering voor de mogelijkheid van premium-invoegtoepassing zodra de opdracht is uitgevoerd.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Om uit te schakelen van de premium-invoegtoepassing voor ExpressRoute
> [!IMPORTANT]
> Als u resources die groter zijn dan wat is toegestaan voor de standard-circuit, kan deze bewerking kan mislukken.
>
>

Let op de volgende informatie:

* Voordat u een downgrade van premium naar standard uitvoeren, moet u ervoor zorgen dat het aantal virtuele netwerken die zijn gekoppeld aan het circuit minder dan 10 is. Als u dit niet doet, uw aanvraag voor bijwerken mislukt en wij factureren u premium-tarieven.
* U moet alle virtuele netwerken in andere geopolitieke regio's ontkoppelen. Als u dit niet doet, uw aanvraag voor bijwerken mislukt en wij factureren u premium-tarieven.
* De routetabel moet minder dan 4000 routes voor persoonlijke peering. Als de grootte van uw route-table groter dan 4000 routes is, wordt de BGP-sessie wordt geweigerd en wordt niet worden ingeschakeld totdat het aantal geadverteerde voorvoegsels lager 4.000 is.

U kunt de ExpressRoute premium-invoegtoepassing voor de bestaande circuit uitschakelen met behulp van de volgende PowerShell-cmdlet:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Bijwerken van de bandbreedte van het ExpressRoute-circuit
Voor ondersteunde bandbreedte-opties voor uw provider, Controleer de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md). U kunt de grootte die groter is dan de grootte van uw bestaande circuit kiezen.

> [!IMPORTANT]
> U moet de ExpressRoute-circuit opnieuw te maken als er onvoldoende capaciteit op de bestaande poort. U kunt het circuit niet bijwerken als er geen extra capaciteit beschikbaar is op die locatie.
>
> U kunt de bandbreedte van een ExpressRoute-circuit zonder onderbreking niet reduceren. Het downgraden van bandbreedte, moet u de inrichting van het ExpressRoute-circuit ongedaan maken en vervolgens opnieuw inrichten van een nieuwe ExpressRoute-circuit.
>

Nadat u welke grootte die u nodig hebt besluit, gebruikt u de volgende opdracht uit om het formaat van uw circuit te:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```


Uw circuit wordt omhoog worden aangepast aan de kant van Microsoft. U kunt vervolgens moet contact opnemen met uw connectiviteitsprovider voor het bijwerken van configuraties op hun kant zodat deze overeenkomt met deze wijziging. Nadat u deze melding, zullen we u facturering voor de bijgewerkte bandbreedte-optie.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Voor het verplaatsen van de SKU van naar gebruik in onbeperkt
U kunt de SKU van een ExpressRoute-circuit wijzigen met behulp van de volgende PowerShell-codefragment:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Voor het beheren van toegang tot het klassieke en Resource Manager-omgevingen
Lees de instructies in [verplaatsen ExpressRoute-circuits van het klassieke naar het Resource Manager-implementatiemodel](expressroute-howto-move-arm.md).

## <a name="delete"></a>Opheffen van inrichting en het verwijderen van een ExpressRoute-circuit
Let op de volgende informatie:

* U moet alle virtuele netwerken loskoppelen van het ExpressRoute-circuit. Als deze bewerking is mislukt, controleert u het zien als alle virtuele netwerken zijn gekoppeld aan het circuit.
* Als het ExpressRoute-circuit serviceprovider-Inrichtingsstatus **Provisioning** of **ingerichte** moet u werken met uw serviceprovider inrichting ongedaan maken van het circuit aan hun kant. We blijven reserveer resources en factureren u totdat de service-provider is voltooid ongedaan maken van inrichting van het circuit en ons wordt geïnformeerd.
* Als de service-provider de inrichting van het circuit is beëindigd (de serviceprovider Inrichtingsstatus is ingesteld op **niet ingericht**), kunt u het circuit verwijderen. Hiermee wordt ook de facturering voor het circuit gestopt.

U kunt uw ExpressRoute-circuit verwijderen door de volgende opdracht uit:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Volgende stappen

Nadat u uw circuit hebt gemaakt, zorg ervoor dat u de volgende stappen voor het volgende doen:

* [Routering voor uw ExpressRoute-circuit maken en wijzigen](expressroute-howto-routing-arm.md)
* [Uw virtueel netwerk koppelen aan uw ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)
