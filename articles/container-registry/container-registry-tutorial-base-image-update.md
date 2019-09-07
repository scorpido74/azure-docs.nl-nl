---
title: 'Zelfstudie: Builds van containerinstallatiekopieën automatiseren wanneer een basisinstallatiekopie wordt bijgewerkt - Azure Container Registry-taken'
description: In deze zelfstudie leert u hoe u een Azure Container Registry-taak configureert om builds van containerinstallatiekopieën automatisch te activeren in de cloud wanneer een basisinstallatiekopie wordt bijgewerkt.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 6b9a74ee6530d8fc195490b0f1414e6348e855f6
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743591"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-an-azure-container-registry"></a>Zelfstudie: Builds van containerinstallatiekopieën automatiseren wanneer een basisinstallatiekopie wordt bijgewerkt in een Azure-containerregister 

ACR Tasks ondersteunt de uitvoering van geautomatiseerde builds wanneer de basisinstallatiekopie van een container wordt bijgewerkt, bijvoorbeeld wanneer u het besturingssysteem of toepassingsframework patcht in een van uw basisinstallatiekopieën. In deze zelfstudie leert u hoe u in ACR Tasks een taak maakt waarmee een build in de cloud wordt geactiveerd wanneer de basisinstallatiekopie van een container naar uw register is gepusht.

Deze laatste zelfstudie in de serie wordt het volgende behandeld:

> [!div class="checklist"]
> * De basisinstallatiekopie bouwen
> * Een build-taak voor de toepassingsinstallatiekopie maken
> * De basisinstallatiekopie bijwerken om een taak voor een toepassingsinstallatiekopie bij te werken
> * De geactiveerde taak weergeven
> * De bijgewerkte toepassingsinstallatiekopie controleren

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u Azure CLI lokaal wilt gebruiken, moet u ervoor zorgen dat versie **2.0.46** of hoger van Azure CLI is geïnstalleerd. Voer `az --version` uit om de versie te bekijken. Als u de CLI wilt installeren of upgraden, raadpleegt u [Azure cli installeren][azure-cli].

## <a name="prerequisites"></a>Vereisten

### <a name="complete-the-previous-tutorials"></a>De vorige zelfstudies voltooien

In deze zelfstudie wordt ervan uitgegaan dat u de stappen van de eerste twee zelfstudies in de serie al hebt uitgevoerd:

* Azure Container Registry maken
* Voorbeeldopslagplaats splitsen
* Voorbeeldopslagplaats klonen
* Persoonlijk toegangstoken van GitHub maken

Als u de eerste twee zelfstudies nog niet hebt afgerond, moet u dit doen voordat u verdergaat:

[Zelfstudie: containerinstallatiekopieën bouwen in de cloud met Azure Container Registry Tasks](container-registry-tutorial-quick-task.md)

[Builds van containerinstallatiekopieën automatiseren met Azure Container Registry Tasks](container-registry-tutorial-build-task.md)

### <a name="configure-the-environment"></a>De omgeving configureren

Vul deze variabelen voor de shell-omgeving in met waarden die geschikt zijn voor uw omgeving. Hoewel deze stap strikt genomen niet vereist is, vereenvoudigt u hiermee de uitvoering van meerregelige Azure CLI-opdrachten in deze zelfstudie. Als u deze omgevingsvariabelen niet invult, moet u elke bijbehorende waarde handmatig vervangen wanneer deze wordt weergegeven in de voorbeeldopdrachten.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

## <a name="base-images"></a>Basisinstallatiekopieën

Dockerfiles waarmee de meeste containerinstallatiekopieën worden gedefinieerd, geven een bovenliggende installatiekopie aan waarop de containerinstallatiekopie is gebaseerd. Deze bovenliggende installatiekopie wordt vaak aangeduid als de *basisinstallatiekopie*. Basis installatie kopieën bevatten doorgaans het besturings systeem, bijvoorbeeld [Alpine Linux][base-alpine] of [Windows nano server][base-windows], waarop de rest van de lagen van de container wordt toegepast. Ze kunnen ook toepassings raamwerken zoals [node. js][base-node] of [.net core][base-dotnet]omvatten.

### <a name="base-image-updates"></a>Updates van basisinstallatiekopieën

Een basisinstallatiekopie wordt vaak door de installatiekopie-maintainer bijgewerkt om nieuwe functies of verbeteringen toe te voegen aan het besturingssysteem of framework in de installatiekopie. Beveiligingspatches zijn een andere veelvoorkomende oorzaak van een update van de basisinstallatiekopie.

