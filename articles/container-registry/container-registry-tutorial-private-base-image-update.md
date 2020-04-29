---
title: Zelf studie-installatie kopie activeren door de installatie kopie van de persoonlijke basis-update
description: In deze zelf studie configureert u een Azure Container Registry taak voor het automatisch activeren van container installatie kopieën in de Cloud wanneer een basis installatie kopie in een ander persoonlijk Azure container Registry wordt bijgewerkt.
ms.topic: tutorial
ms.date: 01/22/2020
ms.openlocfilehash: e8aae8a91288d470c801dc4d82cfa6b44369d832
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77617700"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-another-private-container-registry"></a>Zelf studie: builds van container installatie kopieën automatiseren wanneer een basis installatie kopie wordt bijgewerkt in een ander persoonlijk container register 

ACR-taken bieden ondersteuning voor geautomatiseerde installatie kopieën wanneer de [basis installatie kopie](container-registry-tasks-base-images.md)van een container wordt bijgewerkt, bijvoorbeeld wanneer u het besturings systeem of toepassings raamwerk bijwerkt in een van de basis installatie kopieën. 

In deze zelf studie leert u hoe u een ACR-taak maakt waarmee een build in de Cloud wordt geactiveerd wanneer de basis installatie kopie van een container naar een ander Azure container Registry wordt gepusht. U kunt ook een zelf studie voor het maken van een ACR-taak waarmee een installatie kopie wordt gemaakt, wordt geactiveerd wanneer een basis installatie kopie naar [hetzelfde Azure container Registry](container-registry-tutorial-base-image-update.md)wordt gepusht.

In deze zelfstudie hebt u het volgende gedaan:

> [!div class="checklist"]
> * De basis installatie kopie bouwen in een basis register
> * Een taak voor het bouwen van een toepassing maken in een ander REGI ster om de basis installatie kopie bij te houden 
> * De basisinstallatiekopie bijwerken om een taak voor een toepassingsinstallatiekopie bij te werken
> * De geactiveerde taak weergeven
> * De bijgewerkte toepassingsinstallatiekopie controleren

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u de Azure CLI lokaal wilt gebruiken, moet de Azure CLI-versie **2.0.68** of hoger zijn geïnstalleerd. Voer `az --version` uit om de versie te bekijken. Als u de CLI wilt installeren of upgraden, raadpleegt u [Azure CLI installeren][azure-cli].

## <a name="prerequisites"></a>Vereisten

### <a name="complete-the-previous-tutorials"></a>De vorige zelfstudies voltooien

In deze zelfstudie wordt ervan uitgegaan dat u de stappen van de eerste twee zelfstudies in de reeks al hebt uitgevoerd:

* Azure Container Registry maken
* Voorbeeldopslagplaats fork
* Voorbeeldopslagplaats klonen
* Persoonlijk toegangstoken van GitHub maken

Als u dit nog niet hebt gedaan, voltooit u de volgende zelf studies voordat u doorgaat:

[Zelfstudie: containerinstallatiekopieën bouwen in de cloud met Azure Container Registry Tasks](container-registry-tutorial-quick-task.md)

[Builds van containerinstallatiekopieën automatiseren met Azure Container Registry Tasks](container-registry-tutorial-build-task.md)

Naast het container register dat is gemaakt voor de voor gaande zelf studies, moet u een REGI ster maken om de basis installatie kopieën op te slaan. Als u wilt, maakt u het tweede REGI ster op een andere locatie dan het oorspronkelijke REGI ster.

### <a name="configure-the-environment"></a>De omgeving configureren

Vul deze variabelen voor de shell-omgeving in met waarden die geschikt zijn voor uw omgeving. Hoewel deze stap strikt genomen niet vereist is, vereenvoudigt u hiermee de uitvoering van meerregelige Azure CLI-opdrachten in deze zelfstudie. Als u deze omgevings variabelen niet opgeeft, moet u elke waarde hand matig vervangen, overal waar deze in de voorbeeld opdrachten worden weer gegeven.

