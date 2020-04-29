---
title: '(AFGESCHAFT) Zelfstudie Azure Container Service: App voorbereiden'
description: 'Zelfstudie Azure Container Service: app voorbereiden'
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b67e3008bc2ad681e222af6b9edc4a08875732e4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76275432"
---
# <a name="deprecated-create-container-images-to-be-used-with-azure-container-service"></a>(AFGESCHAFT) Containerinstallatiekopieën maken om te gebruiken bij Azure Container Service

> [!TIP]
> Voor de bijgewerkte versie van deze zelf studie die gebruikmaakt van de Azure Kubernetes-service, raadpleegt u [zelf studie: een toepassing voorbereiden voor Azure Kubernetes service (AKS)](../../aks/tutorial-kubernetes-prepare-app.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

In deze zelfstudie, deel een van zeven, wordt een toepassing met meerdere containers voorbereid voor gebruik in Kubernetes. Dit zijn de uitgevoerde stappen:  

> [!div class="checklist"]
> * Toepassingsbron klonen vanuit GitHub  
> * Een containerinstallatiekopie maken uit de toepassingsbron
> * De toepassing testen in een lokale Docker-omgeving

Na voltooiing is de volgende toepassing toegankelijk in uw lokale ontwikkelomgeving.

![Afbeelding van Kubernetes-cluster in Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

In de volgende zelfstudies wordt de containerinstallatiekopie geüpload naar een Azure Container Registry, en vervolgens uitgevoerd in een met Azure gehost Kubernetes-cluster.

## <a name="before-you-begin"></a>Voordat u begint

In deze zelfstudie wordt ervan uitgegaan dat u een basiskennis hebt van Docker-kernconcepten zoals containers, containerinstallatiekopieën en Docker-basisopdrachten. Zie, indien nodig, [Aan de slag met Docker]( https://docs.docker.com/get-started/) voor een uitleg van de basisprincipes van containers. 

Voor deze zelfstudie hebt u een Docker-ontwikkelomgeving nodig. Docker biedt pakketten die eenvoudig Docker configureren op elk [Mac](https://docs.docker.com/docker-for-mac/)-, [Windows](https://docs.docker.com/docker-for-windows/)- of [Linux](https://docs.docker.com/engine/installation/#supported-platforms)-systeem.

Azure Cloud Shell bevat niet de vereiste Docker-onderdelen die nodig zijn om elke stap in deze zelfstudie te voltooien. Daarom raden wij u aan een volledige Docker-ontwikkelomgeving te gebruiken.

## <a name="get-application-code"></a>Toepassingscode ophalen

De voorbeeldtoepassing die wordt gebruikt in deze zelfstudie, is een eenvoudige stem-app. De toepassing bestaat uit een front-endwebonderdeel en een back-end-Redis-exemplaar. Het webonderdeel is verpakt in een aangepaste containerinstallatiekopie. Het Redis-exemplaar gebruikt een ongewijzigde installatiekopie van Docker Hub.  

Gebruik git om een kopie van de toepassing te downloaden naar de ontwikkelomgeving.

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Wijzig de mappen zodat u vanuit de gekloonde map werkt.

```
cd azure-voting-app-redis
```

In de map bevinden zich de broncode van de toepassing, een vooraf gemaakt Docker Compose-bestand en een Kubernetes-manifestbestand. Deze bestanden worden gebruikt in de alle delen van de zelfstudie. 

## <a name="create-container-images"></a>Containerinstallatiekopieën maken

[Docker Compose](https://docs.docker.com/compose/) kan worden gebruikt om de build te automatiseren op basis van containerinstallatiekopieën en de implementatie van toepassingen met meerdere containers.

Voer het bestand `docker-compose.yml` uit om de containerinstallatiekopie te maken, download de Redis-installatiekopie en start de toepassing.

```bash
docker-compose up -d
```

Wanneer dit is voltooid, gebruikt u de opdracht [docker images](https://docs.docker.com/engine/reference/commandline/images/) om de gemaakte installatiekopieën te bekijken.

```bash
docker images
```

U ziet dat er drie installatiekopieën zijn gedownload of gemaakt. De `azure-vote-front`-installatiekopie bevat de toepassing en gebruikt de `nginx-flask`-installatiekopie als basis. De `redis`-installatiekopie wordt gebruikt om een Redis-exemplaar te starten.

```bash
REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Voer de opdracht [docker PS](https://docs.docker.com/engine/reference/commandline/ps/) uit om de actieve containers te zien.

```bash
docker ps
```

Uitvoer:

```bash
CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Toepassing lokaal testen

Blader naar `http://localhost:8080` om de toepassing te zien terwijl deze wordt uitgevoerd.

![Afbeelding van Kubernetes-cluster in Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="clean-up-resources"></a>Resources opschonen

Nu de functionaliteit van de toepassing is gevalideerd, kunnen de actieve containers worden gestopt en verwijderd. Verwijder de containerinstallatiekopieën niet. De `azure-vote-front`-installatiekopie wordt in de volgende zelfstudie geüpload naar een Azure Container Registry-exemplaar.

Voer de volgende opdracht uit om de actieve containers te stoppen.

```bash
docker-compose stop
```

Verwijder de gestopte containers en resources met de volgende opdracht.

```bash
docker-compose down
```

Na voltooiing hebt u een containerinstallatiekopie die de Azure Vote-toepassing bevat.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie is een toepassing getest en zijn containerinstallatiekopieën gemaakt voor de toepassing. De volgende stappen zijn voltooid:

> [!div class="checklist"]
> * De toepassingsbron klonen vanuit GitHub  
> * Containerinstallatiekopie maken uit de toepassingsbron
> * De toepassing testen in een lokale Docker-omgeving

Ga verder met de volgende zelfstudie voor meer informatie over het opslaan van installatiekopieën van de container in een Azure Container Registry.

> [!div class="nextstepaction"]
> [Installatiekopieën naar Azure Container Registry pushen](./container-service-tutorial-kubernetes-prepare-acr.md)
