---
title: Quick Start-een installatie kopie van &-container maken
description: Voer snel taken uit met Azure Container Registry om op aanvraag een docker-container installatie kopie te maken en uit te voeren in de Cloud.
ms.topic: quickstart
ms.date: 01/31/2020
ms.openlocfilehash: f08f10dd170acaa8594ad5a47f5ef58e27288b10
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76986271"
---
# <a name="quickstart-build-and-run-a-container-image-using-azure-container-registry-tasks"></a>Snelstartgids: een container installatie kopie bouwen en uitvoeren met Azure Container Registry taken

In deze Quick Start gebruikt u Azure Container Registry opdrachten voor taken om snel een docker-container installatie kopie in azure te bouwen, te pushen en uit te voeren, waarin wordt weer gegeven hoe u de ontwikkelings cyclus voor ' Inner-loop ' kunt overzetten naar de Cloud. [ACR-taken][container-registry-tasks-overview] is een reeks functies in azure container Registry die u helpen bij het beheren en wijzigen van container installatie kopieën in de levens cyclus van de container. 

Nadat u deze Snelstartgids hebt bekeken, kunt u meer geavanceerde functies van ACR-taken verkennen. Met ACR-taken kunnen installatie kopieën worden geautomatiseerd op basis van code doorvoer of basis installatie kopie-updates of meerdere containers parallel, onder andere scenario's, testen. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account][azure-account] aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

U kunt de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om deze Quick Start uit te voeren. Als u het lokaal wilt gebruiken, wordt versie 2.0.58 of hoger aanbevolen. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install].

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

In dit voor beeld wordt een *basis* register gemaakt, een kosten geoptimaliseerde optie voor ontwikkel aars die over Azure container Registry leren. Zie [Azure Container Registry-SKU's][container-registry-skus] voor meer informatie over de beschikbare servicelagen.

## <a name="build-an-image-from-a-dockerfile"></a>Een installatie kopie bouwen op basis van een Dockerfile

Gebruik nu Azure Container Registry om een installatie kopie te maken. Maak eerst een werkmap en maak vervolgens een Dockerfile met de naam *Dockerfile* met de volgende inhoud. Dit is een eenvoudig voor beeld van het bouwen van een Linux-container installatie kopie, maar u kunt uw eigen standaard Dockerfile maken en installatie kopieën bouwen voor andere platforms. De opdracht voorbeelden in dit artikel zijn ingedeeld voor de bash-shell.

```bash
echo FROM hello-world > Dockerfile
```

Voer de opdracht [AZ ACR build][az-acr-build] uit om de installatie kopie te bouwen. Wanneer de installatie kopie is gemaakt, wordt deze naar het REGI ster gepusht. In het volgende voor beeld wordt `sample/hello-world:v1` de installatie kopie gepusht. `.` Aan het einde van de opdracht wordt de locatie van de Dockerfile ingesteld, in dit geval de huidige map.

```azurecli-interactive
az acr build --image sample/hello-world:v1 \
  --registry myContainerRegistry008 \
  --file Dockerfile . 
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

Voer nu snel de installatie kopie uit die u hebt gemaakt en gepusht naar het REGI ster. Hier gebruikt u [AZ ACR run][az-acr-run] om de container opdracht uit te voeren. In de ontwikkel werk stroom van de container kan dit een validatie stap zijn voordat u de installatie kopie implementeert, of u kunt de opdracht in een [yaml-bestand met meerdere stappen][container-registry-tasks-multi-step]toevoegen. 

Het volgende voor beeld `$Registry` wordt gebruikt om het REGI ster op te geven waarin u de opdracht uitvoert:

```azurecli-interactive
az acr run --registry myContainerRegistry008 \
  --cmd '$Registry/sample/hello-world:v1' /dev/null
```

Met `cmd` de para meter in dit voor beeld wordt de container in de standaard `cmd` configuratie uitgevoerd `docker run` , maar worden aanvullende `docker` para meters of zelfs andere opdrachten ondersteund.

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

U kunt de opdracht [az group delete][az-group-delete] gebruiken om de resourcegroep, het containerregister en de daar opgeslagen containerinstallatiekopieën te verwijderen wanneer u ze niet meer nodig hebt.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u functies van ACR-taken gebruikt om snel een docker-container installatie kopie in azure te bouwen, te pushen en uit te voeren, zonder een lokale docker-installatie. Ga verder met de zelf studies over Azure Container Registry taken voor meer informatie over het gebruik van ACR-taken voor het automatiseren van installatie kopieën en updates.

> [!div class="nextstepaction"]
> [Zelf studies over Azure Container Registry taken][container-registry-tutorial-quick-task]

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
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tasks-overview]: container-registry-tasks-overview.md
[container-registry-tasks-multi-step]: container-registry-tasks-multi-step.md
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli-install]: /cli/azure/install-azure-cli
