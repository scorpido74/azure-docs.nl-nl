---
title: 'Azure ExpressRoute: ExpressRoute Global Reach configureren: CLI'
description: Met dit artikel u ExpressRoute-circuits koppelen om een privénetwerk te maken tussen uw on-premises netwerken en Global Reach mogelijk te maken.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/12/2018
ms.author: jaredro
ms.openlocfilehash: a39cf4e09a70ca2b1225d699c84abf0e7f1d2eab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476403"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>ExpressRoute Global Reach configureren met behulp van de Azure CLI

Met dit artikel u Azure ExpressRoute Global Reach configureren met behulp van de Azure CLI. Zie [ExpressRoute Global Reach](expressroute-global-reach.md) voor meer informatie.
 
Voer de volgende vereisten uit voordat u de configuratie start:

* Installeer de nieuwste versie van de Azure CLI. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) en [Aan de slag met de Azure CLI](/cli/azure/get-started-with-azure-cli).
* Begrijp de expressroute-werkstromen voor het inrichten van [het circuit.](expressroute-workflows.md)
* Zorg ervoor dat uw ExpressRoute-circuits in de ingerichte status staan.
* Controleer of Azure private peering is geconfigureerd op uw ExpressRoute-circuits.  

### <a name="sign-in-to-your-azure-account"></a>Aanmelden bij uw Azure-account

Als u de configuratie wilt starten, meldt u zich aan bij uw Azure-account. Met de volgende opdracht wordt uw standaardbrowser geopend en wordt u gevraagd om de aanmeldingsreferenties voor uw Azure-account:  

```azurecli
az login
```

Als u meerdere Azure-abonnementen hebt, controleert u de abonnementen voor het account:

```azurecli
az account list
```

Geef het abonnement op dat u wilt gebruiken:

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Uw ExpressRoute-circuits identificeren voor configuratie

U ExpressRoute Global Reach inschakelen tussen twee ExpressRoute-circuits, zolang ze zich in ondersteunde landen/regio's bevinden en op verschillende peeringlocaties zijn gemaakt. Als uw abonnement eigenaar is van beide circuits, u kiezen voor een van beide circuits om de configuratie uit te voeren, zoals later in dit artikel wordt uitgelegd. Als de twee circuits zich in verschillende Azure-abonnementen bevinden, moet u over autorisatie van één Azure-abonnement beschikken en moet u de autorisatiesleutel doorgeven wanneer u de configuratieopdracht uitvoert in het andere Azure-abonnement.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Connectiviteit tussen uw on-premises netwerken inschakelen

Wanneer u de opdracht uitvoert om connectiviteit in te schakelen, moet u de volgende vereisten voor parameterwaarden noteren:

* *peer-circuit* moet de volledige resource-ID zijn. Bijvoorbeeld:

  > /abonnementen/{your_subscription_id}/resourceGroepen/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}

* *adres-voorvoegsel* moet een "/29" IPv4-subnet zijn (bijvoorbeeld "10.0.0.0/29"). We gebruiken IP-adressen in dit subnet om de connectiviteit tussen de twee ExpressRoute-circuits tot stand te brengen. U mag geen adressen in dit subnet gebruiken in uw virtuele Azure-netwerken of in uw on-premises netwerken.

Voer de volgende OPDRACHT CLI uit om twee ExpressRoute-circuits aan te sluiten:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

De CLI-uitvoer ziet er als volgt uit:

```output
{
  "addressPrefix": "<__.__.__.__/29>",
  "authorizationKey": null,
  "circuitConnectionStatus": "Connected",
  "etag": "W/\"48d682f9-c232-4151-a09f-fab7cb56369a\"",
  "expressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<ResourceGroupName>"
  },
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering/connections/<ConnectionName>",
  "name": "<ConnectionName>",
  "peerExpressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<Circuit2ResourceGroupName>"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "<ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/peerings/connections"
}
```

Wanneer deze bewerking is voltooid, hebt u verbinding tussen uw on-premises netwerken aan beide zijden via uw twee ExpressRoute-circuits.

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>Connectiviteit tussen ExpressRoute-circuits inschakelen in verschillende Azure-abonnementen

Als de twee circuits niet in hetzelfde Azure-abonnement zitten, hebt u autorisatie nodig. In de volgende configuratie genereert u autorisatie in het abonnement van Circuit 2 en geeft u de autorisatiesleutel door aan circuit 1.

1. Een autorisatiesleutel genereren:

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   De CLI-uitvoer ziet er als volgt uit:

   ```output
   {
     "authorizationKey": "<authorizationKey>",
     "authorizationUseStatus": "Available",
     "etag": "W/\"cfd15a2f-43a1-4361-9403-6a0be00746ed\"",
     "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/authorizations/<AuthorizationName>",
     "name": "<AuthorizationName>",
     "provisioningState": "Succeeded",
     "resourceGroup": "<Circuit2ResourceGroupName>",
     "type": "Microsoft.Network/expressRouteCircuits/authorizations"
   }
   ```

1. Noteer zowel de resource-id als de autorisatiesleutel voor circuit 2.

1. Voer de volgende opdracht uit tegen circuit 1 en passeer de resource-id en autorisatiesleutel van circuit 2:

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

Wanneer deze bewerking is voltooid, hebt u verbinding tussen uw on-premises netwerken aan beide zijden via uw twee ExpressRoute-circuits.

## <a name="get-and-verify-the-configuration"></a>De configuratie oppakken en verifiëren

Gebruik de volgende opdracht om de configuratie te verifiëren op het circuit waar de configuratie is gemaakt (circuit 1 in het voorgaande voorbeeld):

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

In de CLI-uitvoer ziet u *CircuitConnectionStatus*. Het vertelt u of de verbinding tussen de twee circuits is vastgesteld ("Verbonden") of niet vastgesteld ("Losgekoppeld"). 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Connectiviteit tussen uw on-premises netwerken uitschakelen

Voer de volgende opdracht uit tegen het circuit waar de configuratie is gemaakt (circuit 1 in het eerdere voorbeeld).

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Gebruik ```show``` de opdracht om de status te verifiëren.

Wanneer deze bewerking is voltooid, hebt u geen verbinding meer tussen uw on-premises netwerken via uw ExpressRoute-circuits.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over ExpressRoute Global Reach](expressroute-global-reach.md)
* [ExpressRoute-connectiviteit verifiëren](expressroute-troubleshooting-expressroute-overview.md)
* [Een ExpressRoute-circuit koppelen aan een virtueel netwerk](expressroute-howto-linkvnet-arm.md)
