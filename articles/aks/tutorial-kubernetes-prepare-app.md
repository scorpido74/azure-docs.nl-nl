---
title: 'Zelfstudie over Kubernetes in Azure: Toepassing voorbereiden'
description: In deze zelfstudie over Azure Kubernetes Service (AKS) leert u hoe u een app met meerdere containers voorbereidt en bouwt met Docker Compose, waarna u de app kunt implementeren naar AKS.
services: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.custom: mvc
ms.openlocfilehash: feab8495536b3306fd96793323d51644570b401b
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "77593157"
---
# <a name="tutorial-prepare-an-application-for-azure-kubernetes-service-aks"></a>Zelfstudie: Toepassing voorbereiden voor AKS (Azure Kubernetes Service)

In deze zelfstudie, deel een van zeven, wordt een toepassing met meerdere containers voorbereid voor gebruik in Kubernetes. Bestaande ontwikkelprogramma's, zoals Docker Compose, worden gebruikt voor het lokaal bouwen en testen van een toepassing. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een voorbeeld van een toepassingsbron klonen vanuit GitHub
> * Een containerinstallatiekopie maken van het voorbeeld van de toepassingsbron
> * De toepassing met meerdere containers testen in een lokale Docker-omgeving

Als u dit allemaal hebt gedaan, kunt u de volgende toepassing uitvoeren in uw lokale ontwikkelomgeving:

![Afbeelding van Kubernetes-cluster in Azure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

In aanvullende zelfstudies wordt de containerinstallatiekopie geüpload naar een Azure Container Registry en vervolgens geïmplementeerd naar een AKS-cluster.

## <a name="before-you-begin"></a>Voordat u begint

In deze zelfstudie wordt ervan uitgegaan dat u een basiskennis hebt van Docker-kernconcepten zoals containers, containerinstallatiekopieën en `docker`-opdrachten. Zie [Aan de slag met Docker][docker-get-started] voor een uitleg van de basisprincipes van containers.

Voor deze zelfstudie hebt u een lokale Docker-ontwikkelomgeving met Linux-containers nodig. Docker biedt pakketten voor de configuratie van Docker op een [Mac][docker-for-mac]-, [Windows][docker-for-windows]- of [Linux][docker-for-linux]-systeem.

Azure Cloud Shell bevat niet de Docker-onderdelen die nodig zijn om elke stap in deze zelfstudies te voltooien. Daarom raden wij u aan een volledige Docker-ontwikkelomgeving te gebruiken.

## <a name="get-application-code"></a>Toepassingscode ophalen

De voorbeeldtoepassing die wordt gebruikt in deze zelfstudie, is een eenvoudige stem-app. De toepassing bestaat uit een front-endwebonderdeel en een back-end-Redis-exemplaar. Het webonderdeel is verpakt in een aangepaste containerinstallatiekopie. Het Redis-exemplaar gebruikt een ongewijzigde installatiekopie van Docker Hub.

Gebruik [git][] om de voorbeeldtoepassing te klonen naar uw ontwikkelomgeving:

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Wijzig in de gekloonde map.

```console
cd azure-voting-app-redis
```

In de map bevinden zich de broncode van de toepassing, een vooraf gemaakt Docker Compose-bestand en een Kubernetes-manifestbestand. Deze bestanden worden gebruikt in de alle delen van de zelfstudie.

## <a name="create-container-images"></a>Containerinstallatiekopieën maken

[Docker Compose][docker-compose] kan worden gebruikt om het bouwen van containerinstallatiekopieën en het implementeren van toepassingen met meerdere containers te automatiseren.

Gebruik het voorbeeldbestand `docker-compose.yaml` om automatisch de containerinstallatiekopie te maken, de Redis-installatiekopie te downloaden en de toepassing te starten:

```console
docker-compose up -d
```

Wanneer dit is voltooid, gebruikt u de opdracht [docker images][docker-images] om de gemaakte installatiekopieën te bekijken. Er zijn drie installatiekopieën gedownload of gemaakt. De installatiekopie *azure-vote-front* bevat de front-endtoepassing en gebruikt de installatiekopie `nginx-flask` als basis. De `redis`-installatiekopie wordt gebruikt om een Redis-exemplaar te starten.

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Voer de opdracht [docker ps][docker-ps] uit om de actieve containers te zien:

```
$ docker ps

CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Toepassing lokaal testen

Als u wilt zien hoe de toepassing wordt uitgevoerd, typt u `http://localhost:8080` in een lokale browser. De voorbeeldtoepassing wordt dan geladen, zoals wordt weergegeven in het volgende voorbeeld:

![Afbeelding van Kubernetes-cluster in Azure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

## <a name="clean-up-resources"></a>Resources opschonen

Nu de functionaliteit van de toepassing is gevalideerd, kunnen de actieve containers worden gestopt en verwijderd. De containerinstallatiekopieën moet u niet verwijderen. In de volgende zelfstudie wordt de installatiekopie *azure-vote-front* geüpload naar een Azure Container Registry-exemplaar.

Stop en verwijder de containerexemplaren en -resources met de opdracht [docker-compose down][docker-compose-down]:

```console
docker-compose down
```

Wanneer de lokale toepassing is verwijderd, hebt u een Docker-installatiekopie met de Azure Vote-toepassing, *azure-vote-front*, voor gebruik met de volgende zelfstudie.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie is een toepassing getest en zijn containerinstallatiekopieën gemaakt voor de toepassing. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een voorbeeld van een toepassingsbron klonen vanuit GitHub
> * Een containerinstallatiekopie maken van het voorbeeld van de toepassingsbron
> * De toepassing met meerdere containers testen in een lokale Docker-omgeving

Ga verder met de volgende zelfstudie voor informatie over het opslaan van containerinstallatiekopieën in Azure Container Registry.

> [!div class="nextstepaction"]
> [Installatiekopieën naar Azure Container Registry pushen][aks-tutorial-prepare-acr]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-ps]: https://docs.docker.com/engine/reference/commandline/ps/
[docker-compose-down]: https://docs.docker.com/compose/reference/down
[git]: https://git-scm.com/downloads

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
