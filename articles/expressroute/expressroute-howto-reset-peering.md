---
title: 'Azure-ExpressRoute: een circuit peering opnieuw instellen'
description: Meer informatie over het uitschakelen en inschakelen van peerings van een Azure ExpressRoute-circuit met behulp van Azure PowerShell. Wanneer u peerings configureert, worden deze standaard ingeschakeld.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 01/13/2018
ms.author: duau
ms.openlocfilehash: f3b34966aa46ca8d663f83ab2aceafa4b0dda2eb
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89395737"
---
# <a name="reset-expressroute-circuit-peerings"></a>ExpressRoute-circuit-peerings opnieuw instellen

In dit artikel wordt beschreven hoe u peerings van een ExpressRoute-circuit kunt uitschakelen en inschakelen met behulp van Power shell. Wanneer u een peering uitschakelt, wordt de BGP-sessie op de primaire verbinding en de secundaire verbinding van het ExpressRoute-circuit afgesloten. De connectiviteit met deze peering naar micro soft gaat verloren. Wanneer u een peering inschakelt, wordt de BGP-sessie op zowel de primaire verbinding als de secundaire verbinding van het ExpressRoute-circuit actief. U kunt de verbinding met deze peering met micro soft herstellen. U kunt micro soft-peering en persoonlijke Azure-peering op een ExpressRoute-circuit onafhankelijk van elkaar in-en uitschakelen. Wanneer u de peerings op uw ExpressRoute-circuit voor het eerst configureert, worden de peerings standaard ingeschakeld.

Er zijn een paar scenario's waarin het handig is om uw ExpressRoute-peerings te herstellen.
* Het ontwerp en de implementatie van herstel na nood gevallen testen. U hebt bijvoorbeeld twee ExpressRoute-circuits. U kunt de peerings van één circuit uitschakelen en uw netwerk verkeer afdwingen naar het andere circuit.
* Schakel de detectie van bidirectionele door sturing (BFD) in op persoonlijke Azure-peering of micro soft-peering van uw ExpressRoute-circuit. BFD is standaard ingeschakeld voor persoonlijke Azure-peering als uw ExpressRoute-circuit wordt gemaakt na 1 2018 augustus en op micro soft-peering als uw ExpressRoute-circuit na januari 10 2020 is gemaakt. Als uw circuit eerder is gemaakt, is BFD niet ingeschakeld. U kunt BFD inschakelen door de peering uit te scha kelen en opnieuw in te scha kelen. 

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Een peering opnieuw instellen

1. Als u Power shell lokaal uitvoert, opent u de Power shell-console met verhoogde bevoegdheden en maakt u verbinding met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

   ```azurepowershell
   Connect-AzAccount
   ```
2. Als u meerdere Azure-abonnementen hebt, controleert u de abonnementen voor het account.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
3. Geef het abonnement op dat u wilt gebruiken.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
4. Voer de volgende opdrachten uit om uw ExpressRoute-circuit op te halen.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. Identificeer de peering die u wilt in-of uitschakelen. *Peerings* is een matrix. In het volgende voor beeld is peerings [0] persoonlijke Azure-peering en peerings [1] micro soft-peering.

   ```azurepowershell-interactive
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"cd011bef-dc79-49eb-b4c6-81fb6ea5d178"
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
                                     "ServiceProviderName": "Coresite",
                                     "PeeringLocation": "Los Angeles",
                                     "BandwidthInMbps": 50
                                   }
   ServiceKey                       : ########-####-####-####-############
   Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "10.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "10.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 789,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "NotConfigured",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     },
                                     {
                                       "Name": "MicrosoftPeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/MicrosoftPeering",
                                       "PeeringType": "MicrosoftPeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "3.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "3.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 345,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [
                                           "3.0.0.3/32"
                                         ],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "ValidationNeeded",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
   Authorizations                   : []
   AllowClassicOperations           : False
   GatewayManagerEtag               :
   ```
6. Voer de volgende opdrachten uit om de status van de peering te wijzigen.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   De peering moet een status hebben die u hebt ingesteld. 

## <a name="next-steps"></a>Volgende stappen
Als u hulp nodig hebt bij het oplossen van een probleem met ExpressRoute, raadpleegt u de volgende artikelen:
* [Connectiviteit ExpressRoute controleren](expressroute-troubleshooting-expressroute-overview.md)
* [Problemen met netwerk prestaties oplossen](expressroute-troubleshooting-network-performance.md)
