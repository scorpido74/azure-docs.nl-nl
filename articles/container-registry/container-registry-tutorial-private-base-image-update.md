---
title: Zelfstudie - Image build activeren door een update van privébasisafbeeldingen
description: In deze zelfstudie configureert u een Azure Container Registry Task om automatisch containerimagebuilds in de cloud te activeren wanneer een basisafbeelding in een ander privé-Azure-containerregister wordt bijgewerkt.
ms.topic: tutorial
ms.date: 01/22/2020
ms.openlocfilehash: e8aae8a91288d470c801dc4d82cfa6b44369d832
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77617700"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-another-private-container-registry"></a>Zelfstudie: Automatiseer containerimagebuilds wanneer een basisafbeelding wordt bijgewerkt in een ander register van privécontainers 

ACR Tasks ondersteunt geautomatiseerde imagebuilds wanneer de basisafbeelding van een container [wordt bijgewerkt,](container-registry-tasks-base-images.md)bijvoorbeeld wanneer u het besturingssysteem of toepassingskader patcht in een van uw basisafbeeldingen. 

In deze zelfstudie leert u hoe u een ACR-taak maakt die een build in de cloud activeert wanneer de basisafbeelding van een container naar een ander Azure-containerregister wordt gepusht. U ook een zelfstudie proberen om een ACR-taak te maken die een afbeeldingsbuild activeert wanneer een basisafbeelding naar [hetzelfde Azure-containerregister](container-registry-tutorial-base-image-update.md)wordt gepusht.

In deze zelfstudie hebt u het volgende gedaan:

> [!div class="checklist"]
> * De basisafbeelding in een basisregister bouwen
> * Een taak voor het bouwen van toepassingen maken in een ander register om de basisafbeelding bij te houden 
> * De basisinstallatiekopie bijwerken om een taak voor een toepassingsinstallatiekopie bij te werken
> * De geactiveerde taak weergeven
> * De bijgewerkte toepassingsinstallatiekopie controleren

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u de Azure CLI lokaal wilt gebruiken, moet u de Azure CLI-versie **2.0.68** of hoger hebben geïnstalleerd. Voer `az --version` uit om de versie te bekijken. Als u de CLI wilt installeren of upgraden, raadpleegt u [Azure CLI installeren][azure-cli].

## <a name="prerequisites"></a>Vereisten

### <a name="complete-the-previous-tutorials"></a>De vorige zelfstudies voltooien

In deze zelfstudie wordt ervan uitgegaan dat u de stappen van de eerste twee zelfstudies in de reeks al hebt uitgevoerd:

* Azure Container Registry maken
* Voorbeeldopslagplaats fork
* Voorbeeldopslagplaats klonen
* Persoonlijk toegangstoken van GitHub maken

Als u dit nog niet hebt gedaan, voert u de volgende zelfstudies in voordat u verdergaat:

[Zelfstudie: containerinstallatiekopieën bouwen in de cloud met Azure Container Registry Tasks](container-registry-tutorial-quick-task.md)

[Builds van containerinstallatiekopieën automatiseren met Azure Container Registry Tasks](container-registry-tutorial-build-task.md)

Naast het containerregister dat is gemaakt voor de vorige zelfstudies, moet u een register maken om de basisafbeeldingen op te slaan. Als u wilt, maakt u het tweede register op een andere locatie dan het oorspronkelijke register.

### <a name="configure-the-environment"></a>De omgeving configureren

Vul deze variabelen voor de shell-omgeving in met waarden die geschikt zijn voor uw omgeving. Hoewel deze stap strikt genomen niet vereist is, vereenvoudigt u hiermee de uitvoering van meerregelige Azure CLI-opdrachten in deze zelfstudie. Als u deze omgevingsvariabelen niet vult, moet u elke waarde handmatig vervangen, waar deze ook in de voorbeeldopdrachten wordt weergegeven.

