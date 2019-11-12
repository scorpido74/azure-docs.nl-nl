---
title: 'Zelf studie: taak werk stroom met meerdere stappen-Azure Container Registry taken'
description: In deze zelf studie leert u hoe u een Azure Container Registry taak kunt configureren om automatisch een werk stroom met meerdere stappen te activeren om container installatie kopieën in de cloud te bouwen, uit te voeren en te pushen wanneer u de bron code doorvoert in een Git-opslag plaats.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: dd7dd6f78087a40de93a5ab47936806076fc562a
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931453"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>Zelf studie: een container werk stroom met meerdere stappen in de Cloud uitvoeren bij het door voeren van de bron code

Naast een [snelle taak](container-registry-tutorial-quick-task.md)ondersteunt ACR-taken meerdere stappen, op meerdere containers gebaseerde werk stromen die automatisch kunnen worden geactiveerd wanneer u bron code doorvoert in een Git-opslag plaats. 

In deze zelf studie leert u hoe u voor beelden van YAML-bestanden kunt gebruiken om taken met meerdere stappen te definiëren die een of meer container installatie kopieën bouwen, uitvoeren en pushen naar een REGI ster wanneer u de bron code doorvoert. Zie [zelf studie: builds van container installatie kopieën automatiseren in de Cloud wanneer u bron code doorvoert](container-registry-tutorial-build-task.md)om een taak te maken die slechts één installatie kopie automatiseert die door de code wordt gemaakt. Zie voor een overzicht van ACR-taken het automatiseren van het [besturings systeem en Framework-patches met ACR-taken](container-registry-tasks-overview.md).

In deze zelf studie:

> [!div class="checklist"]
> * Een taak met meerdere stappen definiëren met behulp van een YAML-bestand
> * Een taak maken
> * Eventueel referenties toevoegen aan de taak om toegang tot een ander REGI ster mogelijk te maken
> * De taak testen
> * Taakstatus weergeven
> * De taak activeren met een code-doorvoer

