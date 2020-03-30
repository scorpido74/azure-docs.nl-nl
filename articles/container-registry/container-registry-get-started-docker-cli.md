---
title: Afbeelding & docker trekken & trekken
description: Docker-installatiekopieën pushen naar en ophalen van een privécontainerregister in Azure met de Docker-CLI
ms.topic: article
ms.date: 01/23/2019
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 6751a04c3c1bfe826334161704c20c1ba2e5a6d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456351"
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Uw eerste installatiekopie naar een Docker-containerregister pushen met de Docker-CLI

Een Azure-containerregister slaat persoonlijke [Docker](https://hub.docker.com)-containerinstallatiekopieën op en beheert ze, vergelijkbaar met de manier waarop [Docker Hub](https://hub.docker.com/) openbare Docker-installatiekopieën opslaat. U de [Docker command-line interface](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) gebruiken voor [inloggen,](https://docs.docker.com/engine/reference/commandline/login/) [pushen,](https://docs.docker.com/engine/reference/commandline/push/) [trekken](https://docs.docker.com/engine/reference/commandline/pull/)en andere bewerkingen in uw containerregister.

In de volgende stappen downloadt u een officiële [Nginx-afbeelding](https://store.docker.com/images/nginx) uit het openbare Docker Hub-register, tagt u deze voor uw privé Azure-containerregister, duwt u deze naar uw register en haalt u deze vervolgens uit het register.

## <a name="prerequisites"></a>Vereisten

* **Azure-containerregister**: maak een containerregister in uw Azure-abonnement. Gebruik bijvoorbeeld de [Azure-portal](container-registry-get-started-portal.md) of de [Azure CLI](container-registry-get-started-azure-cli.md).
* **Docker CLI** - U moet Docker ook lokaal hebben geïnstalleerd. Docker biedt pakketten die eenvoudig Docker configureren op elk [Mac][docker-mac]-, [Windows][docker-windows]- of [Linux][docker-linux]-systeem.

## <a name="log-in-to-a-registry"></a>Aanmelden bij een register

Er zijn [verschillende manieren om te verifiëren](container-registry-authentication.md) aan uw prive-container register. De aanbevolen methode bij het werken in een opdrachtregel is met de Azure CLI-opdracht [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login). Bijvoorbeeld om in te loggen op een register met de naam *myregistry:*

```azurecli
az acr login --name myregistry
```

U ook inloggen met [docker login](https://docs.docker.com/engine/reference/commandline/login/). U hebt bijvoorbeeld [een serviceprincipal toegewezen aan](container-registry-authentication.md#service-principal) uw register voor een automatiseringsscenario. Wanneer u de volgende opdracht uitvoert, geeft u de serviceprincipal appID (gebruikersnaam) en het wachtwoord op wanneer u daarom wordt gevraagd. Zie de naslagverwijzing voor de [inlogopdracht docker](https://docs.docker.com/engine/reference/commandline/login/) voor aanbevolen procedures voor het beheren van inloggegevens:

```
docker login myregistry.azurecr.io
```

Beide opdrachten `Login Succeeded` keren terug zodra deze zijn voltooid.

> [!TIP]
> Geef altijd de volledig gekwalificeerde registernaam op `docker login` (alle kleine letters) wanneer u afbeeldingen gebruikt en wanneer u afbeeldingen tagt om naar uw register te duwen. In de voorbeelden in dit artikel wordt de volledig gekwalificeerde naam *myregistry.azurecr.io*.

## <a name="pull-the-official-nginx-image"></a>Trek de officiële Nginx-afbeelding

Trek eerst de openbare Nginx-afbeelding naar uw lokale computer.

```
docker pull nginx
```

## <a name="run-the-container-locally"></a>De container lokaal uitvoeren

Uitvoeren volgende [docker run](https://docs.docker.com/engine/reference/run/) opdracht om een lokale instantie van`-it`de Nginx container interactief te starten ( ) op poort 8080. Het `--rm` argument geeft aan dat de container moet worden verwijderd wanneer u deze stopt.

```
docker run -it --rm -p 8080:80 nginx
```

Blader `http://localhost:8080` naar de standaardwebpagina die door Nginx wordt weergegeven in de lopende container. U ziet een pagina die vergelijkbaar is met de volgende pagina:

![Nginx op lokale computer](./media/container-registry-get-started-docker-cli/nginx.png)

Omdat u de container `-it`interactief hebt gestart met, u de uitvoer van de Nginx-server op de opdrachtregel zien nadat u ernaar in uw browser bent genavigeerd.

Druk op `Control` + `C`om de container te stoppen en te verwijderen.

## <a name="create-an-alias-of-the-image"></a>Een alias van de afbeelding maken

Gebruik [dockertag](https://docs.docker.com/engine/reference/commandline/tag/) om een alias van de afbeelding te maken met het volledig gekwalificeerde pad naar uw register. In dit voorbeeld wordt de naamruimte `samples` gespecificeerd om overbodige items in de hoofdmap van het register te voorkomen.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Zie het gedeelte [Naamruimten repository](container-registry-best-practices.md#repository-namespaces) van [Best practices voor Azure Container Registry voor](container-registry-best-practices.md)meer informatie over het taggen met naamruimten.

## <a name="push-the-image-to-your-registry"></a>De installatiekopie naar uw register pushen

Nu u de afbeelding hebt getagd met het volledig gekwalificeerde pad naar uw privéregister, u deze naar het register pushen met [docker push:](https://docs.docker.com/engine/reference/commandline/push/)

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>De installatiekopie vanuit uw register ophalen

Gebruik de opdracht [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) om de afbeelding uit uw register te halen:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>De Nginx-container starten

Gebruik de opdracht [Docker run](https://docs.docker.com/engine/reference/run/) om de afbeelding uit uw register uit te voeren:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Blader `http://localhost:8080` naar de lopende container om de lopende container te bekijken.

Druk op `Control` + `C`om de container te stoppen en te verwijderen.

## <a name="remove-the-image-optional"></a>De afbeelding verwijderen (optioneel)

Als u de Nginx-afbeelding niet meer nodig hebt, u deze lokaal verwijderen met de opdracht [docker rmi.](https://docs.docker.com/engine/reference/commandline/rmi/)

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Als u afbeeldingen uit uw Azure-containerregister wilt verwijderen, u de opdracht Azure CLI [az acr-repository verwijderen.](/cli/azure/acr/repository#az-acr-repository-delete) Met de volgende opdracht wordt bijvoorbeeld het `samples/nginx:latest` manifest verwijderd waarnaar wordt verwezen door de tag, eventuele unieke laaggegevens en alle andere tags die naar het manifest verwijzen.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>Volgende stappen

Nu u de basis weet, bent u klaar om te beginnen met het gebruik van uw register! Implementeer bijvoorbeeld containerafbeeldingen vanuit uw register naar:

* [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

Installeer eventueel de [Docker-extensie voor Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) en de [Azure-accountextensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) om te werken met uw Azure-containerregisters. Afbeeldingen naar een Azure-containerregister trekken en pushen of ACR-taken uitvoeren, allemaal binnen Visual Studio Code.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
