---
title: Verificatie voor kruisregister van ACR-taak
description: Een Azure Container Registry Task (ACR-taak) configureren om toegang te krijgen tot een ander privé-Azure-containerregister met behulp van een beheerde identiteit voor Azure-resources
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 47b2a50784cf56b089fea0981e5a06d581b8ba3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842487"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Verificatie in het register in een ACR-taak met een door Azure beheerde identiteit 

In een [ACR-taak](container-registry-tasks-overview.md)u [een beheerde identiteit voor Azure-resources inschakelen.](container-registry-tasks-authentication-managed-identity.md) De taak kan de identiteit gebruiken om toegang te krijgen tot andere Azure-bronnen, zonder dat u referenties hoeft op te geven of te beheren. 

In dit artikel leert u hoe u een beheerde identiteit in een taak inschakelt om een afbeelding uit een ander register te halen dan het register dat wordt gebruikt om de taak uit te voeren.

Als u de Azure-resources wilt maken, moet u in dit artikel de Azure CLI-versie 2.0.68 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli].

## <a name="scenario-overview"></a>Overzicht van scenario's

De voorbeeldtaak haalt een basisafbeelding uit een ander Azure-containerregister om een toepassingsafbeelding te maken en te pushen. Als u de basisafbeelding wilt trekken, configureert u de taak met een beheerde identiteit en wijst u de juiste machtigingen toe. 

In dit voorbeeld worden stappen weergegeven met een door de gebruiker toegewezen of door het systeem toegewezen beheerde identiteit. Uw identiteitskeuze is afhankelijk van de behoeften van uw organisatie.

In een real-world scenario kan een organisatie een set basisafbeeldingen onderhouden die door alle ontwikkelteams worden gebruikt om hun toepassingen te bouwen. Deze basisafbeeldingen worden opgeslagen in een bedrijfsregister, waarbij elk ontwikkelteam alleen pull-rechten heeft. 

## <a name="prerequisites"></a>Vereisten

Voor dit artikel hebt u twee Azure-containerregisters nodig:

* U gebruikt het eerste register om ACR-taken te maken en uit te voeren. In dit artikel wordt dit register *myregistry*genoemd. 
* In het tweede register wordt een basisafbeelding weergegeven die wordt gebruikt voor het maken van een afbeelding. In dit artikel wordt het tweede register *mybaseregistry*genoemd. 

Vervang in latere stappen door uw eigen registernamen.

Zie [Quickstart: Een privécontainerregister maken met de Azure CLI](container-registry-get-started-azure-cli.md)als u nog niet over de benodigde Azure-containerregisters beschikt. U hoeft nog geen afbeeldingen naar het register te pushen.

## <a name="prepare-base-registry"></a>Basisregister voorbereiden

Maak eerst een werkmap en maak vervolgens een bestand met de naam Dockerfile met de volgende inhoud. In dit eenvoudige voorbeeld wordt een basisafbeelding van Node.js gemaakt op basis van een openbare afbeelding in Docker Hub.
    
```bash
echo FROM node:9-alpine > Dockerfile
```
Voer in de huidige map de opdracht [az acr build][az-acr-build] uit om de basisafbeelding naar het basisregister te bouwen en te pushen. In de praktijk kan een ander team of proces in de organisatie het basisregister bijhouden.
    
```azurecli
az acr build --image baseimages/node:9-alpine --registry mybaseregistry --file Dockerfile .
```

## <a name="define-task-steps-in-yaml-file"></a>Taakstappen definiëren in YAML-bestand

De stappen voor dit voorbeeld [met meerdere stappen worden](container-registry-tasks-multi-step.md) gedefinieerd in een [YAML-bestand](container-registry-tasks-reference-yaml.md). Maak een `helloworldtask.yaml` bestand met de naam in uw lokale werkmap en plak de volgende inhoud. Werk de `REGISTRY_NAME` waarde van in de buildstap bij met de servernaam van uw basisregister.

```yml
version: v1.1.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t $Registry/hello-world:$ID  https://github.com/Azure-Samples/acr-build-helloworld-node.git -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["$Registry/hello-world:$ID"]
```