Wanneer een basisinstallatiekopie wordt bijgewerkt, is het noodzakelijk dat u op basis hiervan de containerinstallatiekopieën in uw register opnieuw bouwt om de nieuwe functies en oplossingen op te nemen. Met ACR Tasks hebt u de mogelijkheid om automatisch installatiekopieën te maken wanneer een containerinstallatiekopie wordt bijgewerkt.

### <a name="tasks-triggered-by-a-base-image-update"></a>Taken die worden geactiveerd door een basis installatie kopie-update

* Voor installatie kopieën die zijn gebaseerd op een Dockerfile, detecteert een ACR-taak afhankelijkheden op basis installatie kopieën op de volgende locaties:

  * Hetzelfde Azure container Registry waarin de taak wordt uitgevoerd
  * Een ander Azure container registry in dezelfde regio 
  * Een open bare opslag plaats in docker hub 
  * Een open bare opslag plaats in micro soft Container Registry

   Als de basis installatie kopie die in `FROM` de instructie is opgegeven, zich op een van deze locaties bevindt, voegt de ACR-taak een hook toe om ervoor te zorgen dat de installatie kopie telkens opnieuw wordt opgebouwd wanneer de basis wordt bijgewerkt.

* Op dit moment worden met een ACR-taak alleen basis installatie kopieën van updates voor de toepassing (*runtime*) bijgehouden. Updates voor tussenliggende installatie kopieën (*buildtime*) die worden gebruikt in de multi-fase Dockerfiles, worden niet bijgehouden.  

* Wanneer u een ACR-taak maakt met de opdracht [AZ ACR Task Create][az-acr-task-create] , wordt de taak standaard *ingeschakeld* voor trigger door een update van de basis installatie kopie. Dat wil zeggen, `base-image-trigger-enabled` de eigenschap is ingesteld op True. Als u dit gedrag in een taak wilt uitschakelen, werkt u de eigenschap bij op ONWAAR. Voer bijvoorbeeld de volgende opdracht [AZ ACR Task update][az-acr-task-update] uit:

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* Als u een ACR-taak wilt inschakelen om de afhankelijkheden van een container installatie kopie te bepalen en bij te houden, zoals de basis installatie kopie, moet u de taak eerst **ten minste één keer**activeren. Activeer de taak bijvoorbeeld hand matig met behulp van de opdracht [AZ ACR Task run][az-acr-task-run] .

* Voor het activeren van een taak bij het bijwerken van de basis installatie kopie moet de basis installatie kopie een `node:9-alpine`stabiele tag hebben, zoals. Deze code ring is gebruikelijk voor een basis installatie kopie die wordt bijgewerkt met OS-en Framework patches naar een laatste stabiele release. Als de basis installatie kopie wordt bijgewerkt met een nieuwe versie label, wordt er geen taak geactiveerd. Zie de [Best practices-richt lijnen](container-registry-image-tag-version.md)voor meer informatie over afbeeldings codes. 

### <a name="base-image-update-scenario"></a>Bijwerkscenario van basisinstallatiekopieën

In deze zelfstudie wordt u door een bijwerkscenario van een basisinstallatiekopie geleid. Het [code voorbeeld][code-sample] bevat twee Dockerfiles: een toepassings installatie kopie en een installatie kopie die wordt opgegeven als basis. In de volgende secties maakt u een ACR-taak die automatisch een build van de toepassings installatie kopie activeert wanneer een nieuwe versie van de basis installatie kopie naar hetzelfde container register wordt gepusht.

[Dockerfile-app][dockerfile-app]: Een kleine Node.js-webtoepassing die een statische webpagina maakt waarop de Node.js-versie ervan wordt weergegeven. De versietekenreeks wordt gesimuleerd: deze geeft de inhoud weer van een omgevingsvariabele, `NODE_VERSION`, die is gedefinieerd in de basisinstallatiekopie.

[Dockerfile-basis][dockerfile-base]: De installatiekopie die `Dockerfile-app` opgeeft als basis. Het is zelf gebaseerd op een [knooppunt][base-node] installatie kopie en bevat de `NODE_VERSION` omgevings variabele.

In de volgende gedeeltes maakt u een taak, werkt u de waarde `NODE_VERSION` in het Docker-bestand van de basisinstallatiekopie bij en gebruikt u ACR Tasks om de basisinstallatiekopie te maken. Zodra de ACR-taak de nieuwe basisinstallatiekopie naar uw register pusht, wordt er automatisch een build van de toepassingsinstallatiekopie geactiveerd. Optioneel kunt u de containerinstallatiekopie van de toepassing lokaal uitvoeren om de verschillende versietekenreeksen in de ingebouwde installatiekopieën te bekijken.