```azurecli-interactive
BASE_ACR=<base-registry-name>   # The name of your Azure container registry for base images
ACR_NAME=<registry-name>        # The name of your Azure container registry for application images
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

### <a name="base-image-update-scenario"></a>Bijwerkscenario van basisinstallatiekopieën

In deze zelfstudie wordt u door een bijwerkscenario van een basisinstallatiekopie geleid. Dit scenario bevat een ontwikkel werk stroom voor het beheren van basis installatie kopieën in een gemeen schappelijk container register met persoonlijke vermeldingen bij het maken van toepassings installatie kopieën in andere registers. De basis installatie kopieën kunnen algemene besturings systemen en frameworks opgeven die worden gebruikt door een team of zelfs algemene service onderdelen.

Ontwikkel aars die bijvoorbeeld installatie kopieën van toepassingen ontwikkelen in hun eigen registers, kunnen toegang krijgen tot een set basis installatie kopieën die worden onderhouden in het algemene basis register. Het basis register kan zich in een andere regio bevinden of zelfs met geo-replicatie.

Het [codevoorbeeld][code-sample] bevat twee Dockerfiles: een toepassingsinstallatiekopie en een installatiekopie als basis. In de volgende secties maakt u een ACR-taak die automatisch een build van de toepassings installatie kopie activeert wanneer een nieuwe versie van de basis installatie kopie naar een ander Azure container Registry wordt gepusht.

* [Dockerfile-app][dockerfile-app]: Een kleine Node.js-webtoepassing die een statische webpagina maakt waarop de Node.js-versie ervan wordt weergegeven. De versietekenreeks wordt gesimuleerd: deze geeft de inhoud weer van een omgevingsvariabele, `NODE_VERSION`, die is gedefinieerd in de basisinstallatiekopie.

* [Dockerfile-basis][dockerfile-base]: De installatiekopie die `Dockerfile-app` opgeeft als basis. Deze is zelf gebaseerd op een [Node][base-node]-installatiekopie en bevat de omgevingsvariabele `NODE_VERSION`.

In de volgende gedeeltes maakt u een taak, werkt u de waarde `NODE_VERSION` in het Docker-bestand van de basisinstallatiekopie bij en gebruikt u ACR Tasks om de basisinstallatiekopie te maken. Zodra de ACR-taak de nieuwe basisinstallatiekopie naar uw register pusht, wordt er automatisch een build van de toepassingsinstallatiekopie geactiveerd. Optioneel kunt u de containerinstallatiekopie van de toepassing lokaal uitvoeren om de verschillende versietekenreeksen in de ingebouwde installatiekopieën te bekijken.

In deze zelf studie bouwt en pusht uw ACR-taak een toepassings container installatie kopie die is opgegeven in een Dockerfile. ACR-taken kunnen ook [taken met meerdere stappen](container-registry-tasks-multi-step.md)uitvoeren, met behulp van een yaml-bestand om de stappen voor het bouwen, pushen en optioneel testen van meerdere containers te definiëren.

## <a name="build-the-base-image"></a>De basisinstallatiekopie bouwen

Begin met het bouwen van de basis installatie kopie met een *snelle taak*voor ACR-taken met behulp van [AZ ACR build][az-acr-build]. Zoals beschreven in de [eerste zelfstudie](container-registry-tutorial-quick-task.md) in de reeks, wordt met dit proces niet alleen de installatiekopie gebouwd, maar wordt deze ook naar het containerregister gepusht als de build is gelukt. In dit voor beeld wordt de installatie kopie naar het REGI ster van de basis installatie kopie gepusht.

```azurecli-interactive
az acr build --registry $BASE_ACR --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task-to-track-the-private-base-image"></a>Een taak maken om de persoonlijke basis installatie kopie bij te houden

Maak vervolgens een taak in het REGI ster voor toepassings installatie kopieën met [AZ ACR Task Create][az-acr-task-create], waardoor een [beheerde identiteit](container-registry-tasks-authentication-managed-identity.md)wordt ingeschakeld. De beheerde identiteit wordt in latere stappen gebruikt, zodat de taak wordt geverifieerd met het REGI ster van de basis installatie kopie. 

In dit voor beeld wordt een door het systeem toegewezen identiteit gebruikt, maar u kunt een door de gebruiker toegewezen beheerde identiteit maken en inschakelen voor bepaalde scenario's. Zie [verificatie tussen het REGI ster in een ACR-taak met behulp van een door Azure beheerde identiteit](container-registry-tasks-cross-registry-authentication.md)voor meer informatie.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT \
    --arg REGISTRY_NAME=$BASE_ACR.azurecr.io \
    --assign-identity
