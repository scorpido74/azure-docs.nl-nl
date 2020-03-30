---
title: Privé-IP-adres gebruiken voor interne routering voor een binnenkomend eindpunt
description: In dit artikel vindt u informatie over het gebruik van privé-IP's voor interne routering en dus het blootstellen van het Ingress-eindpunt binnen een cluster aan de rest van het VNet.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 570f28ce559ff1c1180ffaacb781b9120b1890a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795494"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>Privé-IP gebruiken voor interne routering voor een Ingress-eindpunt 

Deze functie maakt het mogelijk om `Virtual Network` het binnenkomende eindpunt binnen het gebruik van een privé-IP bloot te leggen.

## <a name="pre-requisites"></a>Vereisten  
Toepassingsgateway met een [privé-IP-configuratie](https://docs.microsoft.com/azure/application-gateway/configure-application-gateway-with-private-frontend-ip)

Er zijn twee manieren om de controller te configureren om privé-IP te gebruiken voor

## <a name="assign-to-a-particular-ingress"></a>Toewijzen aan een bepaalde insgress
Als u een bepaalde ingress boven Private [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) IP wilt blootleggen, gebruikt u annotatie in Ingress.

### <a name="usage"></a>Gebruik
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

Voor toepassingsgateways zonder privé-IP worden insanten die `appgw.ingress.kubernetes.io/use-private-ip: "true"` met ingebruikworden geannoteerd, genegeerd. Dit zal worden aangegeven in de ingress gebeurtenis en AGIC pod log.

* Fout zoals aangegeven in de gebeurtenis Binnengebruik

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* Fout zoals aangegeven in AGIC Logs

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>Globaal toewijzen
In het geval, vereiste is om alle Ingresses `appgw.usePrivateIP: true` te `helm` beperken om te worden blootgesteld via Private IP, gebruik in config.

### <a name="usage"></a>Gebruik
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

Hierdoor filtert de insin-controller de IP-adresconfiguraties voor een privé-IP bij het configureren van de frontend-listeners op de Application Gateway.
AGIC zal in `usePrivateIP: true` paniek raken en crashen als en geen Private IP is toegewezen.

> [!NOTE]
> Application Gateway v2 SKU requires a Public IP. Als u vereist dat Application Gateway [`Network Security Group`](https://docs.microsoft.com/azure/virtual-network/security-overview) privé is, koppelt u een aan het subnet van de Application Gateway om het verkeer te beperken.
