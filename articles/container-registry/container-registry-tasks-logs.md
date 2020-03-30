---
title: Logboeken voor taaktaakbeheer weergeven - Taken
description: Logboeken die zijn gegenereerd door ACR-taken weergeven en beheren.
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: f7098f470a3f8a0cdac019f4bf8eb8fe14330337
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246966"
---
# <a name="view-and-manage-task-run-logs"></a>Logboeken voor taakbeheer weergeven en beheren

Elke taak die wordt uitgevoerd in [Azure Container Registry-taken](container-registry-tasks-overview.md) genereert logboekuitvoer die u controleren om te bepalen of de taakstappen zijn uitgevoerd. 

In dit artikel wordt uitgelegd hoe logboeken voor taakbeheer worden bekeken en beheerd.

## <a name="view-streamed-logs"></a>Gestreamde logboeken weergeven

Wanneer u een taak handmatig activeert, wordt de logboekuitvoer rechtstreeks naar de console gestreamd. Wanneer u bijvoorbeeld een taak handmatig activeert met behulp van de [opdracht AZ ACR Build,](/cli/azure/acr#az-acr-build) [AZ ACR Run](/cli/azure/acr#az-acr-run)of az [acr task run,](/cli/azure/acr/task#az-acr-task-run) ziet u loguitvoer gestreamd naar de console. 

Met de volgende [voorbeeldopdracht az acr run](/cli/azure/acr#az-acr-run) activeert u handmatig een taak die een container uitvoert die uit hetzelfde register is getrokken:

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

Gestreamd logboek:

```console
Queued a run with ID: cf4
Waiting for an agent...
2020/03/09 20:30:10 Alias support enabled for version >= 1.1.0, please see https://aka.ms/acr/tasks/task-aliases for more information.
2020/03/09 20:30:10 Creating Docker network: acb_default_network, driver: 'bridge'
2020/03/09 20:30:10 Successfully set up Docker network: acb_default_network
2020/03/09 20:30:10 Setting up Docker configuration...
2020/03/09 20:30:11 Successfully set up Docker configuration
2020/03/09 20:30:11 Logging in to registry: mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Successfully logged into mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2020/03/09 20:30:12 Launching container with name: acb_step_0
Unable to find image 'mycontainerregistry1220azurecr.io/samples/hello-world:v1' locally
v1: Pulling from samples/hello-world
Digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e888a
Status: Downloaded newer image for mycontainerregistry1220azurecr.io/samples/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]

2020/03/09 20:30:13 Successfully executed container: acb_step_0
2020/03/09 20:30:13 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.180081)

Run ID: cf4 was successful after 5s
```

## <a name="view-stored-logs"></a>Opgeslagen logboeken weergeven 

Azure Container Registry-opslag voert logboeken uit voor alle taken. U opgeslagen run-logboeken weergeven in de Azure-portal. Of gebruik de opdracht [az acr-taaklogboeken](/cli/azure/acr/task#az-acr-task-logs) om een geselecteerd logboek weer te geven. Logopeerlogboeken worden standaard 30 dagen bewaard.

Als een taak automatisch wordt geactiveerd, bijvoorbeeld door een broncode-update, is toegang tot de opgeslagen logboeken de *enige* manier om de run logs weer te geven. Automatische taaktriggers omvatten broncodecommits of pull-aanvragen, basisafbeeldingsupdates en timertriggers.

Ga als bedoeld als u logboeken in de portal weerziet:

1. Navigeer naar uw containerregister.
1. Selecteer Taken **uitvoeren** > **Runs**in **Services**.
1. Selecteer een **run-id** om de runstatus weer te geven en logboeken uit te voeren. Het logboek bevat dezelfde informatie als een gestreamd logboek, als er een wordt gegenereerd.

![Inlogportal voor taakbeheer weergeven](./media/container-registry-tasks-logs/portal-task-run-logs.png)

Als u een logboek wilt weergeven met de Azure CLI, voert u [az acr-taaklogboeken](/cli/azure/acr/task#az-acr-task-logs) uit en geeft u een run-id, een taaknaam of een specifieke afbeelding op die door een buildtaak wordt gemaakt. Als een taaknaam is opgegeven, wordt in de opdracht het logboek voor de laatst gemaakte run weergegeven.

In het volgende voorbeeld wordt het logboek voor de run uitgevoerd met ID *cf4:*

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>Alternatieve logboekopslag

U logboeken voor taakbeheer opslaan op een lokaal bestandssysteem of een alternatieve archiveringsoplossing gebruiken, zoals Azure Storage.

Maak bijvoorbeeld een lokale *taaklogboekmap* en verwijs de uitvoer van [az acr-taaklogboeken](/cli/azure/acr/task#az-acr-task-logs) om naar een lokaal bestand:

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

U ook lokale logboekbestanden opslaan in Azure Storage. Gebruik bijvoorbeeld de [Azure CLI,](../storage/blobs/storage-quickstart-blobs-cli.md)de [Azure-portal](../storage/blobs/storage-quickstart-blobs-portal.md)of andere methoden om bestanden naar een opslagaccount te uploaden.


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure Container Registry Tasks](container-registry-tasks-overview.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
