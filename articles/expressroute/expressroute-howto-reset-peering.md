---
title: 'Azure-ExpressRoute: een circuit peering opnieuw instellen'
description: Het ExpressRoute-circuitpeerings in- en uitschakelen.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/13/2018
ms.author: charwen
ms.openlocfilehash: 9f32eb439872de9e4687d046745c03bafd86b2fa
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941739"
---
# <a name="reset-expressroute-circuit-peerings"></a>Opnieuw instellen van ExpressRoute-circuitpeerings

In dit artikel wordt beschreven hoe u uitschakelen en inschakelen van peerings van een ExpressRoute-circuit met behulp van PowerShell. Wanneer u een peering de uitschakelt, wordt de BGP-sessie op de primaire verbinding en de secundaire verbinding van uw ExpressRoute-circuit worden afgesloten. U verliest connectiviteit via deze peering naar Microsoft. Wanneer u een peering hebt ingeschakeld, wordt de BGP-sessie op de primaire verbinding en de secundaire verbinding van uw ExpressRoute-circuit van worden gebracht. U wordt opnieuw verbonden is via deze peering naar Microsoft. U kunt in- en Microsoft-Peering en Privépeering van Azure onafhankelijk van een ExpressRoute-circuit uitschakelen. Wanneer u eerst de peerings op uw ExpressRoute-circuit configureert, wordt de peerings zijn standaard ingeschakeld.

Er zijn een aantal scenario's waarin u mogelijk nuttig opnieuw instellen van uw ExpressRoute-peerings.
* Test uw disaster recovery ontwerpen en implementeren. Bijvoorbeeld, hebt u twee ExpressRoute-circuits. U kunt de peerings van één aansluiting uitschakelen en afdwingen van uw verkeer failover naar het andere circuit.
* Schakel de detectie van bidirectionele door sturing (BFD) in op persoonlijke Azure-peering of micro soft-peering van uw ExpressRoute-circuit. BFD is standaard ingeschakeld voor persoonlijke Azure-peering als uw ExpressRoute-circuit wordt gemaakt na 1 2018 augustus en op micro soft-peering als uw ExpressRoute-circuit na januari 10 2020 is gemaakt. Als uw circuit voordat die is gemaakt, is niet BFD ingeschakeld. U kunt BFD inschakelen door het uitschakelen van de peering en deze opnieuw inschakelen. 

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Opnieuw instellen van een peering

1. Als u PowerShell lokaal uitvoert, opent u de PowerShell-console met verhoogde bevoegdheden en verbinding maken met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

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
4. Voer de volgende opdrachten om op te halen van uw ExpressRoute-circuit.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. Identificeer de peering die u wilt in- of uitschakelen. *Peerings* is een matrix. In het volgende voorbeeld is Peerings [0] persoonlijke Azure-Peering en Microsoft-Peering Peerings [1].

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
6. Voer de volgende opdrachten de status van de peering wijzigen.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   De peering moet zich in een status die u instelt. 

## <a name="next-steps"></a>Volgende stappen
Als u help voor een ExpressRoute-probleem op te lossen, Raadpleeg de volgende artikelen:
* [ExpressRoute-connectiviteit controleren](expressroute-troubleshooting-expressroute-overview.md)
* [Problemen met netwerkprestaties oplossen](expressroute-troubleshooting-network-performance.md)
