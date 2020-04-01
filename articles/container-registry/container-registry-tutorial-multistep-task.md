---
title: Zelfstudie - ACR-taak met meerdere stappen
description: In deze zelfstudie leert u hoe u een Azure Container Registry Task configureert om automatisch een werkstroom in meerdere stappen te activeren om containerafbeeldingen in de cloud te bouwen, uit te voeren en te pushen wanneer u broncode vastlegt aan een Git-opslagplaats.
ms.topic: tutorial
ms.date: 05/09/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: ff32b3095638af6b2b246b99a5dc9219e0020782
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78402301"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>Zelfstudie: Een containerwerkstroom met meerdere stappen uitvoeren in de cloud wanneer u broncode vastlegt

Naast een [snelle taak](container-registry-tutorial-quick-task.md)ondersteunt ACR Tasks multi-step, multi-container-gebaseerde workflows die automatisch kunnen worden geactiveerd wanneer u broncode vastlegt aan een Git-opslagplaats. 

In deze zelfstudie leert u hoe u voorbeeld YAML-bestanden gebruikt om taken in meerdere stappen te definiëren die een of meer containerafbeeldingen bouwen, uitvoeren en pushen naar een register wanneer u broncode vastlegt. Zie [Zelfstudie: Containerimage builds in de cloud automatiseren wanneer u broncode vastlegt](container-registry-tutorial-build-task.md)als u een taak wilt maken die slechts één afbeelding op codecommit automatiseert. Zie [OS en framework patching automatiseren met ACR-taken](container-registry-tasks-overview.md)voor een overzicht van ACR-taken.

In deze zelfstudie hebt u het volgende gedaan:

> [!div class="checklist"]
> * Een taak in meerdere stappen definiëren met een YAML-bestand
> * Een taak maken
> * Optioneel referenties toevoegen aan de taak om toegang tot een ander register in te schakelen
> * De taak testen
> * Taakstatus weergeven
> * De taak activeren met een code-doorvoer

