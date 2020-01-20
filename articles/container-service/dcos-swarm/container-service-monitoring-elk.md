---
title: KEUR Een Azure DC/OS-cluster-ELK stack bewaken
description: Bewaak een DC/OS-cluster in Azure Container Service cluster met ELK (Elasticsearch, Logstash en Kibana).
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3d34ebe22344be8acc6ec3cc974071639293e2b3
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277759"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-elk"></a>KEUR Een Azure Container Service cluster bewaken met ELK

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

In dit artikel laten we zien hoe u de ELK-stack (Elasticsearch, Logstash, Kibana) implementeert op een DC/OS-cluster in Azure Container Service. 

## <a name="prerequisites"></a>Vereisten
[Implementeer](container-service-deployment.md) en [koppel](../container-service-connect.md) een DC/OS-cluster geconfigureerd door Azure container service. Bekijk [hier](container-service-mesos-marathon-ui.md)de DC/OS-dash board-en Marathon-Services. Installeer ook de [Marathon-Load Balancer](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, Logstash, Kibana)
ELK stack is een combi natie van Elasticsearch, Logstash en Kibana die een end-to-end stack biedt die kan worden gebruikt voor het bewaken en analyseren van Logboeken in uw cluster.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>De ELK-stack op een DC/OS-cluster configureren
Toegang tot uw DC/OS-gebruikers interface via [http://localhost:80/](http://localhost:80/) eenmaal in de DC/OS-gebruikers interface naar een **universum**navigeren. Zoek en installeer Elasticsearch, Logstash en Kibana van het DC/OS-universum en in die specifieke volg orde. Meer informatie over de configuratie vindt u in de koppeling **Geavanceerd installeren** .

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

Zodra de ELK-containers zijn geïnstalleerd en actief zijn, moet u ervoor zorgen dat Kibana toegankelijk zijn via Marathon-LB. Navigeer naar **Services** > **kibana**en klik op **bewerken** , zoals hieronder wordt weer gegeven.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Schakel over naar de **JSON-modus** en schuif omlaag naar de sectie Labels.
U moet hier een `"HAPROXY_GROUP": "external"` vermelding toevoegen, zoals hieronder wordt weer gegeven.
Zodra u op **wijzigingen implementeren**hebt geklikt, wordt de container opnieuw opgestart.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Als u wilt controleren of Kibana is geregistreerd als een service in het HAPROXY-dash board, moet u poort 9090 op het agent cluster openen als HAPROXY wordt uitgevoerd op poort 9090.
Standaard worden poorten 80, 8080 en 443 in het DC/OS-agent cluster geopend.
Instructies voor het openen van een poort en het bieden van een open bare beoordeling vindt u [hier](container-service-enable-public-access.md).

Voor toegang tot het HAPROXY-dash board opent u de marathon-LB-beheer interface op: `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`.
Als u naar de URL gaat, ziet u het HAPROXY-dash board zoals hieronder wordt weer gegeven. u moet een service vermelding voor Kibana zien.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


Voor toegang tot het Kibana-dash board, dat is geïmplementeerd op poort 5601, moet u poort 5601 openen. Volg de instructies [hier](container-service-enable-public-access.md). Open vervolgens het Kibana-dash board op: `http://localhost:5601`.

## <a name="next-steps"></a>Volgende stappen

* Zie [logboek beheer in DC/OS met elk](https://docs.mesosphere.com/1.8/administration/logging/elk/)voor meer informatie over het door sturen en instellen van logboeken van toepassingen.

* Zie [Logboeken filteren met elk](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/)voor informatie over het filteren van Logboeken. 

 