De buildstap `Dockerfile-app` gebruikt het bestand in de repo [Azure-Samples/acr-build-helloworld-node](https://github.com/Azure-Samples/acr-build-helloworld-node.git) om een afbeelding te maken. De `--build-arg` verwijzingen naar het basisregister om de basisafbeelding te trekken. Wanneer de afbeelding met succes is gebouwd, wordt deze naar het register gepusht dat wordt gebruikt om de taak uit te voeren.

## <a name="option-1-create-task-with-user-assigned-identity"></a>Optie 1: Taak maken met door de gebruiker toegewezen identiteit

De stappen in deze sectie maken een taak en schakelen een door de gebruiker toegewezen identiteit in. Zie [Optie 2: Taak maken met een door het systeem toegewezen identiteit](#option-2-create-task-with-system-assigned-identity)als u in plaats daarvan een door het systeem toegewezen identiteit wilt inschakelen. 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Taak maken

Maak de taak *helloworldtask* door de volgende [opdracht az acr-taak te maken][az-acr-task-create] uit te voeren. De taak wordt uitgevoerd zonder broncodecontext en `helloworldtask.yaml` de opdracht verwijst naar het bestand in de werkmap. De `--assign-identity` parameter geeft de resource-id van de door de gebruiker toegewezen identiteit door. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>Optie 2: Taak maken met door het systeem toegewezen identiteit

De stappen in deze sectie maken een taak en schakelen een door het systeem toegewezen identiteit in. Zie [Optie 1: Taak maken met door de gebruiker toegewezen identiteit](#option-1-create-task-with-user-assigned-identity)als u in plaats daarvan een door de gebruiker toegewezen identiteit wilt inschakelen. 

### <a name="create-task"></a>Taak maken

Maak de taak *helloworldtask* door de volgende [opdracht az acr-taak te maken][az-acr-task-create] uit te voeren. De taak wordt uitgevoerd zonder broncodecontext en `helloworldtask.yaml` de opdracht verwijst naar het bestand in de werkmap. De `--assign-identity` parameter zonder waarde maakt de door het systeem toegewezen identiteit op de taak mogelijk. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="give-identity-pull-permissions-to-the-base-registry"></a>Machtigingen voor identiteitspull en identiteit geven aan het basisregister

Geef in deze sectie de beheerde identiteitsmachtigingen die u uit het basisregister, *mybaseregistry,* moet halen.

Gebruik de opdracht [az acr show][az-acr-show] om de resource-ID van het basisregister op te halen en op te slaan in een variabele:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Gebruik de opdracht Voor het maken `acrpull` van [az-roltoewijzing][az-role-assignment-create] om de identiteit van de rol toe te wijzen aan het basisregister. Deze rol heeft alleen machtigingen om afbeeldingen uit het register te halen.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>Doelregisterreferenties toevoegen aan taak

Gebruik nu de [opdracht az acr-taakreferenties om][az-acr-task-credential-add] de taak te verifiëren met het basisregister met behulp van de referenties van de identiteit. Voer de opdracht uit die overeenkomt met het type beheerde identiteit dat u in de taak hebt ingeschakeld. Als u een door de gebruiker `--use-identity` toegewezen identiteit hebt ingeschakeld, geeft u de client-id van de identiteit door. Als u een door het systeem `--use-identity [system]`toegewezen identiteit hebt ingeschakeld, geeft u door .

```azurecli
# Add credentials for user-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity $clientID

# Add credentials for system-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity [system]
```

## <a name="manually-run-the-task"></a>De taak handmatig uitvoeren

Als u wilt controleren of de taak waarin u een beheerde identiteit hebt ingeschakeld, succesvol wordt uitgevoerd, activeert u de taak handmatig met de opdracht [AZ ACR-taakuitvoeren.][az-acr-task-run] 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

Als de taak succesvol wordt uitgevoerd, is de uitvoer vergelijkbaar met:

```
Queued a run with ID: cf10
Waiting for an agent...
2019/06/14 22:47:32 Using acb_vol_dbfbe232-fd76-4ca3-bd4a-687e84cb4ce2 as the home volume
2019/06/14 22:47:39 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/14 22:47:40 Successfully set up Docker network: acb_default_network
2019/06/14 22:47:40 Setting up Docker configuration...
2019/06/14 22:47:41 Successfully set up Docker configuration
2019/06/14 22:47:41 Logging in to registry: myregistry.azurecr.io
2019/06/14 22:47:42 Successfully logged into myregistry.azurecr.io
2019/06/14 22:47:42 Logging in to registry: mybaseregistry.azurecr.io
2019/06/14 22:47:43 Successfully logged into mybaseregistry.azurecr.io
2019/06/14 22:47:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:43 Scanning for dependencies...
2019/06/14 22:47:45 Successfully scanned dependencies
2019/06/14 22:47:45 Launching container with name: acb_step_0
Sending build context to Docker daemon   25.6kB
Step 1/6 : ARG REGISTRY_NAME
Step 2/6 : FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
9-alpine: Pulling from baseimages/node
[...]
Successfully built 41b49a112663
Successfully tagged myregistry.azurecr.io/hello-world:cf10
2019/06/14 22:47:56 Successfully executed container: acb_step_0
2019/06/14 22:47:56 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:56 Pushing image: myregistry.azurecr.io/hello-world:cf10, attempt 1
The push refers to repository [myregistry.azurecr.io/hello-world]
[...]
2019/06/14 22:48:00 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.517011)
2019/06/14 22:48:00 The following dependencies were found:
2019/06/14 22:48:00
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf10
    digest: sha256:611cf6e3ae3cb99b23fadcd89fa144e18aa1b1c9171ad4a0da4b62b31b4e38d1
  runtime-dependency:
    registry: mybaseregistry.azurecr.io
    repository: baseimages/node
    tag: 9-alpine
    digest: sha256:e8e92cffd464fce3be9a3eefd1b65dc9cbe2484da31c11e813a4effc6105c00f
  git:
    git-head-revision: 0f988779c97fe0bfc7f2f74b88531617f4421643

Run ID: cf10 was successful after 32s
```

Voer de opdracht [az acr repository show-tags][az-acr-repository-show-tags] uit om te controleren of de afbeelding is gebouwd en met succes naar *myregistry*is geduwd:

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

Voorbeelduitvoer:

```console
cf10
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het inschakelen van een beheerde identiteit in een ACR-taak](container-registry-tasks-authentication-managed-identity.md).
* Zie de [ACR-taken YAML-referentie](container-registry-tasks-reference-yaml.md)

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
