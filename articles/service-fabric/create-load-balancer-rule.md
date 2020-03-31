---
title: Een Azure Load Balancer-regel voor een cluster maken
description: Configureer een Azure Load Balancer om poorten te openen voor uw Azure Service Fabric-cluster.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: f4599b2e0174381ab7df04aeeb33db7e3ee60f26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025381"
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Poorten openen voor een Service Fabric-cluster

De load balancer die is geïmplementeerd met uw Azure Service Fabric-cluster leidt verkeer naar uw app die op een knooppunt wordt uitgevoerd. Als u uw app wijzigt om een andere poort te gebruiken, moet u die poort (of route een andere poort) in de Azure Load Balancer blootleggen.

Toen u uw Cluster Servicefabric in Azure hebt geïmplementeerd, is er automatisch een load balancer voor u gemaakt. Zie [Een op internet gerichte load balancer configureren](../load-balancer/load-balancer-get-started-internet-portal.md)als u geen load balancer hebt.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-service-fabric"></a>Servicefabric configureren

Het **config-bestand ServiceFabric-toepassing ServiceManifest.xml** definieert de eindpunten die uw toepassing verwacht te gebruiken. Nadat het config-bestand is bijgewerkt om een eindpunt te definiëren, moet de load balancer worden bijgewerkt om die (of een andere) poort bloot te leggen. Zie [Een eindpunt instellen](service-fabric-service-manifest-resources.md)voor meer informatie over het maken van het eindpunt van de servicefabric.

## <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Een load balancer-regel opent een poort op interneten en stuurt verkeer door naar de poort van het interne knooppunt die door uw toepassing wordt gebruikt. Zie [Een op internet gerichte load balancer configureren](../load-balancer/load-balancer-get-started-internet-portal.md)als u geen load balancer hebt.

Als u een regel load balancer wilt maken, moet u de volgende gegevens verzamelen:

- Naam van de load balancer.
- Resourcegroep van het cluster load balancer en servicefabric.
- Externe poort.
- Interne poort.

## <a name="azure-cli"></a>Azure-CLI
Er is slechts één opdracht nodig om een regel voor de load balancer te maken met de **Azure CLI.** U hoeft alleen maar de naam van de load balancer en resourcegroep te kennen om een nieuwe regel te maken.

>[!NOTE]
>Als u de naam van de load balancer wilt bepalen, gebruikt u deze opdracht om snel een lijst te krijgen met alle load balancers en de bijbehorende resourcegroepen.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

De opdracht Azure CLI heeft een aantal parameters die in de volgende tabel worden beschreven:

| Parameter | Beschrijving |
| --------- | ----------- |
| `--backend-port`  | De poort waar de Service Fabric-applicatie naar luistert. |
| `--frontend-port` | De poort die de load balancer blootstelt voor externe verbindingen. |
| `-lb-name` | De naam van de load balancer om te veranderen. |
| `-g`       | De resourcegroep met zowel het cluster load balancer als Service Fabric. |
| `-n`       | De gewenste naam van de regel. |


>[!NOTE]
>Zie [Een load balancer maken met de Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)voor meer informatie over het maken van een load balancer met de Azure CLI.

## <a name="powershell"></a>PowerShell

PowerShell is een beetje ingewikkelder dan de Azure CLI. Volg deze conceptuele stappen om een regel te maken:

1. Haal de load balancer uit Azure.
2. Maak een regel.
3. Voeg de regel toe aan de load balancer.
4. Werk de load balancer bij.

>[!NOTE]
>Als u de naam van de load balancer wilt bepalen, gebruikt u deze opdracht om snel een lijst te krijgen met alle load balancers en bijbehorende resourcegroepen.
>
>`Get-AzLoadBalancer | Select Name, ResourceGroupName`

```powershell
# Get the load balancer
$lb = Get-AzLoadBalancer -Name LB-svcfab3 -ResourceGroupName svcfab_cli

# Create the rule based on information from the load balancer.
$lbrule = New-AzLoadBalancerRuleConfig -Name my-app-rule7 -Protocol Tcp -FrontendPort 39990 -BackendPort 40009 `
                                            -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
                                            -BackendAddressPool  $lb.BackendAddressPools[0] `
                                            -Probe $lb.Probes[0]

# Add the rule to the load balancer
$lb.LoadBalancingRules.Add($lbrule)

# Update the load balancer on Azure
$lb | Set-AzLoadBalancer
```

Wat `New-AzLoadBalancerRuleConfig` de opdracht `-FrontendPort` betreft, vertegenwoordigt de poort die de load `-BackendPort` balancer blootstelt voor externe verbindingen en de poort waar de servicefabric-app naar luistert.

>[!NOTE]
>Zie [Een load balancer maken met PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)voor meer informatie over het maken van een load balancer met PowerShell.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [netwerken in Service Fabric](service-fabric-patterns-networking.md).
