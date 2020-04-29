---
title: Een Azure Load Balancer-regel voor een cluster maken
description: Configureer een Azure Load Balancer voor het openen van poorten voor uw Azure Service Fabric-cluster.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: f4599b2e0174381ab7df04aeeb33db7e3ee60f26
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77025381"
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Poorten openen voor een Service Fabric-cluster

De load balancer die met uw Azure Service Fabric cluster wordt geïmplementeerd, stuurt verkeer naar uw app die op een knoop punt wordt uitgevoerd. Als u de app wijzigt om een andere poort te gebruiken, moet u die poort zichtbaar maken (of een andere poort routeren) in de Azure Load Balancer.

Wanneer u uw Service Fabric-cluster in azure hebt geïmplementeerd, is er automatisch een load balancer voor u gemaakt. Als u geen load balancer hebt, raadpleegt u [een Internet gerichte Load Balancer configureren](../load-balancer/load-balancer-get-started-internet-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-service-fabric"></a>Service Fabric configureren

Het configuratie bestand **ServiceManifest. XML** van service Fabric toepassing definieert de eind punten die uw toepassing verwacht te gebruiken. Nadat het configuratie bestand is bijgewerkt om een eind punt te definiëren, moeten de load balancer worden bijgewerkt om die (of een andere) poort beschikbaar te maken. Zie [een eind punt instellen](service-fabric-service-manifest-resources.md)voor meer informatie over het maken van het service Fabric-eind punt.

## <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Met een Load Balancer regel wordt een Internet gerichte poort geopend en verkeer doorgestuurd naar de poort van het interne knoop punt dat door uw toepassing wordt gebruikt. Als u geen load balancer hebt, raadpleegt u [een Internet gerichte Load Balancer configureren](../load-balancer/load-balancer-get-started-internet-portal.md).

Als u een Load Balancer regel wilt maken, moet u de volgende informatie verzamelen:

- Naam van Load Balancer.
- De resource groep van de load balancer en het service Fabric-cluster.
- Externe poort.
- Interne poort.

## <a name="azure-cli"></a>Azure CLI
Er wordt slechts één opdracht gebruikt om een load balancer regel te maken met de **Azure cli**. U hoeft alleen de naam van de load balancer en de resource groep te kennen om een nieuwe regel te maken.

>[!NOTE]
>Als u de naam van de load balancer wilt bepalen, gebruikt u deze opdracht om snel een lijst met alle load balancers en de bijbehorende resource groepen weer te geven.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

De Azure CLI-opdracht heeft een aantal para meters die in de volgende tabel worden beschreven:

| Parameter | Beschrijving |
| --------- | ----------- |
| `--backend-port`  | De poort waarnaar de Service Fabric toepassing luistert. |
| `--frontend-port` | De poort die door de load balancer wordt weer gegeven voor externe verbindingen. |
| `-lb-name` | De naam van de load balancer die moet worden gewijzigd. |
| `-g`       | De resource groep met zowel het load balancer als het Service Fabric cluster. |
| `-n`       | De gewenste naam van de regel. |


>[!NOTE]
>Zie [een Load Balancer maken met de Azure cli](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)voor meer informatie over het maken van een Load Balancer met de Azure cli.

## <a name="powershell"></a>PowerShell

Power shell is iets gecompliceerder dan de Azure CLI. Volg deze conceptuele stappen om een regel te maken:

1. Haal de load balancer van Azure.
2. Een regel maken.
3. Voeg de regel toe aan de load balancer.
4. Werk de load balancer bij.

>[!NOTE]
>Als u de naam van de load balancer wilt bepalen, gebruikt u deze opdracht om snel een lijst met alle load balancers en gekoppelde resource groepen weer te geven.
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

Met betrekking `New-AzLoadBalancerRuleConfig` tot de opdracht `-FrontendPort` vertegenwoordigt de poort de Load Balancer beschikbaar voor externe verbindingen en geeft het `-BackendPort` de poort aan waarnaar de service Fabric-app luistert.

>[!NOTE]
>Zie [een Load Balancer maken met Power shell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)voor meer informatie over het maken van een Load Balancer met Power shell.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [netwerken in service Fabric](service-fabric-patterns-networking.md).
