---
title: KEUR Azure Swarm-cluster beheren met docker-API
description: Containers implementeren in een docker Swarm-cluster in Azure Container Service
services: container-service
author: rgardler
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 09/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: c76701ce74aafcccdbb2f1a2454f9528b52fc096
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79136224"
---
# <a name="deprecated-container-management-with-docker-swarm"></a>KEUR Container beheer met docker Swarm

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Docker Swarm biedt een omgeving voor het implementeren van beperkte workloads in een gegroepeerde set Docker-hosts. Docker Swarm maakt gebruik van de systeemeigen Docker API. De werkstroom voor het beheer van containers in een Docker Swarm is bijna identiek aan de werkstroom op een enkele containerhost. Dit document bevat enkele eenvoudige voorbeelden van de implementatie van beperkte workloads in een Azure Container Service-exemplaar van Docker Swarm. Zie [Docker Swarm op Docker.com](https://docs.docker.com/swarm/) voor uitgebreidere documentatie bij Docker Swarm.

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Vereisten voor de oefeningen in dit document:

[Een Swarm-cluster in Azure Container Service maken](container-service-deployment.md)

[Verbinding maken met het Swarm-cluster in Azure Container Service](../container-service-connect.md)

## <a name="deploy-a-new-container"></a>Een nieuwe container implementeren
Als u in de Docker Swarm een nieuwe container wilt maken, gebruikt u de opdracht `docker run` (waarbij u ervoor zorgt dat u een SSH-tunnel naar de masters hebt geopend overeenkomstig de bovenstaande vereisten). In dit voorbeeld wordt een container van gemaakt op basis van de `yeasy/simple-web`-installatiekopie:

```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

Nadat de container is gemaakt, gebruikt u `docker ps` om informatie over de container te retourneren. U ziet hier dat de Swarm-agent wordt vermeld die als host fungeert voor de container:

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

U hebt nu toegang tot de toepassing die wordt uitgevoerd in deze container via de openbare DNS-naam van de load balancer van de Swarm-agent. U kunt deze informatie vinden in Azure Portal:  

![Echte bezoekresultaten](./media/container-service-docker-swarm/real-visit.jpg)  

De Load Balancer heeft standaard de poorten 80, 8080 en 443 open. Als u via een andere poort verbinding wilt maken, moet u die poort openen in de Azure Load Balancer voor de Agent-pool.

## <a name="deploy-multiple-containers"></a>Meerdere containers implementeren
Als meerdere containers worden gestart, door 'docker run' meermaals uit te voeren, kunt u de opdracht `docker ps` gebruiken om te zien op welke hosts de containers worden uitgevoerd. In het voorbeeld hieronder zijn drie containers gelijkmatig verdeeld over de drie Swarm-agents:  

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## <a name="deploy-containers-by-using-docker-compose"></a>Containers implementeren met behulp van Docker Compose
U kunt Docker Compose gebruiken om de implementatie en configuratie van meerdere containers te automatiseren. Hiervoor moet een SSH-tunnel (Secure Shell-tunnel) zijn gemaakt en moet de variabele DOCKER_HOST zijn ingesteld (zie de bovenstaande vereisten).

Maak een docker-compose.yml-bestand op het lokale systeem. Gebruik hiervoor het volgende voor beeld:

```dockerfile
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

Voer `docker-compose up -d` uit om de containerimplementaties te starten:

```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

Tot slot wordt de lijst met actieve containers geretourneerd. Deze lijst bevat de containers die zijn geïmplementeerd met behulp van Docker Compose:

```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

Uiteraard kunt u `docker-compose ps` gebruiken om alleen de containers te onderzoeken die in uw bestand `compose.yml` zijn gedefinieerd.

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over Docker Swarm](https://docs.docker.com/swarm/)

