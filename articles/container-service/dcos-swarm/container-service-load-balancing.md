---
title: (AFGESCHAFT) Taken over containers verdelen in een DC/OS-cluster in Azure
description: Verdeel taken over meerdere containers in een DC/OS-cluster in Azure Container Service.
author: rgardler
ms.service: container-service
ms.topic: tutorial
ms.date: 06/02/2017
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: a8f863f16888e6eca2dbc72c5dd612c38edbe46e
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273378"
---
# <a name="deprecated-load-balance-containers-in-an-azure-container-service-dcos-cluster"></a>(AFGESCHAFT) Taken over containers verdelen in een DC/OS-cluster in Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

In dit artikel wordt beschreven hoe een interne load balancer in een met DC/OS beheerde Azure Container Service wordt gemaakt met Marathon-LB. Met deze configuratie kunt u uw toepassingen horizontaal schalen. U kunt hiermee ook gebruikmaken van de clusters met openbare en persoonlijke agents door de load balancers in het openbare cluster en uw toepassingscontainers in het persoonlijke cluster te plaatsen. In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Een Marathon Load Balancer configureren
> * Een toepassing implementeren met behulp van de load balancer
> * Een Azure Load Balancer configureren

U hebt een ACS DC/OS-cluster nodig om de stappen in deze zelfstudie te kunnen uitvoeren. Zo nodig kan [dit voorbeeldscript](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) er een voor u maken.

Voor deze zelfstudie is versie 2.0.4 of hoger van de Azure CLI vereist. Voer `az --version` uit om de versie te bekijken. Als u wilt upgraden, raadpleegt u [Azure CLI installeren]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="load-balancing-overview"></a>Overzicht van taakverdeling

Er zijn twee taakverdelingslagen in het Azure Container Service DC/OS-cluster: 

**De Azure Load Balancer** biedt openbare toegangspunten (voor de eindgebruikers). Een Azure LB wordt automatisch aangeboden door de Azure Container Service en is standaard geconfigureerd om poorten 80 en 443 van 8080 open te stellen.

**De Marathon Load Balancer (marathon-lb)** routeert binnenkomende aanvragen naar de containerinstanties die deze aanvragen bedienen. Als we de containers schalen met onze webservice, past de marathon-lb zich dynamisch aan. Deze taakverdeling is geen standaardvoorziening in uw Container Service, maar is gemakkelijk te installeren.

## <a name="configure-marathon-load-balancer"></a>Marathon Load Balancer configureren

Marathon Load Balancer herconfigureert zichzelf dynamisch op basis van de containers die u hebt geïmplementeerd. De Load Balancer past zich ook aan het verlies van een container of agent aan. Als dit gebeurt, wordt de container door Apache Mesos elders opnieuw gestart en wordt marathon-lb aangepast.

Ga naar [https://shell.azure.com](https://shell.azure.com) om Cloud shell in uw browser te openen.

Voer de volgende opdracht uit om de marathon-taakverdeling op het cluster van de openbare agent te installeren.

```console
dcos package install marathon-lb
```

## <a name="deploy-load-balanced-application"></a>Een toepassing met taakverdeling implementeren

Nu we het Marathon-LB-pakket hebben, kunnen we een toepassingscontainer implementeren waarvan we de taken willen verdelen. 

Haal eerst de FQDN op van de agents die openbaar beschikbaar worden gesteld.

```azurecli-interactive
az acs list --resource-group myResourceGroup --query "[0].agentPoolProfiles[0].fqdn" --output tsv
```

Maak vervolgens een bestand met de naam *hello-web.json* en kopieer de volgende inhoud naar het bestand. Het `HAPROXY_0_VHOST`-label moet worden bijgewerkt met de FQDN van de DC/OS-agents. 

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}
```

Gebruik de DC/OS CLI om de toepassing uit te voeren. Met Marathon wordt de toepassing standaard geïmplementeerd in het privecluster. Dit betekent dat de bovenstaande implementatie alleen toegankelijk is via de load balancer (dit is meestal het gewenste gedrag).

```console
dcos marathon app add hello-web.json
```

Wanneer de toepassing is geïmplementeerd, bladert u naar de FQDN van het agentcluster om de toepassing met taakverdeling weer te geven.

![Afbeelding van een toepassing met taakverdeling](./media/container-service-load-balancing/lb-app.png)

## <a name="configure-azure-load-balancer"></a>Azure Load Balancer configureren

Standaard stelt de Azure Load Balancer poorten 80, 8080 en 443 open. Als u een van deze drie poorten gebruikt (zoals we dit in het bovenstaande voorbeeld doen), dan hoeft u niets te doen. U moet de FQDN van de load balancer van uw agent kunnen bereiken en telkens wanneer u vernieuwt, bereikt u één van de drie webservers volgens round-robin. 

Als u echter een andere poort gebruikt, moet u een round-robinregel en een probe toevoegen aan de load balancer voor de gebruikte poort. Dit kan vanuit de [Azure CLI](../../azure-resource-manager/xplat-cli-azure-resource-manager.md), met de opdrachten `azure network lb rule create` en `azure network lb probe create`.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd over taakverdeling in ACS met zowel de Marathon- als de Azure-load balancer, met inbegrip van de volgende acties:

> [!div class="checklist"]
> * Een Marathon Load Balancer configureren
> * Een toepassing implementeren met behulp van de load balancer
> * Een Azure Load Balancer configureren

Ga naar de volgende zelfstudie om te leren over de integratie van Azure-opslag met DC/OS in Azure.

> [!div class="nextstepaction"]
> [De Azure-bestandsshare koppelen in DC/OS-cluster](container-service-dcos-fileshare.md)
