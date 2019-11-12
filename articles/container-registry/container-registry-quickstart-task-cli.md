---
title: 'Quick Start: een container installatie kopie bouwen & in Azure Container Registry'
description: Voer snel taken uit met Azure Container Registry om op aanvraag een container installatie kopie te maken en uit te voeren in de Cloud.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: quickstart
ms.date: 04/02/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: b97249aa61916975fa641d4620179be33e1d5276
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931542"
---
# <a name="quickstart-build-and-run-a-container-image-using-azure-container-registry-tasks"></a>Snelstartgids: een container installatie kopie bouwen en uitvoeren met Azure Container Registry taken

In deze Quick Start gebruikt u Azure Container Registry opdrachten voor taken om snel een docker-container installatie kopie in azure te bouwen, te pushen en uit te voeren, waarin wordt weer gegeven hoe u de ontwikkelings cyclus voor ' Inner-loop ' kunt overzetten naar de Cloud. [ACR-taken][container-registry-tasks-overview] is een reeks functies in azure container Registry die u helpen bij het beheren en wijzigen van container installatie kopieën in de levens cyclus van de container. 

Nadat u deze Snelstartgids hebt bekeken, kunt u meer geavanceerde functies van ACR-taken verkennen. Met ACR-taken kunnen installatie kopieën worden geautomatiseerd op basis van code doorvoer of basis installatie kopie-updates of meerdere containers parallel, onder andere scenario's, testen. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account][azure-account] aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

U kunt de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om deze Quick Start uit te voeren. Als u het lokaal wilt gebruiken, wordt versie 2.0.58 of hoger aanbevolen. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Als u nog geen container register hebt, maakt u eerst een resource groep met de opdracht [AZ Group Create][az-group-create] . Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Een containerregister maken

Maak een container register met de opdracht [AZ ACR Create][az-acr-create] . De registernaam moet uniek zijn binnen Azure en mag 5 tot 50 alfanumerieke tekens bevatten. In het volgende voor beeld wordt *myContainerRegistry008* gebruikt. Werk deze waarde bij naar een unieke waarde.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry008 --sku Basic
```

In dit voor beeld wordt een *basis* register gemaakt, een kosten geoptimaliseerde optie voor ontwikkel aars die over Azure container Registry leren. Zie [sku's voor container registers][container-registry-skus]voor meer informatie over de beschik bare service lagen.

## <a name="build-an-image-from-a-dockerfile"></a>Een installatie kopie bouwen op basis van een Dockerfile

Gebruik nu Azure Container Registry om een installatie kopie te maken. Maak eerst een werkmap en maak vervolgens een Dockerfile met de naam *Dockerfile* met de volgende inhoud. Dit is een eenvoudig voor beeld van het bouwen van een Linux-container installatie kopie, maar u kunt uw eigen standaard Dockerfile maken en installatie kopieën bouwen voor andere platforms.

```bash
echo FROM hello-world > Dockerfile
```

Voer de opdracht [AZ ACR build][az-acr-build] uit om de installatie kopie te bouwen. Wanneer de installatie kopie is gemaakt, wordt deze naar het REGI ster gepusht. In het volgende voor beeld wordt de `sample/hello-world:v1`-installatie kopie gepusht. Met de `.` aan het einde van de opdracht wordt de locatie van de Dockerfile ingesteld, in dit geval de huidige map.

```azurecli-interactive
az acr build --image sample/hello-world:v1 --registry myContainerRegistry008 --file Dockerfile . 
```

Uitvoer van een geslaagde build en push lijkt op het volgende:

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/build_archive_b0bc1e5d361b44f0833xxxx41b78c24e.tar.gz'...
Sending context (1.856 KiB) to registry: mycontainerregistry008...
Queued a build with ID: ca8
Waiting for agent...
2019/03/18 21:56:57 Using acb_vol_4c7ffa31-c862-4be3-xxxx-ab8e615c55c4 as the home volume
2019/03/18 21:56:57 Setting up Docker configuration...
2019/03/18 21:56:58 Successfully set up Docker configuration
2019/03/18 21:56:58 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Executing step ID: build. Working directory: '', Network: ''
2019/03/18 21:56:59 Obtaining source code and scanning for dependencies...
2019/03/18 21:57:00 Successfully obtained source code and scanned for dependencies
2019/03/18 21:57:00 Launching container with name: build
Sending build context to Docker daemon  13.82kB
Step 1/1 : FROM hello-world
latest: Pulling from library/hello-world
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586fxxxx21577a99efb77324b0fe535
Successfully built fce289e99eb9
Successfully tagged mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:01 Successfully executed container: build
2019/03/18 21:57:01 Executing step ID: push. Working directory: '', Network: ''
2019/03/18 21:57:01 Pushing image: mycontainerregistry008.azurecr.io/sample/hello-world:v1, attempt 1
The push refers to repository [mycontainerregistry008.azurecr.io/sample/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Layer already exists
v1: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/03/18 21:57:03 Successfully pushed image: mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:03 Step ID: build marked as successful (elapsed time in seconds: 2.543040)
2019/03/18 21:57:03 Populating digests for step ID: build...
2019/03/18 21:57:05 Successfully populated digests for step ID: build
2019/03/18 21:57:05 Step ID: push marked as successful (elapsed time in seconds: 1.473581)
2019/03/18 21:57:05 The following dependencies were found:
2019/03/18 21:57:05
- image:
    registry: mycontainerregistry008.azurecr.io
    repository: sample/hello-world
    tag: v1
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: v1
    digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
  git: {}

Run ID: ca8 was successful after 10s
```

