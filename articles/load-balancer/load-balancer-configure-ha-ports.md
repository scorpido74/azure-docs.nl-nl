---
title: Poorten met hoge beschikbaarheid configureren voor Azure Load Balancer
titleSuffix: Azure Load Balancer
description: Meer informatie over het gebruik van poorten met hoge beschikbaarheid voor het balanceren van intern verkeer op alle poorten
services: load-balancer
documentationcenter: na
author: rdhillon
manager: narayan
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: allensu
ms.openlocfilehash: c6b8ecb443408f23ae604bd9c8139cb0a2afcd12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477779"
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Poorten met hoge beschikbaarheid configureren voor een interne load balancer

In dit artikel vindt u een voorbeeld van de implementatie van poorten met hoge beschikbaarheid op een interne load balancer. Zie de bijbehorende providerwebsites voor meer informatie over configuraties die specifiek zijn voor virtuele netwerkapparaten (NVA's).

>[!NOTE]
>Azure Load Balancer ondersteunt twee verschillende typen: Basic en Standard. In dit artikel wordt gesproken over Standard Load Balancer. Zie Overzicht van [load balancer](load-balancer-overview.md)voor meer informatie over Basic Load Balancer.

De illustratie toont de volgende configuratie van het implementatievoorbeeld dat in dit artikel wordt beschreven:

- De NVA's worden geïmplementeerd in de back-end pool van een interne load balancer achter de configuratie van high availability ports. 
- De door de gebruiker gedefinieerde route (UDR) die op het DMZ-subnet wordt toegepast, leidt al het verkeer naar de NVA's door de volgende hop te maken als virtuele IP-interne load balancer. 
- De interne load balancer verdeelt het verkeer naar een van de actieve NVA's volgens het load balancer algoritme.
- De NVA verwerkt het verkeer en stuurt het door naar de oorspronkelijke bestemming in het back-end subnet.
- Het retourpad kan dezelfde route nemen als een overeenkomstige UDR is geconfigureerd in het back-endsubnet. 

![Voorbeeldimplementatie van poorten met hoge beschikbaarheid](./media/load-balancer-configure-ha-ports/haports.png)

## <a name="configure-high-availability-ports"></a>Poorten met hoge beschikbaarheid configureren

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u poorten met hoge beschikbaarheid wilt configureren, stelt u een interne load balancer in met de NVA's in de back-endpool. Stel een bijbehorende configuratie van de statussonde sonde van de load balancer in om de NVA-status en de regel van de load balancer te detecteren met poorten met hoge beschikbaarheid. De algemene configuratie met load balancer wordt behandeld in [Aan de slag](load-balancer-get-started-ilb-arm-portal.md). In dit artikel wordt de configuratie van poorten met hoge beschikbaarheid belicht.

De configuratie omvat in wezen het instellen van de front-end poort en de back-end poortwaarde op **0**. Stel de protocolwaarde in op **Alles**. In dit artikel wordt beschreven hoe u poorten met hoge beschikbaarheid configureert met behulp van de Azure-portal, PowerShell en Azure CLI.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Een load balancer-regel voor poorten met hoge beschikbaarheid configureren met de Azure-portal

Als u poorten met hoge beschikbaarheid wilt configureren met behulp van de Azure-portal, schakelt u het selectievakje **HA-poorten** in. Wanneer deze optie is geselecteerd, wordt de bijbehorende poort- en protocolconfiguratie automatisch ingevuld. 

![Configuratie van poorten met hoge beschikbaarheid via de Azure-portal](./media/load-balancer-configure-ha-ports/haports-portal.png)

### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Een regel voor de taakverdeling van poorten met hoge beschikbaarheid configureren via de sjabloon Resourcebeheer

U poorten met hoge beschikbaarheid configureren met behulp van de API-versie 2017-08-01 voor Microsoft.Network/loadBalancers in de load balancer-bron. Het volgende JSON-fragment illustreert de wijzigingen in de configuratie van de load balancer voor poorten met hoge beschikbaarheid via de REST API:

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

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Een load balancer-regel voor poorten met hoge beschikbaarheid configureren met PowerShell

Gebruik de volgende opdracht om de load balancer-regel voor poorten met hoge beschikbaarheid te maken terwijl u de interne load balancer maakt met PowerShell:

```powershell
lbrule = New-AzLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli"></a>Een load balancer-regel voor poorten met hoge beschikbaarheid configureren met Azure CLI

Gebruik in stap 4 van [Een interne load balancerset maken](load-balancer-get-started-ilb-arm-cli.md)de volgende opdracht om de regel voor de load balancer van de ladensbalans met hoge beschikbaarheid te maken:

```azurecli
az network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [poorten met hoge beschikbaarheid](load-balancer-ha-ports-overview.md).