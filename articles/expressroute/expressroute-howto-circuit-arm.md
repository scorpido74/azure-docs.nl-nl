---
title: 'Azure ExpressRoute: een circuit wijzigen: PowerShell'
description: Maak, indik, verifieer, werk, verwijder en deprovisioneer een ExpressRoute-circuit.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 01/08/2020
ms.author: cherylmc
ms.openlocfilehash: ab44d5358154c17622eef68205ac2326e81377a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75770965"
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>Een ExpressRoute-circuit maken en wijzigen met behulp van PowerShell
> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Powershell](expressroute-howto-circuit-arm.md)
> * [Azure-CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sjabloon](expressroute-howto-circuit-resource-manager-template.md)
> * [Video - Azure-portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-circuit-classic.md)
>

Met dit artikel u een ExpressRoute-circuit maken met PowerShell-cmdlets en het Azure Resource Manager-implementatiemodel. U ook de status controleren, een circuit bijwerken, verwijderen of deprovisionen.

## <a name="before-you-begin"></a>Voordat u begint

Voordat u begint, controleert u de [vereisten](expressroute-prerequisites.md) en [werkstromen](expressroute-workflows.md) voordat u met de configuratie begint.

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Een ExpressRoute-circuit maken en inrichten
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Meld u aan bij uw Azure-account en selecteer uw abonnement

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Download de lijst met ondersteunde providers, locaties en bandbreedtes
Voordat u een ExpressRoute-circuit maakt, hebt u de lijst met ondersteunde connectiviteitsproviders, locaties en bandbreedteopties nodig.

De PowerShell-cmdlet **Get-AzExpressRouteServiceProvider** retourneert deze informatie, die u in latere stappen zult gebruiken:

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

Controleer of uw connectiviteitsprovider daar is vermeld. Noteer de volgende informatie, die u later nodig hebt wanneer u een circuit maakt:

* Name
* PeeringLocaties
* Bandbreedtes aangeboden

Je bent nu klaar om een ExpressRoute circuit te maken.

### <a name="3-create-an-expressroute-circuit"></a>3. Maak een ExpressRoute-circuit
Als u nog geen resourcegroep hebt, moet u er een maken voordat u uw ExpressRoute-circuit maakt. U dit doen door de volgende opdracht uit te voeren:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

In het volgende voorbeeld ziet u hoe u een ExpressRoute-circuit van 200 Mbps maken via Equinix in Silicon Valley. Als u een andere provider en verschillende instellingen gebruikt, vervangt u die informatie wanneer u uw verzoek indient. Gebruik het volgende voorbeeld om een nieuwe servicesleutel aan te vragen:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Zorg ervoor dat u de juiste SKU-laag en SKU-familie opgeeft:

