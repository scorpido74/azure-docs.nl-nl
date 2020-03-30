---
title: 'Azure ExpressRoute: circuitpeer en-peering opnieuw instellen'
description: ExpressRoute-circuitpeeringen uitschakelen en inschakelen.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/13/2018
ms.author: charwen
ms.openlocfilehash: 9f32eb439872de9e4687d046745c03bafd86b2fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941739"
---
# <a name="reset-expressroute-circuit-peerings"></a>ExpressRoute-circuitpeeringen opnieuw instellen

In dit artikel wordt beschreven hoe u peerings van een ExpressRoute-circuit uitschakelen en inschakelen met PowerShell. Wanneer u een peering uitschakelt, wordt de BGP-sessie op zowel de primaire verbinding als de secundaire verbinding van uw ExpressRoute-circuit afgesloten. U verliest connectiviteit door deze peering naar Microsoft. Wanneer u een peering inschakelt, wordt de BGP-sessie op zowel de primaire verbinding als de secundaire verbinding van uw ExpressRoute-circuit weergegeven. U zult de connectiviteit herwinnen door deze peering naar Microsoft. U Microsoft Peering en Azure Private Peering op een ExpressRoute-circuit onafhankelijk inschakelen en uitschakelen. Wanneer u de peeringen voor het eerst configureert op uw ExpressRoute-circuit, worden de peeringen standaard ingeschakeld.

Er zijn een paar scenario's waarin u het nuttig vindt om uw ExpressRoute-peerings opnieuw in te stellen.
* Test uw ontwerp en implementatie voor noodherstel. U hebt bijvoorbeeld twee ExpressRoute-circuits. U de peerings van het ene circuit uitschakelen en uw netwerkverkeer naar het andere circuit laten mislukken.
* Schakel BFD (Bidirectionele Forwarding Detection) in op Azure Private Peering of Microsoft Peering van uw ExpressRoute-circuit. BFD is standaard ingeschakeld op Azure Private Peering als uw ExpressRoute-circuit is gemaakt na 1 augustus 2018 en op Microsoft Peering als uw ExpressRoute-circuit na 10 januari 2020 is gemaakt. Als uw circuit daarvoor is gemaakt, is BFD niet ingeschakeld. U BFD inschakelen door het peering uit te schakelen en opnieuw in te schakelen. 

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Een peering opnieuw instellen

1. Als u PowerShell lokaal uitvoert, opent u uw PowerShell-console met verhoogde bevoegdheden en maakt u verbinding met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

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
5. Identificeer de peering die u wilt uitschakelen of inschakelen. *Peerings* is een array. In het volgende voorbeeld is Peerings[0] Azure Private Peering en Peerings[1] Microsoft Peering.

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
6. Voer de volgende opdrachten uit om de status van het peering te wijzigen.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   Het peering moet in een staat zijn die u instelt. 

## <a name="next-steps"></a>Volgende stappen
Als u hulp nodig hebt om een Probleem met ExpressRoute op te lossen, raadpleegt u de volgende artikelen:
* [Connectiviteit ExpressRoute controleren](expressroute-troubleshooting-expressroute-overview.md)
* [Problemen met de netwerkprestaties oplossen](expressroute-troubleshooting-network-performance.md)