```


Deze taak is vergelijkbaar met de taak die in de [vorige zelf studie](container-registry-tutorial-build-task.md)is gemaakt. De taak geeft ACR Tasks de opdracht om een installatiekopiebuild te activeren wanneer doorvoeracties worden gepusht naar de opslagplaats die met `--context` is opgegeven. De Dockerfile die wordt gebruikt om de installatie kopie op te bouwen in de vorige zelf studie,`FROM node:9-alpine`geeft een open bare basis installatie kopie () op, de Dockerfile in deze taak, [Dockerfile-app][dockerfile-app], geeft een basis installatie kopie op in het REGI ster basis installatie kopie:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

Met deze configuratie kunt u eenvoudig een framework-patch in de basis installatie kopie simuleren verderop in deze zelf studie.

## <a name="give-identity-pull-permissions-to-base-registry"></a>Machtigingen voor het verzamelen van identiteiten aan het basis register geven

Als u de beheerde identiteits machtigingen van de taak wilt gebruiken voor het ophalen van installatie kopieën uit het REGI ster van de basis installatie kopie, voert u eerst [AZ ACR Task show][az-acr-task-show] uit om de Service-Principal-id van de identiteit te verkrijgen. Voer [AZ ACR show][az-acr-show] uit om de resource-id van het basis register op te halen:

```azurecli-interactive
# Get service principal ID of the task
principalID=$(az acr task show --name taskhelloworld --registry $ACR_NAME --query identity.principalId --output tsv) 

# Get resource ID of the base registry
baseregID=$(az acr show --name $BASE_ACR --query id --output tsv) 
```
 
Wijs de pull-machtigingen voor beheerde identiteiten toe aan het REGI ster door de [opdracht AZ Role Assignment maken][az-role-assignment-create]uit te voeren:

```azurecli-interactive
az role assignment create \
  --assignee $principalID \
  --scope $baseregID --role acrpull 
```

## <a name="add-target-registry-credentials-to-the-task"></a>De referenties voor het doel register toevoegen aan de taak

Voer [AZ ACR taak referentie add][az-acr-task-credential-add] uit om referenties toe te voegen aan de taak. Geef de `--use-identity [system]` para meter door om aan te geven dat de door het systeem toegewezen beheerde identiteit van de taak toegang kan krijgen tot de referenties.

```azurecli-interactive
az acr task credential add \
  --name taskhelloworld \
  --registry $ACR_NAME \
  --login-server $BASE_ACR.azurecr.io \
  --use-identity [system] 
```

## <a name="manually-run-the-task"></a>De taak hand matig uitvoeren

Gebruik [AZ ACR Task run][az-acr-task-run] om de taak hand matig te activeren en de installatie kopie van de toepassing samen te stellen. Deze stap is nodig om ervoor te zorgen dat de taak de afhankelijkheid van de toepassings installatie kopie op de basis installatie kopie bijhoudt.

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Zodra de taak is voltooid, moet u de **run-id** (bijvoorbeeld 'da6') noteren als u de volgende optionele stap wilt voltooien.

### <a name="optional-run-application-container-locally"></a>Optioneel: toepassingscontainer lokaal uitvoeren

Als u lokaal werkt (niet in Cloud Shell) en Docker hebt geïnstalleerd, voert u de container uit om de toepassing te bekijken in een webbrowser voordat u de basisinstallatiekopie opnieuw bouwt. Als u Cloud Shell gebruikt, slaat u deze sectie over (Cloud Shell biedt geen ondersteuning voor `az acr login` of `docker run`).

Verifieer eerst uw container register met [AZ ACR login][az-acr-login]:

```azurecli
az acr login --name $ACR_NAME
```

Voer de container nu lokaal uit met `docker run`. Vervang ** \<run-id\> ** door de run-id die is gevonden in de uitvoer van de vorige stap (bijvoorbeeld "da6"). In dit voor beeld wordt `myapp` de container een `--rm` naam en bevat de para meter voor het verwijderen van de container wanneer u deze stopt.

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Ga naar `http://localhost:8080` in uw browser. U ziet nu het weergegeven nummer van de Node.js-versie op de webpagina, zoals hieronder weergegeven. In een latere stap verhoogt u de versie door een 'a' toe te voegen aan de versietekenreeks.

![Schermafbeelding van de voorbeeldtoepassing die wordt weergegeven in de browser][base-update-01]

Voer de volgende opdracht uit om de container te stoppen en te verwijderen:

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>De builds weergeven