In deze zelfstudie wordt ervan uitgegaan dat u de stappen in de [vorige zelfstudie](container-registry-tutorial-quick-task.md) hebt voltooid. Als u dit nog niet hebt gedaan, voert u de stappen in de sectie [Vereisten](container-registry-tutorial-quick-task.md#prerequisites) van de vorige zelfstudie uit voordat u doorgaat.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u de Azure CLI lokaal wilt gebruiken, moet u Azure CLI versie **2.0.62** of hoger hebben geïnstalleerd en zijn aangemeld met [AZ login][az-login]. Voer `az --version` uit om de versie te bekijken. Als u de CLI wilt installeren of upgraden, raadpleegt u [Azure cli installeren][azure-cli].

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-a-multi-step-task"></a>Een taak met meerdere stappen maken

Nu u de stappen hebt voltooid die vereist zijn om ACR-taken in te scha kelen voor het lezen van de commit-status en het maken van webhooks in een opslag plaats, maakt u een taak met meerdere stappen die het bouwen, uitvoeren en het pushen van een container installatie kopie activeert.

### <a name="yaml-file"></a>YAML-bestand

U definieert de stappen voor een taak met meerdere stappen in een [yaml-bestand](container-registry-tasks-reference-yaml.md). Het eerste voor beeld van een taak met meerdere stappen voor deze zelf studie is gedefinieerd in het bestand `taskmulti.yaml`, dat zich in de hoofdmap van de GitHub opslag plaats bevindt die u hebt gekloond:

```yml
version: v1.0.0
steps:
# Build target image
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push image
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
```

Deze taak met meerdere stappen doet het volgende:

1. Voert een `build` stap uit om een installatie kopie te maken van de Dockerfile in de werkmap. De afbeelding is gericht op de `Run.Registry`, het REGI ster waar de taak wordt uitgevoerd en is gelabeld met een unieke run-ID voor ACR-taken. 
1. Voert een `cmd` stap uit om de installatie kopie in een tijdelijke container uit te voeren. In dit voor beeld wordt een langlopende container op de achtergrond gestart en wordt de container-ID geretourneerd, waarna de container wordt gestopt. In een praktijk scenario kunt u stappen nemen om de actieve container te testen om te controleren of deze correct wordt uitgevoerd.
1. In een `push` stap duwt u de installatie kopie die is gebouwd voor het uitvoeren van het REGI ster.

### <a name="task-command"></a>Taak opdracht

Vul eerst deze shell-omgevingsvariabelen met waarden die geschikt zijn voor uw omgeving. Hoewel deze stap strikt genomen niet vereist is, vereenvoudigt u hiermee de uitvoering van meerregelige Azure CLI-opdrachten in deze zelfstudie. Als u deze omgevings variabelen niet opgeeft, moet u elke waarde hand matig vervangen, overal waar deze in de voorbeeld opdrachten worden weer gegeven.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Maak nu de taak door de volgende [AZ ACR taak Create][az-acr-task-create] opdracht uit te voeren:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

Met deze taak wordt aangegeven dat elke tijd code wordt doorgevoerd in de *hoofd* vertakking in de opslag plaats die is opgegeven door `--context`, ACR-taken uitvoeren de taak met meerdere stappen vanuit de code in die vertakking. Het YAML-bestand dat is opgegeven door `--file` uit de hoofdmap van de opslag plaats definieert de stappen. 

De uitvoer van een geslaagd [AZ ACR taak Create][az-acr-task-create] opdracht lijkt op het volgende:

```console
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2019-05-03T03:14:31.763887+00:00",
  "credentials": null,
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskmulti",
  "location": "westus",
  "name": "example1",
  "platform": {
    "architecture": "amd64",
    "os": "linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "status": "Enabled",
  "step": {
    "baseImageDependencies": null,
    "contextAccessToken": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node.git",
    "taskFilePath": "taskmulti.yaml",
    "type": "FileTask",
    "values": [],
    "valuesFilePath": null
  },
  "tags": null,
  "timeout": 3600,
  "trigger": {
    "baseImageTrigger": {
      "baseImageTriggerType": "Runtime",
      "name": "defaultBaseimageTriggerName",
      "status": "Enabled"
    },
    "sourceTriggers": [
      {
        "name": "defaultSourceTriggerName",
        "sourceRepository": {
          "branch": "master",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node.git",
          "sourceControlAuthProperties": null,
          "sourceControlType": "Github"
        },
        "sourceTriggerEvents": [
          "commit"
        ],
        "status": "Enabled"
      }
    ]
  },
  "type": "Microsoft.ContainerRegistry/registries/tasks"
}
```

## <a name="test-the-multi-step-workflow"></a>De werk stroom met meerdere stappen testen

Als u de taak met meerdere stappen wilt testen, moet u deze hand matig activeren door de opdracht [AZ ACR Task run uit][az-acr-task-run] te voeren:

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

Met de opdracht `az acr task run` streamt u standaard de logboekuitvoer naar de console wanneer u de opdracht uitvoert. In de uitvoer ziet u de voortgang van het uitvoeren van elk van de taak stappen. De onderstaande uitvoer is versmald om de belangrijkste stappen weer te geven.

```console
Queued a run with ID: cf19
Waiting for an agent...
2019/05/03 03:03:31 Downloading source code...
2019/05/03 03:03:33 Finished downloading source code
2019/05/03 03:03:33 Using acb_vol_cfe6bd55-3076-4215-8091-6a81aec3d1b1 as the home volume
2019/05/03 03:03:33 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 03:03:34 Successfully set up Docker network: acb_default_network
2019/05/03 03:03:34 Setting up Docker configuration...
2019/05/03 03:03:34 Successfully set up Docker configuration
2019/05/03 03:03:34 Logging in to registry: myregistry.azurecr.io
2019/05/03 03:03:35 Successfully logged into myregistry.azurecr.io
2019/05/03 03:03:35 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:35 Scanning for dependencies...
2019/05/03 03:03:36 Successfully scanned dependencies
2019/05/03 03:03:36 Launching container with name: acb_step_0
Sending build context to Docker daemon  24.06kB

[...]

Successfully built f669bfd170af
Successfully tagged myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:43 Successfully executed container: acb_step_0
2019/05/03 03:03:43 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:43 Launching container with name: acb_step_1
279b1cb6e092b64c8517c5506fcb45494cd5a0bd10a6beca3ba97f25c5d940cd
2019/05/03 03:03:44 Successfully executed container: acb_step_1
2019/05/03 03:03:44 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:44 Pushing image: myregistry.azurecr.io/hello-world:cf19, attempt 1

[...]

2019/05/03 03:03:46 Successfully pushed image: myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:46 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 7.425169)
2019/05/03 03:03:46 Populating digests for step ID: acb_step_0...
2019/05/03 03:03:47 Successfully populated digests for step ID: acb_step_0
2019/05/03 03:03:47 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 0.827129)
2019/05/03 03:03:47 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.112113)
2019/05/03 03:03:47 The following dependencies were found:
2019/05/03 03:03:47
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf19
    digest: sha256:6b981a8ca8596e840228c974c929db05c0727d8630465de536be74104693467a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 1a3065388a0238e52865db1c8f3e97492a43444c

Run ID: cf19 was successful after 18s
```

## <a name="trigger-a-build-with-a-commit"></a>Een build activeren met een doorvoer

Nu u de taak hebt getest door deze handmatig uit te voeren, gaat u deze automatisch activeren met een wijziging in de broncode.

Zorg er eerst voor dat u zich in de map bevindt met uw lokale kloon van de [opslag plaats][sample-repo]:

```azurecli-interactive
cd acr-build-helloworld-node
```

Voer vervolgens de volgende opdrachten uit om een nieuw bestand te maken, door te voeren en pushen naar uw fork van de opslagplaats op GitHub:

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

U wordt mogelijk gevraagd om uw GitHub-referenties op te geven wanneer u de opdracht `git push` uitvoert. Geef uw GitHub-gebruikersnaam op en voer het persoonlijke toegangstoken (PAT) in dat u eerder hebt gemaakt voor het wachtwoord.

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Zodra u een door Voer hebt gepusht naar uw opslag plaats, wordt de webhook die is gemaakt door ACR-taken geactiveerd en wordt de taak in Azure Container Registry. Geef de logboeken weer voor de taak die momenteel wordt uitgevoerd om de voortgang van de build te verifiëren en controleren:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

De uitvoer is vergelijkbaar met de volgende, waarin de op dit moment uitgevoerde (of de laatst uitgevoerde) taak wordt getoond:

```console
$ az acr task logs --registry $ACR_NAME
Showing logs of the last created run.
Run ID: cf1d

[...]

Run ID: cf1d was successful after 37s
```

## <a name="list-builds"></a>Builds weergeven

Als u een lijst met taken wilt weer geven, voert u de opdracht [AZ ACR][az-acr-task-list-runs] ACR uit om de taken lijst uit te voeren die voor uw REGI ster is voltooid:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

De uitvoer van de opdracht ziet er ongeveer als volgt uit. De runs die ACR Tasks heeft uitgevoerd, worden weergegeven. 'Git Commit' wordt weergegeven in de kolom TRIGGER voor de meest recente taak:

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
cf1d      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:37
cf1c      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:39
cf1b      example1   linux       Succeeded  Manual     2019-05-03T03:10:30Z  00:00:31
cf1a      example1   linux       Succeeded  Commit     2019-05-03T03:09:32Z  00:00:31
cf19      example1   linux       Succeeded  Manual     2019-05-03T03:03:30Z  00:00:21
```

## <a name="create-a-multi-registry-multi-step-task"></a>Een taak met meerdere stappen maken

ACR-taken zijn standaard gemachtigd om installatie kopieën te pushen of te halen uit het REGI ster waarin de taak wordt uitgevoerd. U kunt een taak met meerdere stappen uitvoeren die een of meer registers bedoelt naast het uitvoerings register. U moet bijvoorbeeld installatie kopieën in één REGI ster bouwen en installatie kopieën met andere Tags opslaan in een tweede REGI ster dat wordt geopend door een productie systeem. Dit voor beeld laat zien hoe u een dergelijke taak maakt en referenties voor een ander REGI ster opgeeft.

Als u nog geen tweede REGI ster hebt, maakt u er een voor dit voor beeld. Als u een register nodig hebt, zie dan de [vorige zelfstudie](container-registry-tutorial-quick-task.md) of [Snelstartgids: Een containerregister maken met de Azure CLI](container-registry-get-started-azure-cli.md).

Als u de taak wilt maken, hebt u de naam van de aanmeldings server van het REGI ster nodig. Dit is de vorm *mycontainerregistrydate.azurecr.io* (alle kleine letters). In dit voor beeld gebruikt u het tweede REGI ster om installatie kopieën op te slaan die zijn gelabeld op basis van build date.

### <a name="yaml-file"></a>YAML-bestand

Het tweede voor beeld van een taak met meerdere stappen voor deze zelf studie is gedefinieerd in het bestand `taskmulti-multiregistry.yaml`, dat zich in de hoofdmap van de GitHub opslag plaats bevindt die u hebt gekloond:

```yml
version: v1.0.0
steps:
# Build target images
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
- build: -t {{.Values.regDate}}/hello-world:{{.Run.Date}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push images
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
  - {{.Values.regDate}}/hello-world:{{.Run.Date}}
```

Deze taak met meerdere stappen doet het volgende:

1. Voert twee `build` stappen uit om installatie kopieën te bouwen op basis van de Dockerfile in de werkmap:
    * Het eerste streeft naar het `Run.Registry`, het REGI ster waar de taak wordt uitgevoerd en is gelabeld met de run-ID van de ACR-taken. 
    * De tweede streeft naar het REGI ster geïdentificeerd door de waarde van `regDate`, die u hebt ingesteld bij het maken van de taak (of door middel van een extern `values.yaml` bestand dat is door gegeven aan `az acr task create`). Deze afbeelding is gelabeld met de uitvoerings datum.
1. Voert een `cmd` stap uit om een van de gebouwde containers uit te voeren. In dit voor beeld wordt een langlopende container op de achtergrond gestart en wordt de container-ID geretourneerd, waarna de container wordt gestopt. In een praktijk scenario kunt u een actieve container testen om er zeker van te zijn dat deze correct wordt uitgevoerd.
1. In een `push` stap duwt de installatie kopieën die zijn gebouwd, het eerste naar het Run Registry, de tweede naar het REGI ster geïdentificeerd door `regDate`.

### <a name="task-command"></a>Taak opdracht

Maak de taak met behulp van de eerder gedefinieerde shell-omgevings variabelen door de volgende [AZ ACR taak Create][az-acr-task-create] opdracht uit te voeren. Vervang de naam van het REGI ster door voor *mycontainerregistrydate*.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti-multiregistry.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Taak referentie toevoegen

Als u installatie kopieën wilt pushen naar het REGI ster dat wordt geïdentificeerd door de waarde van `regDate`, gebruikt u de opdracht [AZ ACR taak Credential add][az-acr-task-credential-add] om aanmeldings referenties voor het REGI ster toe te voegen aan de taak.

Voor dit voor beeld raden we u aan een [Service-Principal](container-registry-auth-service-principal.md) te maken met toegang tot het REGI ster die is toegewezen aan de *AcrPush* -rol. Zie dit [Azure CLI-script](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh)voor het maken van de Service-Principal.

Geef de toepassings-ID en het wacht woord van de Service-Principal op in de volgende `az acr task credential add` opdracht:

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

De CLI retourneert de naam van de aanmeldings server voor het REGI ster die u hebt toegevoegd.

### <a name="test-the-multi-step-workflow"></a>De werk stroom met meerdere stappen testen

Net als in het voor gaande voor beeld, kunt u de taak met meerdere stappen hand matig activeren door de opdracht [AZ ACR Task run uit][az-acr-task-run] te voeren. Als u de taak wilt activeren met een doorvoer bewerking voor de Git-opslag plaats, raadpleegt u de sectie [een build met een commit activeren](#trigger-a-build-with-a-commit).

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

Met de opdracht `az acr task run` streamt u standaard de logboekuitvoer naar de console wanneer u de opdracht uitvoert. Net als voorheen toont de uitvoer de voortgang van het uitvoeren van elk van de taak stappen. De uitvoer wordt versmald om de belangrijkste stappen weer te geven.

Uitvoer:

```console
Queued a run with ID: cf1g
Waiting for an agent...
2019/05/03 04:33:39 Downloading source code...
2019/05/03 04:33:41 Finished downloading source code
2019/05/03 04:33:42 Using acb_vol_4569b017-29fe-42bd-83b2-25c45a8ac807 as the home volume
2019/05/03 04:33:42 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 04:33:43 Successfully set up Docker network: acb_default_network
2019/05/03 04:33:43 Setting up Docker configuration...
2019/05/03 04:33:44 Successfully set up Docker configuration
2019/05/03 04:33:44 Logging in to registry: mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Successfully logged into mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Logging in to registry: mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Successfully logged into mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:47 Scanning for dependencies...
2019/05/03 04:33:47 Successfully scanned dependencies
2019/05/03 04:33:47 Launching container with name: acb_step_0
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:33:55 Successfully executed container: acb_step_0
2019/05/03 04:33:55 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:55 Scanning for dependencies...
2019/05/03 04:33:56 Successfully scanned dependencies
2019/05/03 04:33:56 Launching container with name: acb_step_1
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:33:57 Successfully executed container: acb_step_1
2019/05/03 04:33:57 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:57 Launching container with name: acb_step_2
721437ff674051b6be63cbcd2fa8eb085eacbf38d7d632f1a079320133182101
2019/05/03 04:33:58 Successfully executed container: acb_step_2
2019/05/03 04:33:58 Executing step ID: acb_step_3. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:58 Launching container with name: acb_step_3
test
2019/05/03 04:34:09 Successfully executed container: acb_step_3
2019/05/03 04:34:09 Executing step ID: acb_step_4. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:34:09 Pushing image: mycontainerregistry.azurecr.io/hello-world:cf1g, attempt 1
The push refers to repository [mycontainerregistry.azurecr.io/hello-world]

[...]

2019/05/03 04:34:12 Successfully pushed image: mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:34:12 Pushing image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z, attempt 1
The push refers to repository [mycontainerregistrydate.azurecr.io/hello-world]

[...]

2019/05/03 04:34:19 Successfully pushed image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:34:19 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 8.125744)
2019/05/03 04:34:19 Populating digests for step ID: acb_step_0...
2019/05/03 04:34:21 Successfully populated digests for step ID: acb_step_0
2019/05/03 04:34:21 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.009281)
2019/05/03 04:34:21 Populating digests for step ID: acb_step_1...
2019/05/03 04:34:23 Successfully populated digests for step ID: acb_step_1
2019/05/03 04:34:23 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 0.795440)
2019/05/03 04:34:23 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.446775)
2019/05/03 04:34:23 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 9.734973)
2019/05/03 04:34:23 The following dependencies were found:
2019/05/03 04:34:23
- image:
    registry: mycontainerregistry.azurecr.io
    repository: hello-world
    tag: cf1g
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc
- image:
    registry: mycontainerregistrydate.azurecr.io
    repository: hello-world
    tag: 20190503-043342z
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc

Run ID: cf1g was successful after 46s
```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u multi-step-taken op basis van meerdere containers kunt maken die automatisch worden geactiveerd wanneer u bron code doorvoert in een Git-opslag plaats. Zie de [ACR taken yaml-Naslag informatie](container-registry-tasks-reference-yaml.md)voor geavanceerde functies van taken met meerdere stappen, waaronder parallelle en afhankelijke uitvoering van stappen. Ga verder naar de volgende zelfstudie voor informatie over het maken van taken die builds activeren wanneer de basisinstallatiekopie van de containerinstallatiekopie wordt bijgewerkt.

> [!div class="nextstepaction"]
> [Builds automatiseren op basis van installatiekopie-update](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add    
[az-login]: /cli/azure/reference-index#az-login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
