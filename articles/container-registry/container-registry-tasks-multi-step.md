---
title: Taak in meerdere stappen om & patchafbeelding te bouwen, te testen
description: Inleiding tot taken in meerdere stappen, een functie van ACR-taken in Azure Container Registry die taakgebaseerde werkstromen biedt voor het maken, testen en patchen van containerafbeeldingen in de cloud.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 0dcd38559d3f50715f982de4c9c80bfe9c6c8433
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399693"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Taken met meerdere stappen bouwen, testen en patchen uitvoeren in ACR-taken

Taken in meerdere stappen breiden de single image build-and-push-mogelijkheden van ACR-taken uit met workflows in meerdere stappen, meerdere containers. Gebruik taken in meerdere stappen om meerdere afbeeldingen te bouwen en te pushen, in serie of parallel. Voer deze afbeeldingen vervolgens uit als opdrachten binnen één taaktaak. Elke stap definieert een containerimagebuild- of pushbewerking en kan ook de uitvoering van een container definiëren. Elke stap in een taak in meerdere stappen gebruikt een container als uitvoeringsomgeving.

> [!IMPORTANT]
> Als u eerder taken hebt gemaakt tijdens de preview met de opdracht `az acr build-task`, moeten deze taken opnieuw worden gemaakt met de opdracht [az acr task][az-acr-task].

U bijvoorbeeld een taak uitvoeren met stappen die de volgende logica automatiseren:

1. Een afbeelding van een webtoepassing maken
1. De webtoepassingscontainer uitvoeren
1. Een testafbeelding voor webtoepassingen maken
1. Voer de testcontainer voor webtoepassingen uit die tests uitvoert met de lopende toepassingscontainer
1. Als de tests slagen, bouwt u een Helm-archiefpakket
1. Een `helm upgrade` pakket met behulp van het nieuwe helmdiagramarchief uitvoeren

Alle stappen worden uitgevoerd binnen Azure, waarbij het werk wordt verwijderd naar de rekenbronnen van Azure en u wordt bevrijd van infrastructuurbeheer. Naast uw Azure-containerregister betaalt u alleen voor de resources die u gebruikt. Zie de sectie **Containerbuild** in azure [containerregistry-prijzen][pricing]voor informatie over de prijzen.


## <a name="common-task-scenarios"></a>Veelvoorkomende taakscenario's

Taken in meerdere stappen maken scenario's mogelijk, zoals de volgende logica:

* Bouw, tag en duw een of meer containerafbeeldingen, in serie of parallel.
* Test- en codedekkingsresultaten uitvoeren en vastleggen.
* Voer functionele tests uit en leg deze vast. ACR Tasks ondersteunt het uitvoeren van meer dan één container en voert een reeks aanvragen tussen deze containers uit.
* Uitvoeren op taakgebaseerde uitvoering, inclusief pre/post-stappen van een containerimagebuild.
* Implementeer een of meer containers met uw favoriete implementatie-engine in uw doelomgeving.

## <a name="multi-step-task-definition"></a>Taakdefinitie in meerdere stappen

Een taak in meerdere stappen in ACR-taken wordt gedefinieerd als een reeks stappen in een YAML-bestand. In elke stap kunnen afhankelijkheden van de succesvolle voltooiing van een of meer eerdere stappen worden opgegeven. De volgende taakstaptypen zijn beschikbaar:

