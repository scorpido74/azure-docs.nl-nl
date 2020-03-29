---
title: Een Azure Application Gateway maken - Azure-klassieke CLI
description: Meer informatie over het maken van een toepassingsgateway met de klassieke AZURE-CLI in Resource Manager
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: victorh
ms.openlocfilehash: 7107f45253c4f13b3378489726bf5034e104fa30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "62095979"
---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Een toepassingsgateway maken met de Azure CLI

Azure Application Gateway is een load balancer in laag 7. De gateway biedt opties voor failovers en het routeren van HTTP-aanvragen tussen servers (on-premises en in de cloud). Application gateway heeft de volgende functies voor het leveren van toepassingen: HTTP load balancing, cookie-based session affinity, en Secure Sockets Layer (SSL) offload, custom health probes en ondersteuning voor multi-site.

## <a name="prerequisite-install-the-azure-cli"></a>Voorwaarde: De Azure CLI installeren

Als u de stappen in dit artikel wilt uitvoeren, moet u [de Azure CLI installeren](../xplat-cli-install.md) en moet u zich aanmelden bij [Azure](/cli/azure/authenticate-azure-cli). 

> [!NOTE]
> Als u geen Azure-account hebt, hebt u er een nodig. U kunt zich [hier aanmelden voor een gratis proefversie](../active-directory/fundamentals/sign-up-organization.md).

## <a name="scenario"></a>Scenario

In dit scenario leert u hoe u een toepassingsgateway maakt met behulp van de Azure-portal.

Dit scenario zal:

* Maak een middelgrote toepassingsgateway met twee instanties.
* Maak een virtueel netwerk met de naam ContosoVNET met een gereserveerd CIDR-blok van 10.0.0.0/16.
* Maak een subnet genaamd subnet01 dat 10.0.0.0/28 als CIDR-blok gebruikt.

> [!NOTE]
> Aanvullende configuratie van de toepassingsgateway, inclusief aangepaste statussondes, backendpooladressen en aanvullende regels worden geconfigureerd nadat de toepassingsgateway is geconfigureerd en niet tijdens de eerste implementatie.

## <a name="before-you-begin"></a>Voordat u begint

Azure Application Gateway vereist een eigen subnet. Wanneer u een virtueel netwerk maakt, moet u ervoor zorgen dat u voldoende adresruimte overlaat om meerdere subnetten te hebben. Zodra u een toepassingsgateway implementeert naar een subnet, kunnen alleen extra toepassingsgateways aan het subnet worden toegevoegd.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Open de **Opdrachtprompt van Microsoft Azure**en meld u aan.

```azurecli-interactive
az login
```

Zodra u het voorgaande voorbeeld typt, wordt een code verstrekt. Navigeer https://aka.ms/devicelogin naar in een browser om het aanmeldingsproces voort te zetten.

![cmd met apparaataanmelding][1]

Voer in de browser de code in die u hebt ontvangen. U wordt doorgestuurd naar een aanmeldingspagina.

![browser om code in te voeren][2]

Zodra de code is ingevoerd, bent u aangemeld, sluit u de browser om verder te gaan met het scenario.

![met succes aangemeld][3]

## <a name="switch-to-resource-manager-mode"></a>Overschakelen naar resourcebeheermodus

```azurecli-interactive
azure config mode arm
```

## <a name="create-the-resource-group"></a>De resourcegroep maken

Voordat u de toepassingsgateway maakt, wordt een resourcegroep gemaakt om de toepassingsgateway te bevatten. Hieronder ziet u de opdracht.

```azurecli-interactive
azure group create \
--name ContosoRG \
--location eastus
```

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Zodra de brongroep is gemaakt, wordt een virtueel netwerk gemaakt voor de toepassingsgateway.  In het volgende voorbeeld was de adresruimte 10.0.0.0/16 zoals gedefinieerd in de voorgaande scenarionotities.

```azurecli-interactive
azure network vnet create \
--name ContosoVNET \
--address-prefixes 10.0.0.0/16 \
--resource-group ContosoRG \
--location eastus
```

## <a name="create-a-subnet"></a>Een subnet maken

Nadat het virtuele netwerk is gemaakt, wordt een subnet toegevoegd voor de toepassingsgateway.  Als u van plan bent de toepassingsgateway te gebruiken met een web-app die wordt gehost in hetzelfde virtuele netwerk als de toepassingsgateway, moet u voldoende ruimte laten voor een ander subnet.

```azurecli-interactive
azure network vnet subnet create \
--resource-group ContosoRG \
--name subnet01 \
--vnet-name ContosoVNET \
--address-prefix 10.0.0.0/28 
```

## <a name="create-the-application-gateway"></a>De toepassingsgateway maken

Zodra het virtuele netwerk en subnet zijn gemaakt, zijn de vereisten voor de toepassingsgateway voltooid. Daarnaast zijn een eerder geëxporteerd .pfx-certificaat en het wachtwoord voor het certificaat vereist voor de volgende stap: De IP-adressen die voor de backend worden gebruikt, zijn de IP-adressen voor uw backendserver. Deze waarden kunnen privé-IP's zijn in het virtuele netwerk, openbare ips of volledig gekwalificeerde domeinnamen voor uw backendservers.

```azurecli-interactive
azure network application-gateway create \
--name AdatumAppGateway \
--location eastus \
--resource-group ContosoRG \
--vnet-name ContosoVNET \
--subnet-name subnet01 \
--servers 134.170.185.46,134.170.188.221,134.170.185.50 \
--capacity 2 \
--sku-tier Standard \
--routing-rule-type Basic \
--frontend-port 80 \
--http-settings-cookie-based-affinity Enabled \
--http-settings-port 80 \
--http-settings-protocol http \
--frontend-port http \
--sku-name Standard_Medium
```

> [!NOTE]
> Voer voor een lijst met parameters die tijdens het maken kunnen worden verstrekt de volgende opdracht uit: **azure network application-gateway create --help**.

In dit voorbeeld wordt een basistoepassingsgateway gemaakt met standaardinstellingen voor de listener, backendpool, backend http-instellingen en regels. U deze instellingen aanpassen aan uw implementatie zodra de inrichting is geslaagd.
Als u uw webtoepassing al hebt gedefinieerd met de backendpool in de voorgaande stappen, begint de taakverdeling eenmaal te worden gemaakt.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het maken van aangepaste statussondes door [een aangepaste statussonde te](application-gateway-create-probe-portal.md) maken

Meer informatie over het configureren van SSL Offloading en het uitschakelen van de kostbare SSL-decryptie van uw webservers door [SSL-offload configureren](application-gateway-ssl-arm.md) te bezoeken

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli-nodejs/scenario.png
[1]: ./media/application-gateway-create-gateway-cli-nodejs/figure1.png
[2]: ./media/application-gateway-create-gateway-cli-nodejs/figure2.png
[3]: ./media/application-gateway-create-gateway-cli-nodejs/figure3.png