* De SKU-laag bepaalt of een ExpressRoute-circuit [lokaal](expressroute-faqs.md#expressroute-local), standaard of [premium](expressroute-faqs.md#expressroute-premium)is. U *Lokaal,* *Standaard* of *Premium*opgeven.
* De SKU-familie bepaalt het factureringstype. U *Datadata* opgeven voor een data-abonnement met data met data met data en *Unlimiteddata* voor een onbeperkt data-abonnement. U het factureringstype wijzigen van *Gemetendata* naar *Unlimiteddata,* maar u het type niet wijzigen van *Unlimiteddata* naar *Gemetendata.* Een *lokaal* circuit is altijd *Unlimiteddata.*

> [!IMPORTANT]
> Uw ExpressRoute-circuit wordt gefactureerd vanaf het moment dat een servicesleutel wordt uitgegeven. Zorg ervoor dat u deze bewerking uitvoert wanneer de connectiviteitsprovider klaar is om het circuit in te richten.
>
>

Het antwoord bevat de servicesleutel. U gedetailleerde beschrijvingen van alle parameters krijgen door de volgende opdracht uit te voeren:

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4. Alle ExpressRoute-circuits vermelden
Voer de opdracht **Get-AzExpressRouteCircuit** uit om een lijst te krijgen met alle ExpressRoute-circuits die u hebt gemaakt:

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

U deze informatie op elk `Get-AzExpressRouteCircuit` gewenst moment ophalen met behulp van de cmdlet. Het maken van de oproep zonder parameters lijsten van alle circuits. Uw servicesleutel wordt vermeld in het veld *ServiceKey:*

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


### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Stuur de servicesleutel naar uw connectiviteitsprovider voor inlevering
*ServiceProviderProvisioningState* geeft informatie over de huidige stand van de inrichting aan de kant van de dienstverlener. Status geeft de status aan de Microsoft-kant. Zie [Werkstromen](expressroute-workflows.md#expressroute-circuit-provisioning-states)voor meer informatie over statussen voor het inrichten van circuits.

Wanneer u een nieuw ExpressRoute-circuit maakt, bevindt het circuit zich in de volgende staat:

    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



Het circuit verandert in de volgende status wanneer de connectiviteitsprovider het voor u inschakelt:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Om een ExpressRoute-circuit te kunnen gebruiken, moet het in de volgende staat zijn:

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Controleer periodiek de status en de status van de circuitsleutel
Als u de status en de status van de circuitsleutel controleert, weet u wanneer uw provider uw circuit heeft ingeschakeld. Nadat het circuit is geconfigureerd, wordt *ServiceProviderProvisioningState* weergegeven als *Ingerichte*, zoals in het volgende voorbeeld wordt weergegeven:

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

### <a name="7-create-your-routing-configuration"></a>7. Uw routeringsconfiguratie maken
Zie het artikel voor het [routeringsconfiguratieartikel ExpressRoute-circuit](expressroute-howto-routing-arm.md) om circuitpeeringen te maken en te wijzigen voor stapsgewijze instructies.

> [!IMPORTANT]
> Deze instructies zijn alleen van toepassing op circuits die zijn gemaakt met serviceproviders die layer 2-connectiviteitsservices aanbieden. Als u een serviceprovider gebruikt die beheerde laag 3-services aanbiedt (meestal een IP VPN, zoals MPLS), configureert en beheert uw connectiviteitsprovider routering voor u.
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Koppel een virtueel netwerk aan een ExpressRoute-circuit
Koppel vervolgens een virtueel netwerk aan uw ExpressRoute-circuit. Gebruik het artikel [Virtuele netwerken koppelen aan expressroute-circuits](expressroute-howto-linkvnet-arm.md) wanneer u met het implementatiemodel Resource Manager werkt.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>De status van een ExpressRoute-circuit verkrijgen
U deze informatie op elk gewenst moment ophalen met behulp van de **cmdlet Get-AzExpressRouteCircuit.** Het maken van de oproep zonder parameters lijsten van alle circuits.

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


U informatie over een specifiek ExpressRoute-circuit opvragen door de naam en de naam van de resourcegroep als parameter door te geven aan de aanroep:

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


U gedetailleerde beschrijvingen van alle parameters krijgen door de volgende opdracht uit te voeren:

```azurepowershell-interactive
get-help Get-AzExpressRouteCircuit -detailed
```

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Een ExpressRoute-circuit wijzigen
U bepaalde eigenschappen van een ExpressRoute-circuit wijzigen zonder dat dit gevolgen heeft voor de connectiviteit.

U de volgende taken uitvoeren zonder downtime:

* Een ExpressRoute premium add-on voor uw ExpressRoute-circuit in- of uitschakelen.
* Verhoog de bandbreedte van uw ExpressRoute-circuit, mits er capaciteit beschikbaar is op de poort. Het verlagen van de bandbreedte van een circuit wordt niet ondersteund.
* Wijzig het meetplan van Gemeten Data in Onbeperkte gegevens. Het wijzigen van het meetplan van Unlimited Data naar Gemeten Data wordt niet ondersteund.
* U *Klassieke bewerkingen*toestaan en uitschakelen.

Zie de Veelgestelde vragen over [ExpressRoute voor](expressroute-faqs.md)meer informatie over limieten en beperkingen.

### <a name="to-enable-the-expressroute-premium-add-on"></a>De ExpressRoute premium add-on inschakelen
U de ExpressRoute premium add-on voor uw bestaande circuit inschakelen met behulp van het volgende PowerShell-fragment:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Het circuit heeft nu de ExpressRoute premium add-on functies ingeschakeld. We beginnen met het factureren van u voor de premium add-on mogelijkheid zodra de opdracht is uitgevoerd.

### <a name="to-disable-the-expressroute-premium-add-on"></a>De ExpressRoute premium add-on uitschakelen
> [!IMPORTANT]
> Als u resources gebruikt die groter zijn dan wat is toegestaan voor het standaardcircuit, kan deze bewerking mislukken.
>
>

Let op de volgende informatie:

* Voordat u downgraden van premium naar standaard, moet u ervoor zorgen dat het aantal virtuele netwerken die zijn gekoppeld aan het circuit is minder dan 10. Als u dit niet doet, mislukt uw updateverzoek en factureren we u tegen premiumtarieven.
* U moet alle virtuele netwerken in andere geopolitieke regio's ontkoppelen. Als u dit niet doet, mislukt uw updateverzoek en factureren we u tegen premiumtarieven.
* Uw routetabel moet minder dan 4.000 routes hebben voor privé-peering. Als de grootte van uw routetabel groter is dan 4.000 routes, daalt de BGP-sessie en wordt deze pas opnieuw ingeschakeld als het aantal geadverteerde voorvoegsels onder de 4.000 komt.

U de ExpressRoute premium add-on voor het bestaande circuit uitschakelen met behulp van de volgende PowerShell-cmdlet:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>De bandbreedte van het ExpressRoute-circuit bijwerken
Voor ondersteunde bandbreedteopties voor uw provider raadpleegt u de [veelgestelde vragen van ExpressRoute.](expressroute-faqs.md) U elke grootte kiezen die groter is dan de grootte van uw bestaande circuit.

> [!IMPORTANT]
> Het kan zijn dat u het ExpressRoute-circuit opnieuw moet maken als er onvoldoende capaciteit is op de bestaande poort. U het circuit niet upgraden als er op die locatie geen extra capaciteit beschikbaar is.
>
> U de bandbreedte van een ExpressRoute-circuit niet zonder onderbreking verminderen. Voor het verlagen van de bandbreedte moet u het ExpressRoute-circuit deprovisioneren en vervolgens een nieuw ExpressRoute-circuit opnieuw inrichten.
>

Nadat u hebt bepaald welke grootte u nodig hebt, gebruikt u de volgende opdracht om het formaat van uw circuit te wijzigen:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```


Uw circuit wordt aan de Microsoft-kant zo groot. Vervolgens moet u contact opnemen met uw connectiviteitsprovider om configuraties aan hun kant bij te werken om deze wijziging te kunnen aanpassen. Nadat u deze melding hebt gedaan, beginnen we u te factureren voor de bijgewerkte bandbreedteoptie.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Om de SKU te verplaatsen van gemeten naar onbeperkt
U de SKU van een ExpressRoute-circuit wijzigen met behulp van het volgende PowerShell-fragment:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>De toegang tot de klassieke en Resource Manager-omgevingen beheren
Bekijk de instructies in [de circuits Move ExpressRoute, van de klassieke naar het implementatiemodel resourcemanager.](expressroute-howto-move-arm.md)

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Deprovisioning and deleting an ExpressRoute circuit (Inrichting van een ExpressRoute-circuit ongedaan maken en het circuit verwijderen)
Let op de volgende informatie:

* U moet alle virtuele netwerken loskoppelen van het ExpressRoute-circuit. Als deze bewerking mislukt, controleert u of er virtuele netwerken zijn gekoppeld aan het circuit.
* Als de ExpressRoute-dienstverlener provisioning state is **Provisioning** or **Provisioned,** moet u samenwerken met uw serviceprovider om het circuit aan hun kant te deprovisioneren. We blijven resources reserveren en u factureren totdat de serviceprovider het circuit heeft voltooid en ons hiervan op de hoogte stelt.
* Als de serviceprovider het circuit heeft gedeprovisioneerd (de status van de serviceprovider is ingesteld op **Niet-ingericht),** u het circuit verwijderen. Hiermee wordt ook de facturering voor het circuit gestopt.

U uw ExpressRoute-circuit verwijderen door de volgende opdracht uit te voeren:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Volgende stappen

Nadat u uw circuit hebt gemaakt, moet u ervoor zorgen dat u de volgende stappen volgt:

* [Routering voor uw ExpressRoute-circuit maken en wijzigen](expressroute-howto-routing-arm.md)
* [Koppel uw virtuele netwerk aan uw ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)