```azurecli-interactive
BASE_ACR=<base-registry-name>   # The name of your Azure container registry for base images
ACR_NAME=<registry-name>        # The name of your Azure container registry for application images
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

### <a name="base-image-update-scenario"></a>Bijwerkscenario van basisinstallatiekopieën

In deze zelfstudie wordt u door een bijwerkscenario van een basisinstallatiekopie geleid. Dit scenario weerspiegelt een ontwikkelingsworkflow voor het beheren van basisafbeeldingen in een gemeenschappelijk privécontainerregister bij het maken van toepassingsafbeeldingen in andere registers. De basisafbeeldingen kunnen algemene besturingssystemen en frameworks opgeven die door een team of zelfs gemeenschappelijke servicecomponenten worden gebruikt.

Ontwikkelaars die bijvoorbeeld toepassingsafbeeldingen in hun eigen registers ontwikkelen, hebben toegang tot een set basisafbeeldingen die in het gemeenschappelijke basisregister worden bijgehouden. Het basisregister kan zich in een andere regio bevinden of zelfs geo-gerepliceerd zijn.

Het [codevoorbeeld][code-sample] bevat twee Dockerfiles: een toepassingsinstallatiekopie en een installatiekopie als basis. In de volgende secties maakt u een ACR-taak die automatisch een build van de toepassingsafbeelding activeert wanneer een nieuwe versie van de basisafbeelding naar een ander Azure-containerregister wordt gepusht.

* [Dockerfile-app][dockerfile-app]: Een kleine Node.js-webtoepassing die een statische webpagina maakt waarop de Node.js-versie ervan wordt weergegeven. De versietekenreeks wordt gesimuleerd: deze geeft de inhoud weer van een omgevingsvariabele, `NODE_VERSION`, die is gedefinieerd in de basisinstallatiekopie.

* [Dockerfile-basis][dockerfile-base]: De installatiekopie die `Dockerfile-app` opgeeft als basis. Deze is zelf gebaseerd op een [Node][base-node]-installatiekopie en bevat de omgevingsvariabele `NODE_VERSION`.

In de volgende gedeeltes maakt u een taak, werkt u de waarde `NODE_VERSION` in het Docker-bestand van de basisinstallatiekopie bij en gebruikt u ACR Tasks om de basisinstallatiekopie te maken. Zodra de ACR-taak de nieuwe basisinstallatiekopie naar uw register pusht, wordt er automatisch een build van de toepassingsinstallatiekopie geactiveerd. Optioneel kunt u de containerinstallatiekopie van de toepassing lokaal uitvoeren om de verschillende versietekenreeksen in de ingebouwde installatiekopieën te bekijken.

In deze zelfstudie bouwt en pusht uw ACR-taak een afbeelding van de toepassingscontainer die is opgegeven in een Dockerfile. [ACR-taken](container-registry-tasks-multi-step.md)kunnen ook taken in meerdere stappen uitvoeren, met behulp van een YAML-bestand om stappen te definiëren om meerdere containers te bouwen, te pushen en optioneel te testen.

## <a name="build-the-base-image"></a>De basisinstallatiekopie bouwen

Begin met het bouwen van de basisafbeelding met een *snelle taak*van ACR Tasks , met behulp van [az acr build][az-acr-build]. Zoals beschreven in de [eerste zelfstudie](container-registry-tutorial-quick-task.md) in de reeks, wordt met dit proces niet alleen de installatiekopie gebouwd, maar wordt deze ook naar het containerregister gepusht als de build is gelukt. In dit voorbeeld wordt de afbeelding naar het basisbeeldregister gepusht.

```azurecli-interactive
az acr build --registry $BASE_ACR --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task-to-track-the-private-base-image"></a>Een taak maken om de privébasisafbeelding bij te houden

Maak vervolgens een taak in het applicatieimageregister met [az acr-taak maken,][az-acr-task-create]waardoor een [beheerde identiteit mogelijk wordt](container-registry-tasks-authentication-managed-identity.md). De beheerde identiteit wordt in latere stappen gebruikt, zodat de taak wordt geverifieerd met het basisafbeeldingsregister. 

In dit voorbeeld wordt een door het systeem toegewezen identiteit gebruikt, maar u een door de gebruiker toegewezen beheerde identiteit voor bepaalde scenario's maken en inschakelen. Zie Verificatie [voor cross-registry in een ACR-taak met een door Azure beheerde identiteit](container-registry-tasks-cross-registry-authentication.md)voor meer informatie.

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