* [`build`](container-registry-tasks-reference-yaml.md#build): Maak een of meer `docker build` containerafbeeldingen met behulp van bekende syntaxis, in reeks of parallel.
* [`push`](container-registry-tasks-reference-yaml.md#push): Duw gebouwde afbeeldingen naar een containerregister. Privéregisters zoals Azure Container Registry worden ondersteund, evenals de openbare Docker Hub.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): Voer een container uit, zodat deze als functie kan functioneren binnen de context van de lopende taak. U parameters doorgeven aan `[ENTRYPOINT]`de container en eigenschappen opgeven zoals `docker run` env, losmaken en andere bekende parameters. Het `cmd` staptype maakt eenheids- en functionele tests mogelijk, met gelijktijdige uitvoering van containers.

In de volgende fragmenten ziet u hoe u deze taakstaptypen combineren. Taken in meerdere stappen kunnen net zo eenvoudig zijn als het bouwen van één afbeelding uit een Dockerfile en naar uw register pushen, met een YAML-bestand dat lijkt op:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
  - push: ["$Registry/hello-world:$ID"]
```

Of complexer, zoals deze fictieve definitie in meerdere stappen die stappen bevat voor het bouwen, testen, helmpakket en het implementeren van het roer (containerregister en Helm-repository-configuratie niet weergegeven):

```yml
version: v1.1.0
steps:
  - id: build-web
    build: -t $Registry/hello-world:$ID .
    when: ["-"]
  - id: build-tests
    build -t $Registry/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["$Registry/helloworld:$ID"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: $Registry/helloworld:$ID
  - id: funcTests
    cmd: $Registry/helloworld:$ID
    env: ["host=helloworld:80"]
  - cmd: $Registry/functions/helm package --app-version $ID -d ./helm ./helm/helloworld/
  - cmd: $Registry/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image=$Registry/helloworld:$ID
```

Zie [taakvoorbeelden](container-registry-tasks-samples.md) voor YAML-bestanden met meerdere stappen en Dockerfiles voor verschillende scenario's.

## <a name="run-a-sample-task"></a>Een voorbeeldtaak uitvoeren

Taken ondersteunen zowel handmatige uitvoering, een zogenaamde 'quick run' als geautomatiseerde uitvoering op Git commit of base image update.

Als u een taak wilt uitvoeren, definieert u eerst de stappen van de taak in een YAML-bestand en voert u vervolgens de opdracht Azure CLI [uit die AZ ACR uitvoert.][az-acr-run]

Hier vindt u een voorbeeld van de opdracht Azure CLI waarmee een taak wordt uitgevoerd met een YAML-bestand voor voorbeeldtaken. De stappen bouwen en duw vervolgens een afbeelding. Werk `\<acrName\>` bij met de naam van uw eigen Azure-containerregister voordat u de opdracht uitvoert.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Wanneer u de taak uitvoert, moet de uitvoer de voortgang weergeven van elke stap die is gedefinieerd in het YAML-bestand. In de volgende uitvoer worden `acb_step_0` `acb_step_1`de stappen weergegeven als en .

```azurecli
az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

```output
Sending context to registry: myregistry...
Queued a run with ID: yd14
Waiting for an agent...
2018/09/12 20:08:44 Using acb_vol_0467fe58-f6ab-4dbd-a022-1bb487366941 as the home volume
2018/09/12 20:08:44 Creating Docker network: acb_default_network
2018/09/12 20:08:44 Successfully set up Docker network: acb_default_network
2018/09/12 20:08:44 Setting up Docker configuration...
2018/09/12 20:08:45 Successfully set up Docker configuration
2018/09/12 20:08:45 Logging in to registry: myregistry.azurecr-test.io
2018/09/12 20:08:46 Successfully logged in
2018/09/12 20:08:46 Executing step: acb_step_0
2018/09/12 20:08:46 Obtaining source code and scanning for dependencies...
2018/09/12 20:08:47 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  109.6kB
Step 1/1 : FROM hello-world
 ---> 4ab4c602aa5e
Successfully built 4ab4c602aa5e
Successfully tagged myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:48 Executing step: acb_step_1
2018/09/12 20:08:48 Pushing image: myregistry.azurecr-test.io/hello-world:yd14, attempt 1
The push refers to repository [myregistry.azurecr-test.io/hello-world]
428c97da766c: Preparing
428c97da766c: Layer already exists
yd14: digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812 size: 524
2018/09/12 20:08:55 Successfully pushed image: myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:55 Step id: acb_step_0 marked as successful (elapsed time in seconds: 2.035049)
2018/09/12 20:08:55 Populating digests for step id: acb_step_0...
2018/09/12 20:08:57 Successfully populated digests for step id: acb_step_0
2018/09/12 20:08:57 Step id: acb_step_1 marked as successful (elapsed time in seconds: 6.832391)
The following dependencies were found:
- image:
    registry: myregistry.azurecr-test.io
    repository: hello-world
    tag: yd14
    digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
  git: {}


Run ID: yd14 was successful after 19s
```

Zie de [analyse van image builds](container-registry-tutorial-build-task.md) en Base image [update](container-registry-tutorial-base-image-update.md) voor meer informatie over geautomatiseerde builds op Git commit of base image update.

## <a name="next-steps"></a>Volgende stappen

U vindt hier de verwijzing naar taken en voorbeelden in meerdere stappen:

* [Taakverwijzing](container-registry-tasks-reference-yaml.md) - Taakstaptypen, hun eigenschappen en gebruik.
* [Taakvoorbeelden](container-registry-tasks-samples.md) - `task.yaml` Voorbeeld- en Docker-bestanden voor verschillende scenario's, eenvoudig tot complex.
* [Cmd repo](https://github.com/AzureCR/cmd) - Een verzameling containers als opdrachten voor ACR-taken.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task]: /cli/azure/acr/task