In deze zelfstudie wordt ervan uitgegaan dat u de stappen in de [vorige zelfstudie](container-registry-tutorial-quick-task.md) hebt voltooid. Als u dit nog niet hebt gedaan, voert u de stappen in de sectie [Vereisten](container-registry-tutorial-quick-task.md#prerequisites) van de vorige zelfstudie uit voordat u doorgaat.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u de Azure CLI lokaal wilt gebruiken, moet u Azure CLI-versie **2.0.62** of hoger hebben geïnstalleerd en ingelogd met [az-aanmelding.][az-login] Voer `az --version` uit om de versie te bekijken. Als u de CLI wilt installeren of upgraden, raadpleegt u [Azure CLI installeren][azure-cli].

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-a-multi-step-task"></a>Een taak in meerdere stappen maken

Nu u de stappen hebt voltooid die nodig zijn om ACR-taken in staat te stellen commitstatus te lezen en webhooks in een opslagplaats te maken, maakt u een taak in meerdere stappen die het bouwen, uitvoeren en pushen van een containerafbeelding activeert.

### <a name="yaml-file"></a>YAML-bestand

U definieert de stappen voor een taak in meerdere stappen in een [YAML-bestand.](container-registry-tasks-reference-yaml.md) De eerste voorbeeldtaak voor meerdere stappen voor deze `taskmulti.yaml`zelfstudie wordt gedefinieerd in het bestand , dat zich in de hoofdmap bevindt van de GitHub-repo die u hebt gekloond:

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

Deze taak in meerdere stappen doet het volgende:

1. Voert `build` een stap uit om een afbeelding te maken vanuit het Dockerbestand in de werkmap. De afbeelding `Run.Registry`is gericht op het register waarin de taak wordt uitgevoerd en wordt getagd met een unieke ACR-taken-run-id. 
1. Voert `cmd` een stap uit om de afbeelding in een tijdelijke container uit te voeren. In dit voorbeeld wordt een langlopende container op de achtergrond gestart en wordt de container-ID geretourneerd en wordt de container gestopt. In een real-world scenario u stappen opnemen om de lopende container te testen om ervoor te zorgen dat deze correct wordt uitgevoerd.
1. In `push` een stap, duwt de afbeelding die is gebouwd om de run register.

### <a name="task-command"></a>Taak, opdracht

Vul eerst deze shell-omgevingsvariabelen met waarden die geschikt zijn voor uw omgeving. Hoewel deze stap strikt genomen niet vereist is, vereenvoudigt u hiermee de uitvoering van meerregelige Azure CLI-opdrachten in deze zelfstudie. Als u deze omgevingsvariabelen niet vult, moet u elke waarde handmatig vervangen, waar deze ook in de voorbeeldopdrachten wordt weergegeven.

[![Start insluiten](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell starten")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Maak nu de taak door de volgende [opdracht az acr-taak te maken][az-acr-task-create] uit te voeren:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

Met deze taak wordt opgegeven dat elke tijdcode is `--context`vastgelegd in de hoofdvertakking in de repository die is opgegeven door , acr-taken de taak met meerdere stappen uitvoeren vanuit de code in die vertakking. *master* Het YAML-bestand `--file` dat is opgegeven door de root van de opslagplaats, definieert de stappen. 

De uitvoer van een geslaagde [az acr task create][az-acr-task-create]-opdracht is vergelijkbaar met het volgende:

```output
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

## <a name="test-the-multi-step-workflow"></a>De werkstroom in meerdere stappen testen

Als u de taak met meerdere stappen wilt testen, activeert u deze handmatig door de opdracht [AZ ACR-taakuit te voeren:][az-acr-task-run]

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

Met de opdracht `az acr task run` streamt u standaard de logboekuitvoer naar de console wanneer u de opdracht uitvoert. De uitvoer toont de voortgang van het uitvoeren van elk van de taakstappen. De onderstaande uitvoer wordt gecondenseerd om belangrijke stappen weer te geven.

```output
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

Controleer eerst of u zich bevindt in de map waarin de lokale kloon van de [opslagplaats][sample-repo] staat:

```console
cd acr-build-helloworld-node
```

Voer vervolgens de volgende opdrachten uit om een nieuw bestand te maken, door te voeren en pushen naar uw fork van de opslagplaats op GitHub:

```console
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

U wordt mogelijk gevraagd om uw GitHub-referenties op te geven wanneer u de opdracht `git push` uitvoert. Geef uw GitHub-gebruikersnaam op en voer het persoonlijke toegangstoken (PAT) in dat u eerder hebt gemaakt voor het wachtwoord.

```azurecli-interactive
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Zodra u een commit naar uw opslagplaats hebt gepusht, wordt de webhook die door ACR Tasks is gemaakt, geactiveerd en wordt de taak gestart in Azure Container Registry. Geef de logboeken weer voor de taak die momenteel wordt uitgevoerd om de voortgang van de build te verifiëren en controleren:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

De uitvoer is vergelijkbaar met de volgende, waarin de op dit moment uitgevoerde (of de laatst uitgevoerde) taak wordt getoond:

```output
Showing logs of the last created run.
Run ID: cf1d

[...]

Run ID: cf1d was successful after 37s
```

## <a name="list-builds"></a>Builds weergeven

Voor een overzicht van de taken die ACR Tasks heeft voltooid voor het register, voert u de opdracht [az acr task-list-runs][az-acr-task-list-runs] uit:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

De uitvoer van de opdracht ziet er ongeveer als volgt uit. De runs die ACR Tasks heeft uitgevoerd, worden weergegeven. 'Git Commit' wordt weergegeven in de kolom TRIGGER voor de meest recente taak:

```output
RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
cf1d      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:37
cf1c      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:39
cf1b      example1   linux       Succeeded  Manual     2019-05-03T03:10:30Z  00:00:31
cf1a      example1   linux       Succeeded  Commit     2019-05-03T03:09:32Z  00:00:31
cf19      example1   linux       Succeeded  Manual     2019-05-03T03:03:30Z  00:00:21
```

## <a name="create-a-multi-registry-multi-step-task"></a>Een multi-registry multi-step taak maken

ACR Tasks heeft standaard machtigingen om afbeeldingen uit het register te duwen of te trekken waar de taak wordt uitgevoerd. U een taak in meerdere stappen uitvoeren die naast het run-register een of meer registers target. Het kan bijvoorbeeld nodig zijn om afbeeldingen in één register te bouwen en afbeeldingen met verschillende tags op te slaan in een tweede register dat wordt geopend door een productiesysteem. In dit voorbeeld ziet u hoe u een dergelijke taak maakt en referenties verstrekt voor een ander register.

Als u nog geen tweede register hebt, maakt u er een voor dit voorbeeld. Als u een register nodig hebt, zie dan de [vorige zelfstudie](container-registry-tutorial-quick-task.md) of [Snelstartgids: Een containerregister maken met de Azure CLI](container-registry-get-started-azure-cli.md).

Om de taak te maken, hebt u de naam van de registerinlogserver nodig, die van het formulier *mycontainerregistrydate.azurecr.io* (alle kleine letters) is. In dit voorbeeld gebruikt u het tweede register om afbeeldingen op te slaan die zijn getagd op de bouwdatum.

### <a name="yaml-file"></a>YAML-bestand

De tweede voorbeeldtaak voor meerdere stappen voor deze `taskmulti-multiregistry.yaml`zelfstudie wordt gedefinieerd in het bestand , dat zich in de hoofdmap bevindt van de GitHub-repo die u hebt gekloond:

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

Deze taak in meerdere stappen doet het volgende:

1. Voer `build` twee stappen uit om afbeeldingen uit het Dockerbestand in de werkmap te maken:
    * De eerste `Run.Registry`richt zich op het register waar de taak wordt uitgevoerd en wordt getagd met de acr-taken-run-id. 
    * De tweede is gericht op het `regDate`register dat wordt geïdentificeerd door de waarde `values.yaml` van , `az acr task create`die u instelt wanneer u de taak maakt (of verstrekt via een extern bestand dat is doorgegeven aan ). Deze afbeelding is gelabeld met de run datum.
1. Voert `cmd` een stap uit om een van de gebouwde containers uit te voeren. In dit voorbeeld wordt een langlopende container op de achtergrond gestart en wordt de container-ID geretourneerd en wordt de container gestopt. In een real-world scenario u een lopende container testen om ervoor te zorgen dat deze correct wordt uitgevoerd.
1. In `push` een stap, duwt de beelden die werden gebouwd, de eerste naar de `regDate`run register, de tweede naar het register geïdentificeerd door .

### <a name="task-command"></a>Taak, opdracht

Maak de taak met behulp van de eerder gedefinieerde shell-omgevingsvariabelen door de volgende opdracht [voor az acr-taak te maken.][az-acr-task-create] Vervang de naam van uw register voor *mycontainerregistrydate.*

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti-multiregistry.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Taakreferenties toevoegen

Als u afbeeldingen naar het register `regDate`wilt pushen dat is geïdentificeerd door de waarde van , voegt u de opdracht az [acr-taakreferenties toe][az-acr-task-credential-add] om inloggegevens voor dat register aan de taak toe te voegen.

In dit voorbeeld raden we u aan een [serviceprincipal](container-registry-auth-service-principal.md) te maken met toegang tot het register dat is afgestemd op de *AcrPush-rol.* Zie dit [Azure CLI-script](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh)als u de serviceprincipal wilt maken.

Geef de servicehoofdtoepassings-id en `az acr task credential add` het wachtwoord door in de volgende opdracht:

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

De CLI retourneert de naam van de registerinlogserver die u hebt toegevoegd.

### <a name="test-the-multi-step-workflow"></a>De werkstroom in meerdere stappen testen

Net als in het voorgaande voorbeeld, om de multi-step taak te testen, activeer deze handmatig door de opdracht [az acr-taak uit te voeren.][az-acr-task-run] Als u de taak wilt activeren met een commit naar de Git-repository, raadpleegt u de sectie [Trigger a build with a commit](#trigger-a-build-with-a-commit).

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

Met de opdracht `az acr task run` streamt u standaard de logboekuitvoer naar de console wanneer u de opdracht uitvoert. Net als voorheen geeft de uitvoer de voortgang weer van het uitvoeren van elk van de taakstappen. De uitvoer wordt gecondenseerd om belangrijke stappen weer te geven.

Uitvoer:

```output
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

In deze zelfstudie hebt u geleerd hoe u taken met meerdere containers met meerdere stappen maken die automatisch worden geactiveerd wanneer u broncode vastlegt aan een Git-opslagplaats. Zie de [ACR-taken YAML-verwijzing](container-registry-tasks-reference-yaml.md)voor geavanceerde functies van taken in meerdere stappen, waaronder parallelle en afhankelijke stapuitvoering. Ga verder naar de volgende zelfstudie voor informatie over het maken van taken die builds activeren wanneer de basisinstallatiekopie van de containerinstallatiekopie wordt bijgewerkt.

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
