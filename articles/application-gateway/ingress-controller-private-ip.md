---
title: Privé IP-adres gebruiken voor interne route ring voor een ingangs eindpunt
description: Dit artikel bevat informatie over het gebruik van privé Ip's voor interne route ring en het beschikbaar maken van het eind punt binnen een cluster naar de rest van het VNet.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 570f28ce559ff1c1180ffaacb781b9120b1890a2
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795494"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>Privé-IP gebruiken voor interne route ring voor een ingangs eindpunt 

Met deze functie kunt u het ingangs eindpunt binnen de `Virtual Network` beschikbaar maken met behulp van een privé-IP.

## <a name="pre-requisites"></a>Vereisten  
Application Gateway met een [persoonlijke IP-configuratie](https://docs.microsoft.com/azure/application-gateway/configure-application-gateway-with-private-frontend-ip)

Er zijn twee manieren om de controller te configureren voor het gebruik van privé-IP-adressen voor inkomend verkeer,

## <a name="assign-to-a-particular-ingress"></a>Toewijzen aan een bepaalde inkomend verkeer
Als u een bepaalde inkomend verkeer via privé-IP beschikbaar wilt maken, gebruikt u aantekening [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) in inkomend verkeer.

### <a name="usage"></a>Gebruik
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

Voor toepassings gateways zonder een persoonlijk IP-adres, Ingresses aantekeningen met `appgw.ingress.kubernetes.io/use-private-ip: "true"` worden genegeerd. Dit wordt aangegeven in de ingangs gebeurtenis en het AGIC pod-logboek.

* Fout zoals aangegeven in de ingangs gebeurtenis

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* Fout zoals aangegeven in AGIC-logboeken

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>Wereld wijd toewijzen
In het geval is de vereiste om alle Ingresses te beperken die worden weer gegeven via particulier IP-gebruik `appgw.usePrivateIP: true` in `helm` config.

### <a name="usage"></a>Gebruik
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

Hiermee wordt het IP-adres geconfigureerd voor een privé-IP bij het configureren van de frontend-listeners op het Application Gateway.
AGIC gaat in paniek en crashen als `usePrivateIP: true` en er geen privé-IP-adres is toegewezen.

> [!NOTE]
> Voor de SKU van Application Gateway v2 is een openbaar IP-adres vereist. Als u wilt dat Application Gateway privé is, koppelt u een [`Network Security Group`](https://docs.microsoft.com/azure/virtual-network/security-overview) aan het subnet van Application Gateway om het verkeer te beperken.