Deze taak is vergelijkbaar met de taak die in de [vorige zelfstudie](container-registry-tutorial-build-task.md)is gemaakt. De taak geeft ACR Tasks de opdracht om een installatiekopiebuild te activeren wanneer doorvoeracties worden gepusht naar de opslagplaats die met `--context` is opgegeven. Terwijl het Dockerfile dat wordt gebruikt om de afbeelding`FROM node:9-alpine`in de vorige zelfstudie te bouwen, een afbeelding van de openbare basis ( ), geeft het Dockerfile-bestand in deze taak, [Dockerfile-app][dockerfile-app], een basisafbeelding op in het basisafbeeldingsregister:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

Deze configuratie maakt het eenvoudig om een framework patch in de basisafbeelding later in deze tutorial te simuleren.

## <a name="give-identity-pull-permissions-to-base-registry"></a>Machtigingen voor identiteitspull geven aan basisregister

Om de beheerde identiteitsmachtigingen van de taak te geven om afbeeldingen uit het basisbeeldregister te halen, moet eerst [az acr-taak worden weergegeven][az-acr-task-show] om de serviceprincipal-id van de identiteit te krijgen. Voer vervolgens [de AZ ACR-show][az-acr-show] uit om de resource-ID van het basisregister te krijgen:

```azurecli-interactive
# Get service principal ID of the task
principalID=$(az acr task show --name taskhelloworld --registry $ACR_NAME --query identity.principalId --output tsv) 

# Get resource ID of the base registry
baseregID=$(az acr show --name $BASE_ACR --query id --output tsv) 
```
 
Wijs de beheerde identiteitspull-machtigingen toe aan het register door het uitvoeren van [az-roltoewijzing:][az-role-assignment-create]

```azurecli-interactive
az role assignment create \
  --assignee $principalID \
  --scope $baseregID --role acrpull 
```

## <a name="add-target-registry-credentials-to-the-task"></a>Doelregisterreferenties toevoegen aan de taak

Voer [az acr taak referentie toe][az-acr-task-credential-add] te voegen aan referenties toe te voegen aan de taak. Geef `--use-identity [system]` de parameter door om aan te geven dat de door het systeem toegewezen beheerde identiteit van de taak toegang heeft tot de referenties.

```azurecli-interactive
az acr task credential add \
  --name taskhelloworld \
  --registry $ACR_NAME \
  --login-server $BASE_ACR.azurecr.io \
  --use-identity [system] 
```

## <a name="manually-run-the-task"></a>De taak handmatig uitvoeren

Gebruik [de AZ ACR-taak run][az-acr-task-run] om de taak handmatig te activeren en de toepassingsafbeelding te bouwen. Deze stap is nodig zodat de taak de afhankelijkheid van de toepassingsafbeelding van de basisafbeelding bijhoudt.

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Zodra de taak is voltooid, moet u de **run-id** (bijvoorbeeld 'da6') noteren als u de volgende optionele stap wilt voltooien.

### <a name="optional-run-application-container-locally"></a>Optioneel: toepassingscontainer lokaal uitvoeren

Als u lokaal werkt (niet in Cloud Shell) en Docker hebt geïnstalleerd, voert u de container uit om de toepassing te bekijken in een webbrowser voordat u de basisinstallatiekopie opnieuw bouwt. Als u Cloud Shell gebruikt, slaat u deze sectie over (Cloud Shell biedt geen ondersteuning voor `az acr login` of `docker run`).

Eerst, authenticeren aan uw container register met [az acr login:][az-acr-login]

```azurecli
az acr login --name $ACR_NAME
```

Voer de container nu lokaal uit met `docker run`. Vervang ** \<run-id\> ** door de run-id in de uitvoer van de vorige stap (bijvoorbeeld 'da6'). In dit voorbeeld `myapp` wordt `--rm` de container vermeld en wordt de parameter bevat om de container te verwijderen wanneer u deze stopt.

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

In deze zelfstudie hebt u geleerd hoe u een taak configureert om automatisch builds van containerinstallatiekopieën te activeren wanneer een basisinstallatiekopie is bijgewerkt. Ga nu naar de volgende zelfstudie om te leren hoe u taken activeren volgens een gedefinieerd schema.

> [!div class="nextstepaction"]
> [Een taak volgens een planning uitvoeren](container-registry-tasks-scheduled.md)

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