In deze zelf studie bouwt en pusht uw ACR-taak een toepassings container installatie kopie die is opgegeven in een Dockerfile. ACR-taken kunnen ook [taken met meerdere stappen](container-registry-tasks-multi-step.md)uitvoeren, met behulp van een yaml-bestand om de stappen voor het bouwen, pushen en optioneel testen van meerdere containers te definiëren.

## <a name="build-the-base-image"></a>De basisinstallatiekopie bouwen

Start met het bouwen van de basisinstallatiekopie met een *snelle taak* van ACR Tasks. Zoals beschreven in de [eerste zelfstudie](container-registry-tutorial-quick-task.md) in de reeks, wordt met dit proces niet alleen de installatiekopie gebouwd, maar wordt deze ook naar het containerregister gepusht als de build is gelukt.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task"></a>Een taak maken

Maak vervolgens een taak met [AZ ACR Task Create][az-acr-task-create]:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> Als u eerder taken hebt gemaakt tijdens het voor beeld `az acr build-task` met de opdracht, moeten deze taken opnieuw worden gemaakt met de opdracht [AZ ACR Task][az-acr-task] .

Deze taak is vergelijkbaar met de snelle taak die u hebt gemaakt in de [vorige zelfstudie](container-registry-tutorial-build-task.md). De taak geeft ACR Tasks de opdracht om een installatiekopiebuild te activeren wanneer doorvoeracties worden gepusht naar de opslagplaats die met `--context` is opgegeven. De Dockerfile die wordt gebruikt om de installatie kopie op te bouwen in de vorige zelf studie,`FROM node:9-alpine`geeft een open bare basis installatie kopie () op, de Dockerfile in deze taak, [Dockerfile-app][dockerfile-app], geeft een basis installatie kopie op in hetzelfde REGI ster:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

Met deze configuratie kunt u eenvoudig een framework-patch in de basis installatie kopie simuleren verderop in deze zelf studie.

## <a name="build-the-application-container"></a>De toepassingscontainer bouwen

Gebruik [AZ ACR Task run][az-acr-task-run] om de taak hand matig te activeren en de installatie kopie van de toepassing samen te stellen. Met deze stap zorgt u ervoor dat de taak de afhankelijkheid van de toepassings installatie kopie op de basis installatie kopie bijhoudt.

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Zodra de taak is voltooid, moet u de **run-id** (bijvoorbeeld 'da6') noteren als u de volgende optionele stap wilt voltooien.

### <a name="optional-run-application-container-locally"></a>Optioneel: Toepassingscontainer lokaal uitvoeren

Als u lokaal werkt (niet in Cloud Shell) en Docker hebt geïnstalleerd, voert u de container uit om de toepassing te bekijken in een webbrowser voordat u de basisinstallatiekopie opnieuw bouwt. Als u Cloud Shell gebruikt, slaat u deze sectie over (Cloud Shell biedt geen ondersteuning voor `az acr login` of `docker run`).

Verifieer eerst uw container register met [AZ ACR login][az-acr-login]:

```azurecli
az acr login --name $ACR_NAME
```

Voer de container nu lokaal uit met `docker run`. Vervang **\<run-id\>** door de gevonden run-id in de uitvoer van de vorige stap (bijvoorbeeld 'da6'). In dit voor beeld wordt `myapp` de container een `--rm` naam en bevat de para meter voor het verwijderen van de container wanneer u deze stopt.

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

Vervolgens wordt een lijst met de taak uitgevoerd die ACR taken uitvoert voor uw REGI ster met de opdracht [AZ ACR Task List-runs][az-acr-task-list-runs] :

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

Hier kunt u een frameworkpatch in de basisinstallatiekopie simuleren. Bewerk de **Dockerfile-basis**  en voeg een 'a' toe achter het versienummer dat is gedefinieerd in `NODE_VERSION`:

```Dockerfile
ENV NODE_VERSION 9.11.2a
```

Voer een snelle taak uit om de gewijzigde basisinstallatiekopie te bouwen. Noteer de waarde van **run-id** in de uitvoer.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
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

### <a name="optional-run-newly-built-image"></a>Optioneel: Nieuw gebouwde installatiekopie uitvoeren

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

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle resources wilt verwijderen die u in deze zelfstudiereeks hebt gemaakt, met inbegrip van het containerregister, containerexemplaar, sleutelkluis en service-principal, gebruikt u de volgende opdrachten:

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een taak configureert om automatisch builds van containerinstallatiekopieën te activeren wanneer een basisinstallatiekopie is bijgewerkt. Ga nu verder naar meer informatie over verificatie voor uw containerregister.

> [!div class="nextstepaction"]
> [Verificatie in Azure Container Registry](container-registry-authentication.md)

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
[az-acr-build]: /cli/azure/acr#az-acr-build-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr
[az-acr-task]: /cli/azure/acr

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png