## <a name="run-the-image"></a>De installatie kopie uitvoeren

Voer nu snel de installatie kopie uit die u hebt gemaakt en gepusht naar het REGI ster. In de ontwikkel werk stroom van de container kan dit een validatie stap zijn voordat u de installatie kopie implementeert.

Maak een bestand *quickrun. yaml* in een lokale werkmap met de volgende inhoud voor één stap. Vervang de naam van de aanmeldings server van het REGI ster door *\<acrLoginServer\>* . De naam van de aanmeldings server bevindt zich in de indeling *\<REGI ster-naam\>. azurecr.io* (alle kleine letters), bijvoorbeeld *mycontainerregistry008.azurecr.io*. In dit voor beeld wordt ervan uitgegaan dat u de `sample/hello-world:v1` installatie kopie in de vorige sectie hebt gemaakt en gepusht:

```yml
steps:
  - cmd: <acrLoginServer>/sample/hello-world:v1
```

Met de `cmd` stap in dit voor beeld wordt de container in de standaard configuratie uitgevoerd, maar `cmd` ondersteunt extra `docker run` para meters of zelfs andere `docker`-opdrachten.

Voer de container uit met de volgende opdracht:

```azurecli-interactive
az acr run --registry myContainerRegistry008 --file quickrun.yaml .
```

De uitvoer lijkt op het volgende:

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/run_archive_ebf74da7fcb04683867b129e2ccad5e1.tar.gz'...
Sending context (1.855 KiB) to registry: mycontainerre...
Queued a run with ID: cab
Waiting for an agent...
2019/03/19 19:01:53 Using acb_vol_60e9a538-b466-475f-9565-80c5b93eaa15 as the home volume
2019/03/19 19:01:53 Creating Docker network: acb_default_network, driver: 'bridge'
2019/03/19 19:01:53 Successfully set up Docker network: acb_default_network
2019/03/19 19:01:53 Setting up Docker configuration...
2019/03/19 19:01:54 Successfully set up Docker configuration
2019/03/19 19:01:54 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/03/19 19:01:55 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

2019/03/19 19:01:56 Successfully executed container: acb_step_0
2019/03/19 19:01:56 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 0.843801)

Run ID: cab was successful after 6s
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, kunt u de opdracht [AZ Group delete][az-group-delete] gebruiken om de resource groep, het container register en de container installatie kopieën die daar zijn opgeslagen, te verwijderen.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u functies van ACR-taken gebruikt om snel een docker-container installatie kopie in azure te bouwen, te pushen en uit te voeren. Ga door naar de Azure Container Registry zelf studies voor meer informatie over het gebruik van ACR-taken voor het automatiseren van installatie kopieën en updates.

> [!div class="nextstepaction"]
> [Azure Container Registry zelf studies][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[azure-account]: https://azure.microsoft.com/free/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tasks-overview]: container-registry-tasks-overview.md
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli-install]: /cli/azure/install-azure-cli
