---
title: Een interne Load Balancer maken voor Azure Cloud Services - klassieke implementatie
titlesuffix: Azure Load Balancer
description: Meer informatie over hoe u met PowerShell een interne load balancer maakt in het klassieke implementatiemodel
services: load-balancer
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: e0df334cb4836138f9f7b9c70d299d87d159fefc
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090910"
---
# <a name="get-started-creating-an-internal-load-balancer-classic-for-cloud-services"></a>Aan de slag met het maken van een interne load balancer (klassiek) voor cloudservices

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [Azure-CLI](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Cloudservices](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

> [!IMPORTANT]
> Azure heeft twee verschillende implementatiemodellen voor het maken van en werken met resources:  [Resource Manager en het klassieke model](../azure-resource-manager/resource-manager-deployment-model.md).  Dit artikel gaat over het gebruik van het klassieke implementatiemodel. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken. Lees [meer informatie over het uitvoeren van deze stappen met het Resource Manager-model](load-balancer-get-started-ilb-arm-ps.md).

## <a name="configure-internal-load-balancer-for-cloud-services"></a>Een interne load balancer configureren voor cloudservices

Een interne load balancer wordt ondersteund voor zowel virtuele machines als cloudservices. Een eindpunt van een interne load balancer in een cloudservice buiten een regionaal virtueel netwerk is alleen toegankelijk in de cloudservice.

De interne load balancer moet worden geconfigureerd wanneer de eerste implementatie in de cloudservice wordt gemaakt, zoals u in het voorbeeld hieronder kunt zien.

> [!IMPORTANT]
> Om de volgende stappen uit te kunnen voeren, moet er al een virtueel netwerk voor de cloudimplementatie zijn gemaakt. U hebt de naam van het virtuele netwerk en van het subnet nodig om de Interne taakverdeling te kunnen maken.

### <a name="step-1"></a>Stap 1

Open het serviceconfiguratiebestand (.cscfg) voor de cloudimplementatie in Visual Studio en voeg de volgende sectie toe om de Interne taakverdeling te maken onder het laatste '`</Role>`'-item van de netwerkconfiguratie.

```xml
<NetworkConfiguration>
    <LoadBalancers>
    <LoadBalancer name="name of the load balancer">
        <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
    </LoadBalancer>
    </LoadBalancers>
</NetworkConfiguration>
```

U gaat nu de waarden voor het netwerkconfiguratiebestand toevoegen om te zien hoe het eruitziet. In het voorbeeld wordt ervan uitgegaan dat u een Vnet test_vnet hebt gemaakt met subnet 10.0.0.0/24 met de naam test_subnet en een statisch IP-adres 10.0.0.4. De load balancer wordt testLB genoemd.

```xml
<NetworkConfiguration>
    <LoadBalancers>
    <LoadBalancer name="testLB">
        <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
    </LoadBalancer>
    </LoadBalancers>
</NetworkConfiguration>
```

Voor meer informatie over het load balancer-schema raadpleegt u [Add Load Balancer](https://msdn.microsoft.com/library/azure/dn722411.aspx) (Load balancer toevoegen).

### <a name="step-2"></a>Stap 2

Wijzig het servicedefinitiebestand (.csdef) om eindpunten aan de Interne taakverdeling toe te voegen. Op het moment dat er een rolinstantie wordt gemaakt, voegt het servicedefinitiebestand de rolinstanties aan de Interne taakverdeling toe.

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
    </Endpoints>
</WorkerRole>
```

U gaat nu met dezelfde waarden uit bovenstaand voorbeeld de waarden aan het servicedefinitiebestand toevoegen.

```xml
<WorkerRole name="WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
    </Endpoints>
</WorkerRole>
```

Het netwerkverkeer krijgt een gelijke taakverdeling met load balancer testLB die poort 80 gebruikt voor binnenkomende aanvragen en verzendt naar werkrolinstanties, eveneens op poort 80.

## <a name="next-steps"></a>Volgende stappen

[Een distributiemodus voor de load balancer configureren met bron-IP-affiniteit](load-balancer-distribution-mode.md)

[TCP-time-outinstellingen voor inactiviteit voor de load balancer configureren](load-balancer-tcp-idle-timeout.md)

