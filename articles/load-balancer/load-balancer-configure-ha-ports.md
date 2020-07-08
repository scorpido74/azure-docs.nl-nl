---
title: Poorten met hoge Beschik baarheid configureren voor Azure Load Balancer
titleSuffix: Azure Load Balancer
description: Meer informatie over het gebruik van poorten met hoge Beschik baarheid voor intern verkeer voor taak verdeling op alle poorten
services: load-balancer
documentationcenter: na
author: rdhillon
manager: narayan
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: allensu
ms.openlocfilehash: a71a01629f334534d8eb26847a8f3400efbbeafe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807822"
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Poorten met hoge Beschik baarheid configureren voor een interne load balancer

Dit artikel bevat een voorbeeld implementatie van poorten met hoge Beschik baarheid in een interne load balancer. Zie de bijbehorende provider websites voor meer informatie over configuraties die specifiek zijn voor virtuele netwerk apparaten (Nva's).

>[!NOTE]
>Azure Load Balancer ondersteunt twee verschillende typen: Basic en Standard. In dit artikel worden Standard Load Balancer beschreven. Zie [Load Balancer Overview](load-balancer-overview.md)voor meer informatie over basis Load Balancer.

In de afbeelding ziet u de volgende configuratie van het implementatie voorbeeld dat in dit artikel wordt beschreven:

- De Nva's worden geïmplementeerd in de back-end-pool van een interne load balancer achter de configuratie van poorten met hoge Beschik baarheid. 
- De door de gebruiker gedefinieerde route (UDR) die wordt toegepast op het DMZ-subnet, stuurt al het verkeer naar de Nva's door de volgende hop als interne load balancer virtueel IP-adres te maken. 
- Met de interne load balancer wordt het verkeer naar een van de actieve Nva's gedistribueerd op basis van het load balancer algoritme.
- De NVA verwerkt het verkeer en stuurt deze door naar de oorspronkelijke bestemming in het back-end-subnet.
- Het pad naar de retour waarde kan dezelfde route uitvoeren als een bijbehorende UDR is geconfigureerd in het back-end-subnet. 

![Voorbeeld implementatie poorten met hoge Beschik baarheid](./media/load-balancer-configure-ha-ports/haports.png)

## <a name="configure-high-availability-ports"></a>Poorten met hoge Beschik baarheid configureren

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u poorten met een hoge Beschik baarheid wilt configureren, stelt u een interne load balancer in met de Nva's in de back-end-pool. Stel een overeenkomende load balancer Health probe-configuratie in om de NVA-status en de load balancer regel te detecteren met poorten met hoge Beschik baarheid. De algemene configuratie met betrekking tot load balancer wordt behandeld in [aan de slag](load-balancer-get-started-ilb-arm-portal.md). In dit artikel wordt de configuratie van poorten met hoge Beschik baarheid gemarkeerd.

De configuratie in feite omvat het instellen van de front-end-poort en de waarde voor de back-end-poort op **0**. Stel de protocol waarde in op **alle**. In dit artikel wordt beschreven hoe u poorten met een hoge Beschik baarheid configureert met behulp van de Azure Portal, Power shell en Azure CLI.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Configureer een Maxi maal beschik bare poort load balancer regel met de Azure Portal

Schakel het selectie vakje **ha-poorten** in om poorten met hoge Beschik baarheid te configureren met behulp van de Azure Portal. Als deze functie is geselecteerd, wordt de gerelateerde poort en protocol configuratie automatisch ingevuld. 

![Configuratie van poorten met hoge Beschik baarheid via de Azure Portal](./media/load-balancer-configure-ha-ports/haports-portal.png)

### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Een taakverdelings regel voor poorten met hoge Beschik baarheid configureren via de Resource Manager-sjabloon

U kunt poorten voor hoge Beschik baarheid configureren met behulp van de 2017-08-01 API-versie voor micro soft. Network/loadBalancers in de Load Balancer resource. In het volgende JSON-code fragment ziet u de wijzigingen in de configuratie van de load balancer voor poorten met hoge Beschik baarheid via de REST API:

```json
    {
        "apiVersion": "2017-08-01",
        "type": "Microsoft.Network/loadBalancers",
        ...
        "sku":
        {
            "name": "Standard"
        },
        ...
        "properties": {
            "frontendIpConfigurations": [...],
            "backendAddressPools": [...],
            "probes": [...],
            "loadBalancingRules": [
             {
                "properties": {
                    ...
                    "protocol": "All",
                    "frontendPort": 0,
                    "backendPort": 0
                }
             }
            ],
       ...
       }
    }
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Een Maxi maal beschik bare poorten configureren load balancer regel met Power shell

Gebruik de volgende opdracht om de poorten voor maximale Beschik baarheid te maken load balancer regel tijdens het maken van de interne load balancer met Power shell:

```powershell
lbrule = New-AzLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli"></a>Een Maxi maal beschik bare poorten configureren load balancer regel met Azure CLI

In stap 4 van [een set met interne Load Balancer maken](load-balancer-get-started-ilb-arm-cli.md), gebruikt u de volgende opdracht om de poorten voor maximale Beschik baarheid te maken Load Balancer regel:

```azurecli
az network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [poorten voor hoge Beschik baarheid](load-balancer-ha-ports-overview.md).