Geef vervolgens een overzicht weer van de taken die ACR Tasks heeft voltooid voor het register door de opdracht [az acr task list-runs][az-acr-task-list-runs] te gebruiken:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Als u de vorige zelfstudie hebt voltooid (en het register niet hebt verwijderd), ziet u de uitvoer zoals hieronder weergegeven. Noteer het aantal taak-runs en de meest recente run-id, zodat u de uitvoer kunt vergelijken nadat u in het volgende gedeelte de basisinstallatiekopie hebt bijgewerkt.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK            PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da6       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual      2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="update-the-base-image"></a>De basisinstallatiekopie bijwerken

Hier kunt u een frameworkpatch in de basisinstallatiekopie simuleren. Bewerk de **Dockerfile-basis ** en voeg een 'a' toe achter het versienummer dat is gedefinieerd in `NODE_VERSION`:

```Dockerfile
ENV NODE_VERSION 9.11.2a
```

Voer een snelle taak uit om de gewijzigde basisinstallatiekopie te bouwen. Noteer de waarde van **run-id** in de uitvoer.

```azurecli-interactive
az acr build --registry $BASE_ACR --image baseimages/node:9-alpine --file Dockerfile-base .
```

Zodra de build is voltooid en de ACR-taak de nieuwe basisinstallatiekopie naar uw register heeft gepusht, wordt automatisch een build van de toepassingsinstallatiekopie geactiveerd. Het kan enkele ogenblikken duren voordat de toepassingsinstallatiekopie wordt geactiveerd door de taak die u eerder hebt gemaakt, omdat met de taak de pas gemaakte en gepushte basisinstallatiekopie moet worden gedetecteerd.

## <a name="list-updated-build"></a>Bijgewerkte build weergeven

Nu u de basisinstallatiekopie hebt bijgewerkt, geeft u opnieuw een lijst van uw taken weer om deze te vergelijken met de vorige lijst. Als de uitvoer in eerste instantie niet verschilt, voert u regelmatig de opdracht uit tot de nieuwe taak-run in de lijst wordt weergegeven.

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

De uitvoer lijkt op het volgende. De TRIGGER voor de als laatste uitgevoerde build moet Update van installatiekopie zijn. Hiermee wordt aangegeven dat de taak is gestart door uw snelle taak van de basisinstallatiekopie.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

Run ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
da8       taskhelloworld  Linux       Succeeded  Image Update  2018-09-17T23:11:50Z  00:00:33
da7                       Linux       Succeeded  Manual        2018-09-17T23:11:27Z  00:00:35
da6       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual        2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit    2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual        2018-09-17T22:29:59Z  00:00:57
```

Als u de volgende optionele stap van de nieuw gebouwde container wilt uitvoeren om het bijgewerkte versienummer te zien, noteert u de **RUN ID**-waarde voor het bijwerken van de door de installatiekopie getriggerde build (in de vorige uitvoer is dit 'da8').

### <a name="optional-run-newly-built-image"></a>Optioneel: nieuw gebouwde installatiekopie uitvoeren

Als u lokaal werkt (niet in Cloud Shell) en u hebt Docker geïnstalleerd, voert u de nieuwe toepassingsinstallatiekopie uit zodra de build is voltooid. Vervang `<run-id>` door de run-id uit de vorige stap. Als u Cloud Shell gebruikt, slaat u deze sectie over (Cloud Shell biedt geen ondersteuning voor `docker run`).

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Ga naar http://localhost:8081 in uw browser. U ziet nu het bijgewerkte nummer van de Node.js-versie (met de 'a') op de webpagina:

![Schermafbeelding van de voorbeeldtoepassing die wordt weergegeven in de browser][base-update-02]

U hebt uw **basis**installatiekopie bijgewerkt met een nieuw versienummer, maar de **toepassings**installatiekopie die als laatste is gebouwd, geeft nieuwe versie weer. ACR Tasks heeft uw wijziging van de basisinstallatiekopie herkend en heeft de toepassingsinstallatiekopie automatisch opnieuw opgebouwd.

Voer de volgende opdracht uit om de container te stoppen en te verwijderen:

```bash
docker stop updatedapp
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een taak configureert om automatisch builds van containerinstallatiekopieën te activeren wanneer een basisinstallatiekopie is bijgewerkt. Ga nu verder met de volgende zelf studie voor meer informatie over het activeren van taken volgens een gedefinieerd schema.

> [!div class="nextstepaction"]
> [Een taak uitvoeren volgens een schema](container-registry-tasks-scheduled.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task]: /cli/azure/acr#az-acr-task
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png
