---
title: Overzicht van ACR-taken
description: Een inleiding tot ACR Tasks, een reeks functies in Azure Container Registry die veilige, geautomatiseerde containerimage-build, beheer en patching in de cloud biedt.
ms.topic: article
ms.date: 01/22/2020
ms.openlocfilehash: 4fda57c1d7c866f2e6f72b04d75e53f91e995baf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087275"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Bouw en onderhoud van containerafbeeldingen automatiseren met ACR-taken

Containers bieden nieuwe niveaus van virtualisatie, waarbij toepassings- en ontwikkelaarsafhankelijkheden worden geïsoleerd van infrastructuur en operationele vereisten. Wat overblijft, is echter de noodzaak om aan te pakken hoe deze applicatie virtualisatie wordt beheerd en gepatcht over de levenscyclus van de container.

## <a name="what-is-acr-tasks"></a>Wat is ACR-taken?

**ACR Tasks** is een reeks functies binnen Azure Container Registry. Het biedt cloudgebaseerde containerimagebuilding voor [platforms](#image-platforms) zoals Linux, Windows en ARM, en kan [os- en frameworkpatching](#automate-os-and-framework-patching) voor uw Docker-containers automatiseren. ACR Tasks breidt niet alleen uw "inner-loop" ontwikkelingscyclus uit naar de cloud met on-demand containerimagebuilds, maar maakt ook geautomatiseerde builds mogelijk die worden geactiveerd door broncode-updates, updates van de basisafbeelding van een container of timers. Met basisimage-updatetriggers u bijvoorbeeld uw patchworkflow voor het patchen van uw besturingssysteem en toepassingsframework automatiseren, veilige omgevingen onderhouden terwijl u zich houdt aan de beginselen van onveranderlijke containers.

## <a name="task-scenarios"></a>Taakscenario's

ACR Tasks ondersteunt verschillende scenario's voor het maken en onderhouden van containerafbeeldingen en andere artefacten. Zie de volgende secties in dit artikel voor meer informatie.

* **[Snelle taak](#quick-task)** - Bouw en duw één containerafbeelding op aanvraag in Azure naar een containerregister, zonder dat u een lokale Docker Engine-installatie nodig hebt. Denk `docker build` `docker push` , in de cloud.
* **Automatisch geactiveerde taken** : Schakel een of meer *triggers* in om een afbeelding te maken:
  * **[Trigger bij broncode-update](#trigger-task-on-source-code-update)** 
  * **[Trigger op basisafbeeldingsupdate](#automate-os-and-framework-patching)** 
  * **[Trigger op een schema](#schedule-a-task)** 
* **[Taak in meerdere stappen](#multi-step-tasks)** : breid de build-and-push-mogelijkheden voor meerdere afbeeldingen uit met workflows in meerdere stappen, meerdere containers. 

Elke ACR-taak heeft een bijbehorende [broncodecontext](#context-locations) - de locatie van een set bronbestanden die worden gebruikt om een containerafbeelding of ander artefact te bouwen. Voorbeeldcontexten zijn een Git-repository of een lokaal bestandssysteem.

Taken kunnen ook profiteren van [runvariabelen,](container-registry-tasks-reference-yaml.md#run-variables)zodat u taakdefinities opnieuw gebruiken en tags voor afbeeldingen en artefacten standaardiseren.

## <a name="quick-task"></a>Snelle taak

De inner-loop ontwikkelingscyclus, het iteratieve proces van het schrijven van code, het bouwen en testen van uw toepassing voordat u zich verbindt tot bronbeheer, is echt het begin van container lifecycle management.

Voordat u uw eerste regel code vastlegt, kan de [snelle taakfunctie](container-registry-tutorial-quick-task.md) van ACR Tasks een geïntegreerde ontwikkelingservaring bieden door uw containerimagebuilds naar Azure te ontladen. Met snelle taken u uw geautomatiseerde builddefinities verifiëren en potentiële problemen oplossen voordat u uw code ingaat.

Met behulp `docker build` van de vertrouwde indeling neemt de opdracht [az acr build][az-acr-build] in de Azure CLI een [context](#context-locations) (de set bestanden die moeten worden gebouwd), stuurt deze ACR Tasks en duwt standaard de gebouwde afbeelding naar het register na voltooiing.

Zie voor een inleiding de snelle start voor [het maken en uitvoeren van een containerafbeelding](container-registry-quickstart-task-cli.md) in Azure Container Registry.  

ACR Tasks is ontworpen als een primitieve containerlevenscyclus. Integreer bijvoorbeeld ACR Tasks in uw CI/CD-oplossing. Door [az login][az-login] uit te voeren met een [service principal,][az-login-service-principal]kan je CI/CD-oplossing dan az [acr build][az-acr-build] commando's geven om image builds af te trappen.

Lees hoe u snelle taken gebruiken in de eerste zelfstudie van ACR-taken, [Containerafbeeldingen maken in de cloud met Azure Container Registry Tasks](container-registry-tutorial-quick-task.md).

> [!TIP]
> Als u een afbeelding rechtstreeks vanuit de broncode wilt bouwen en pushen, zonder Dockerfile, biedt Azure Container Registry de opdracht [az acr pack build][az-acr-pack-build] (preview). Deze tool bouwt en duwt een afbeelding van de broncode van toepassingen met behulp van [Cloud Native Buildpacks.](https://buildpacks.io/)

## <a name="trigger-task-on-source-code-update"></a>Taak activeren bij broncode-update

Activeer een containerimagebuild of multi-step taak wanneer code is vastgelegd of een pull-aanvraag wordt gedaan of bijgewerkt, naar een openbare of private Git-opslagplaats in GitHub of Azure DevOps. Configureer bijvoorbeeld een buildtaak met de [opdracht Az ACR-taak van][az-acr-task-create] Azure CLI door een Git-repository op te geven en optioneel een branch en Dockerfile. Wanneer uw team code bijwerkt in de opslagplaats, activeert een webhook die door ACR Tasks is gemaakt, een build van de containerafbeelding die in de repo is gedefinieerd. 

ACR-taken ondersteunt de volgende triggers wanneer u een Git repo instelt als context van de taak:

| Trigger | Standaard ingeschakeld |
| ------- | ------------------ |
| Doorvoeren | Ja |
| Pull-aanvraag | Nee |

Als u een trigger voor de broncodeupdate wilt configureren, moet u de taak een persoonlijk toegangstoken (PAT) verstrekken om de webhook in de openbare of private GitHub of Azure DevOps repo in te stellen.

> [!NOTE]
> Momenteel ondersteunt ACR Tasks geen commit- of pull-aanvraagtriggers in GitHub Enterprise-repo's.

Meer informatie over het activeren van builds op broncodecommit in de tweede zelfstudie van ACR-taken, [Containerimagebuilds automatiseren met Azure Container Registry Tasks.](container-registry-tutorial-build-task.md)

## <a name="automate-os-and-framework-patching"></a>Os- en frameworkpatching automatiseren

De kracht van ACR-taken om uw werkstroom voor containerbuild echt te verbeteren, komt voort uit de mogelijkheid om een update van een *basisafbeelding*te detecteren. Een functie van de meeste containerafbeeldingen, een basisafbeelding is een bovenliggende afbeelding waarop een of meer toepassingsafbeeldingen zijn gebaseerd. Basisafbeeldingen bevatten doorgaans het besturingssysteem en soms toepassingsframeworks. 

U een ACR-taak instellen om een afhankelijkheid van een basisafbeelding bij te houden wanneer deze een toepassingsafbeelding bouwt. Wanneer de bijgewerkte basisafbeelding naar uw register wordt gepusht of een basisafbeelding wordt bijgewerkt in een openbare repo, zoals in Docker Hub, kan ACR Tasks automatisch toepassingsafbeeldingen bouwen op basis daarvan.
Met deze automatische detectie en wederopbouw bespaart ACR Tasks u de tijd en moeite die normaal gesproken nodig is om elke toepassingsafbeelding handmatig bij te houden en bij te werken die verwijst naar uw bijgewerkte basisafbeelding.

Meer informatie over [basisimage-updatetriggers](container-registry-tasks-base-images.md) voor ACR-taken. En meer informatie over het activeren van een afbeeldingsopbouw wanneer een basisafbeelding naar een containerregister wordt gepusht in de zelfstudie [Automatiseer containerimagebuilds wanneer een basisafbeelding wordt bijgewerkt in een Azure-containerregister](container-registry-tutorial-base-image-update.md)

## <a name="schedule-a-task"></a>Een taak plannen

Plan optioneel een taak door een of meer *timertriggers* in te stellen wanneer u de taak maakt of bijwerkt. Het plannen van een taak is handig voor het uitvoeren van containerworkloads volgens een gedefinieerd schema of het uitvoeren van onderhoudsbewerkingen of tests op afbeeldingen die regelmatig naar uw register worden gepusht. Zie Een [ACR-taak uitvoeren volgens een gedefinieerd schema voor](container-registry-tasks-scheduled.md)meer informatie.

## <a name="multi-step-tasks"></a>Taken met meerdere stappen

Taken in meerdere stappen bieden stapsgewijze taakdefinitie en -uitvoering voor het maken, testen en patchen van containerafbeeldingen in de cloud. Taakstappen die zijn gedefinieerd in een [YAML-bestand](container-registry-tasks-reference-yaml.md) specificeren afzonderlijke build- en pushbewerkingen voor containerafbeeldingen of andere artefacten. Ze kunnen ook de uitvoering definiëren van een of meer containers, waarbij elke stap de container als uitvoeringsomgeving gebruikt.

U bijvoorbeeld een taak in meerdere stappen maken die het volgende automatiseert:

1. Een afbeelding van een webtoepassing maken
1. De webtoepassingscontainer uitvoeren
1. Een testafbeelding voor webtoepassingen maken
1. Voer de testcontainer voor webtoepassingen uit, die tests uitvoert met de lopende toepassingscontainer
1. Als de tests slagen, bouwt u een Helm-archiefpakket
1. Een `helm upgrade` pakket met behulp van het nieuwe helmdiagramarchief uitvoeren

Met taken in meerdere stappen u het gebouw, het uitvoeren en testen van een afbeelding splitsen in meer composable stappen, met ondersteuning voor afhankelijkheid tussen stappen. Met taken in meerdere stappen in ACR-taken hebt u meer gedetailleerde controle over werkstromen voor het maken, testen en bewerken van afbeeldingen en framework-opdrachten.

Meer informatie over taken in meerdere stappen in Taken met meerdere stappen uitvoeren in Taken [met meerdere stappen uitvoeren in ACR-taken](container-registry-tasks-multi-step.md).

## <a name="context-locations"></a>Contextlocaties

In de volgende tabel vindt u enkele voorbeelden van ondersteunde contextlocaties voor ACR-taken:

| Contextlocatie | Beschrijving | Voorbeeld |
| ---------------- | ----------- | ------- |
| Lokaal bestandssysteem | Bestanden in een map op het lokale bestandssysteem. | `/home/user/projects/myapp` |
| GitHub master branch | Bestanden binnen de hoofd -of andere standaardtak van een openbare of private GitHub-opslagplaats.  | `https://github.com/gituser/myapp-repo.git` |
| GitHub-filiaal | Specifieke tak van een openbare of private GitHub repo.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub-submap | Bestanden in een submap in een openbare of private GitHub repo. Voorbeeld toont de combinatie van een branch en submap specificatie. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| GitHub commit | Specifieke commit in een openbare of private GitHub repo. Voorbeeld toont een combinatie van een commit hash (SHA) en submap specificatie. | `https://github.com/gituser/myapp-repo.git#git-commit-hash:myfolder` |
| Submap Azure DevOps | Bestanden in een submap in een openbare of private Azure-repo. Voorbeeld toont een combinatie van branch en submap specificatie. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| Remote tarball | Bestanden in een gecomprimeerd archief op een externe webserver. | `http://remoteserver/myapp.tar.gz` |

> [!NOTE]
> Wanneer u een privé-Git-repo gebruikt als context voor een taak, moet u een persoonlijk toegangstoken (PAT) verstrekken.

## <a name="image-platforms"></a>Afbeeldingsplatforms

Standaard bouwt ACR Tasks afbeeldingen voor het Linux OS en de amd64-architectuur. Geef `--platform` de tag op om Windows-afbeeldingen of Linux-afbeeldingen voor andere architecturen te bouwen. Geef het besturingssysteem op en eventueel een ondersteunde architectuur `--platform Linux/arm`in de OS/architectuurindeling (bijvoorbeeld). Voor ARM-architecturen geeft u optioneel een variant op in `--platform Linux/arm64/v8`OS/architectuur/variantformaat (bijvoorbeeld):

| OS | Architectuur|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-output"></a>Taakuitvoer weergeven

Elke taakrun genereert logboekuitvoer die u controleren om te bepalen of de taakstappen zijn uitgevoerd. Wanneer u een taak handmatig activeert, wordt de logboekuitvoer voor de taaktaak uitgevoerd naar de console gestreamd en ook opgeslagen voor later ophalen. Wanneer een taak automatisch wordt geactiveerd, bijvoorbeeld door een broncodecommit of een basisafbeeldingsupdate, worden taaklogboeken alleen opgeslagen. Bekijk de run logs in de Azure portal of gebruik de opdracht [az acr-taaklogboeken.](/cli/azure/acr/task#az-acr-task-logs)

Meer informatie over [het weergeven en beheren van taaklogboeken.](container-registry-tasks-logs.md)

## <a name="next-steps"></a>Volgende stappen

Wanneer u klaar bent om het bouwen en onderhoud van containerafbeeldingen in de cloud te automatiseren, raadpleegt u de [zelfstudiereeks ACR Tasks.](container-registry-tutorial-quick-task.md)

Installeer eventueel de [Docker-extensie voor Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) en de [Azure-accountextensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) om te werken met uw Azure-containerregisters. Afbeeldingen naar een Azure-containerregister trekken en pushen of ACR-taken uitvoeren, allemaal binnen Visual Studio Code.

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
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
