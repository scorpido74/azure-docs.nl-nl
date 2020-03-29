---
title: (AFGESCHAFT) Een Azure DC/OS-cluster bewaken - ELK-stack
description: Monitor een DC/OS-cluster in Azure Container Service-cluster met ELK (Elasticsearch, Logstash en Kibana).
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3d34ebe22344be8acc6ec3cc974071639293e2b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277759"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-elk"></a>(AFGESCHAFT) Een Azure Container Service-cluster bewaken met ELK

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

In dit artikel laten we zien hoe u de elk -stack (Elasticsearch, Logstash, Kibana) implementeert op een DC/OS-cluster in Azure Container Service. 

## <a name="prerequisites"></a>Vereisten
[Een](container-service-deployment.md) DC/OS-cluster implementeren en [verbinden](../container-service-connect.md) dat is geconfigureerd door Azure Container Service. Bekijk [hier](container-service-mesos-marathon-ui.md)het DC/OS dashboard en marathon services. Installeer ook de [Marathon Load Balancer.](container-service-load-balancing.md)


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, Logstash, Kibana)
ELK stack is een combinatie van Elasticsearch, Logstash en Kibana die een end-to-end stack biedt die kan worden gebruikt om logboeken in uw cluster te controleren en te analyseren.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>De ELK-stack configureren op een DC/OS-cluster
Krijg toegang tot uw [http://localhost:80/](http://localhost:80/) DC/OS-gebruikersinterface via Eenmaal in de DC/OS-gebruikersinterface navigeer u naar **Universe.** Zoek en installeer Elasticsearch, Logstash en Kibana uit het DC/OS-universum en in die specifieke volgorde. U meer informatie over de configuratie vinden als u naar de koppeling **Geavanceerde installatie** gaat.

![ELANDEN1](./media/container-service-monitoring-elk/elk1.PNG) ![ELANDEN2](./media/container-service-monitoring-elk/elk2.PNG) ![ELANDEN3](./media/container-service-monitoring-elk/elk3.PNG) 

Zodra de ELK containers en zijn up and running, moet u kibana toegang tot via Marathon-LB. Navigeer naar **Services** > **kibana**en klik op **Bewerken** zoals hieronder weergegeven.

![ELANDEN4](./media/container-service-monitoring-elk/elk4.PNG)


Schakel in op **de JSON-modus** en blader omlaag naar de sectie labels.
Je moet een `"HAPROXY_GROUP": "external"` item toe te voegen hier zoals hieronder weergegeven.
Zodra u op **Wijzigingen implementeren**klikt, wordt de container opnieuw opgestart.

![ELANDEN5](./media/container-service-monitoring-elk/elk5.PNG)


Als u wilt controleren of Kibana is geregistreerd als een service in het HAPROXY-dashboard, moet u poort 9090 openen op het agentcluster, omdat HAPROXY op poort 9090 draait.
Standaard openen we poorten 80, 8080 en 443 in het dc/os-agentcluster.
Instructies om een poort te openen en het publiek te beoordelen zijn [hier](container-service-enable-public-access.md)verstrekt.

Als u toegang wilt krijgen tot het HAPROXY-dashboard, opent u de marathon-LB-beheerinterface op: `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`.
Zodra u naar de URL navigeert, ziet u het HAPROXY-dashboard zoals hieronder weergegeven en ziet u een servicevermelding voor Kibana.

![ELANDEN6](./media/container-service-monitoring-elk/elk6.PNG)


Om toegang te krijgen tot het Kibana-dashboard, dat is geïmplementeerd op poort 5601, moet u poort 5601 openen. Volg [hier](container-service-enable-public-access.md)de instructies . Open vervolgens het Kibana `http://localhost:5601`dashboard op: .

## <a name="next-steps"></a>Volgende stappen

* Zie [Logbeheer in DC/OS met ELK](https://docs.mesosphere.com/1.8/administration/logging/elk/)voor het doorsturen en instellen van systeem- en toepassingslogboeken.

* Zie [Logboeken filteren met ELK](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/)als u logboeken wilt filteren. 

 

