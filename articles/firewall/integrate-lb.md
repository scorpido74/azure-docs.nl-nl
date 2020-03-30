---
title: Azure Firewall integreren met Azure Standard Load Balancer
description: U een Azure Firewall integreren in een virtueel netwerk met een Azure Standard Load Balancer (openbaar of intern).
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/28/2020
ms.author: victorh
ms.openlocfilehash: ab9a500d9535b55702b8baff15f8cc47e6ac2c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196697"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Azure Firewall integreren met Azure Standard Load Balancer

U een Azure Firewall integreren in een virtueel netwerk met een Azure Standard Load Balancer (openbaar of intern). 

Het voorkeursontwerp is om een interne load balancer te integreren met uw Azure-firewall, omdat dit een veel eenvoudiger ontwerp is. U een openbare load balancer gebruiken als u er al een hebt geïmplementeerd en u deze op zijn plaats wilt houden. U moet zich echter bewust zijn van een asymmetrisch routeringsprobleem dat functionaliteit kan verbreken met het scenario van de openbare load balancer.

Zie Wat is Azure Load Balancer voor meer informatie over Azure Load [Balancer?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Public load balancer

Met een openbare load balancer wordt de load balancer geïmplementeerd met een openbaar IP-adres aan de voorkant.

### <a name="asymmetric-routing"></a>Asymmetrisch routeren

Asymmetrische routering is waar een pakket een pad naar de bestemming neemt en een ander pad neemt wanneer het terugkeert naar de bron. Dit probleem treedt op wanneer een subnet een standaardroute heeft die naar het privé-IP-adres van de firewall gaat en u een openbare load balancer gebruikt. In dit geval wordt het inkomende load balancer-verkeer ontvangen via het openbare IP-adres, maar het retourpad gaat via het privé IP-adres van de firewall. Aangezien de firewall stateful is, laat het het terugkerende pakket vallen omdat de firewall zich niet bewust is van een dergelijke gevestigde sessie.

### <a name="fix-the-routing-issue"></a>Het routeringsprobleem oplossen

Wanneer u een Azure Firewall implementeert in een subnet, moet u een standaardroute maken voor het subnet dat pakketten leidt via het privé-IP-adres van de firewall op het AzureFirewallSubnet. Zie [Zelfstudie: Azure Firewall implementeren en configureren met behulp van de Azure-portal](tutorial-firewall-deploy-portal.md#create-a-default-route)voor meer informatie.

Wanneer u de firewall introduceert in uw load balancer-scenario, wilt u dat uw internetverkeer binnenkomt via het openbare IP-adres van uw firewall. Van daaruit past de firewall regels en NATs de pakketten op het openbare IP-adres van uw load balancer. Dit is waar het probleem zich voordoet. Pakketten komen aan op het openbare IP-adres van de firewall, maar keren terug naar de firewall via het privé IP-adres (met behulp van de standaardroute).
Maak een extra hostroute voor het openbare IP-adres van de firewall om dit probleem te voorkomen. Pakketten die naar het openbare IP-adres van de firewall gaan, worden via internet doorgestuurd. Dit voorkomt dat u de standaardroute naar het privé-IP-adres van de firewall neemt.

![Asymmetrisch routeren](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>Voorbeeld van routetabel

De volgende routes zijn bijvoorbeeld voor een firewall op openbaar IP-adres 20.185.97.136 en privé-IP-adres 10.0.1.4.

> [!div class="mx-imgBorder"]
> ![Routetabel](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>Voorbeeld van NAT-regel

In het volgende voorbeeld vertaalt een NAT-regel RDP-verkeer naar de firewall op 20.185.97.136 naar de load balancer op 20.42.98.220:

> [!div class="mx-imgBorder"]
> ![NAT-regel](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>Statuscontroles

Houd er rekening mee dat u een webservice moet hebben die wordt uitgevoerd op de hosts in de groep load balancer als u TCP-statussondes gebruikt om 80 of HTTP/HTTPS-sondes te poort.

## <a name="internal-load-balancer"></a>Interne load balancer

Met een interne load balancer wordt de load balancer geïmplementeerd met een eigen ip-adres aan de voorkant.

Er is geen asymmetrische routing probleem met dit scenario. De binnenkomende pakketten komen aan op het openbare IP-adres van de firewall, worden vertaald naar het privé IP-adres van de load balancer en keren vervolgens terug naar het privé IP-adres van de firewall met behulp van hetzelfde retourpad.

U dit scenario dus implementeren dat vergelijkbaar is met het scenario voor de openbare load balancer, maar zonder de noodzaak van de openbare IP-adreshostroute van de firewall.

## <a name="additional-security"></a>Extra beveiliging

Om de beveiliging van uw load-balanced scenario verder te verbeteren, u netwerkbeveiligingsgroepen (NSG's) gebruiken.

U bijvoorbeeld een NSG maken op het backend-subnet waar de load-balanced virtuele machines zich bevinden. Inkomend verkeer toestaan dat afkomstig is van het IP-adres/de firewall.

![Netwerkbeveiligingsgroep](media/integrate-lb/nsg-01.png)

Zie [Beveiligingsgroepen voor](../virtual-network/security-overview.md)meer informatie over NSG's .

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [implementeren en configureren van een Azure Firewall](tutorial-firewall-deploy-portal.md).