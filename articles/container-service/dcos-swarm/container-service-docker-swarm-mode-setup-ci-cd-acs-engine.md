---
title: KEUR CI/CD met Azure Container Service-engine en Swarm-modus
description: Gebruik Azure Container Service-engine met docker Swarm-modus, een Azure Container Registry en Azure DevOps om voortdurend een .NET core-toepassing met meerdere containers te leveren
author: diegomrtnzg
ms.service: container-service
ms.topic: conceptual
ms.date: 05/27/2017
ms.author: dimart
ms.custom: mvc
ms.openlocfilehash: 1ec7ece6f5afd1bbd2613ae08af04b82e8a156b2
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277920"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-azure-devops"></a>KEUR Volledige CI/CD-pijp lijn voor het implementeren van een toepassing met meerdere containers op Azure Container Service met de ACS-engine en de docker Swarm-modus met behulp van Azure DevOps

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

*Dit artikel is gebaseerd op de [volledige CI/cd-pijp lijn voor het implementeren van een toepassing met meerdere containers op Azure container service met docker Swarm met behulp van Azure DevOps](container-service-docker-swarm-setup-ci-cd.md) -documentatie*

Tegenwoordig, een van de grootste uitdagingen bij het ontwikkelen van moderne toepassingen voor de Cloud is dat deze toepassingen continu kunnen worden geleverd. In dit artikel leert u hoe u een volledige, continue integratie-en implementatie-pijp lijn (CI/CD) kunt implementeren met behulp van: 
* Azure Container Service-engine met docker Swarm-modus
* Azure Container Registry
* Azure DevOps


![Voorbeeld toepassing MyShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

Het doel is om deze toepassing voortdurend te leveren in een docker Swarm modus-cluster met behulp van Azure DevOps. In de volgende afbeelding vindt u meer informatie over deze continue bezorgings pijplijn:

![Voorbeeld toepassing MyShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Hier volgt een korte uitleg van de stappen:

1. Code wijzigingen worden doorgevoerd in de opslag plaats van de bron code (hier, GitHub) 
2. GitHub activeert een build in azure DevOps 
3. Azure DevOps haalt de nieuwste versie van de bronnen op en bouwt alle installatie kopieën op waaruit de toepassing is samengesteld. 
4. Azure DevOps duwt elke installatie kopie naar een docker-REGI ster dat is gemaakt met behulp van de Azure Container Registry-service 
5. Azure DevOps activeert een nieuwe release 
6. De release voert enkele opdrachten uit met SSH op het hoofd knooppunt van het Azure container service-cluster 
7. De docker Swarm-modus op het cluster haalt de meest recente versie van de installatie kopieën op 
8. De nieuwe versie van de toepassing wordt geïmplementeerd met behulp van docker-stack 

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelf studie begint, moet u de volgende taken uitvoeren:

- [Een Swarm-modus cluster maken in Azure Container Service met de ACS-engine](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Verbinding maken met het Swarm-cluster in Azure Container Service](../container-service-connect.md)
- [Een Azure container Registry maken](../../container-registry/container-registry-get-started-portal.md)
- [Een Azure DevOps-organisatie en-project hebben gemaakt](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [De GitHub-opslag plaats splitsen in uw GitHub-account](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> De Docker Swarm-orchestrator in Azure Container Service maakt gebruik van legacy standalone Swarm. Momenteel is de geïntegreerde [Swarm-modus](https://docs.docker.com/engine/swarm/) (in Docker 1.12 en hoger) geen ondersteunde orchestrator in Azure Container Service. Daarom gebruiken we de [ACS-engine](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), een community-bijgedragen Quick Start- [sjabloon](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/)of een docker-oplossing in [Azure Marketplace](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-azure-devops-organization"></a>Stap 1: uw Azure DevOps-organisatie configureren 

In deze sectie configureert u uw Azure DevOps-organisatie. Als u Azure DevOps Services-eind punten wilt configureren, klikt u in uw Azure DevOps-project op het pictogram **instellingen** op de werk balk en selecteert u **Services**.

![Service-eind punt openen](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-azure-devops-and-azure-account"></a>Verbinding maken met Azure DevOps en Azure-account

Stel een verbinding in tussen uw Azure DevOps-project en uw Azure-account.

1. Klik aan de linkerkant op **nieuw service-eind punt** > **Azure Resource Manager**.
2. Als u Azure DevOps wilt autoriseren om te werken met uw Azure-account, selecteert u uw **abonnement** en klikt u op **OK**.

    ![Azure DevOps-Azure autoriseren](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-azure-devops-and-github"></a>Verbinding maken met Azure DevOps en GitHub

Stel een verbinding in tussen uw Azure DevOps-project en uw GitHub-account.

1. Klik aan de linkerkant op **nieuw service-eind punt** > **github**.
2. Als u Azure DevOps wilt machtigen voor gebruik met uw GitHub-account, klikt u op **autoriseren** en volgt u de procedure in het venster dat wordt geopend.

    ![Azure DevOps-GitHub autoriseren](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-azure-devops-to-your-azure-container-service-cluster"></a>Azure DevOps verbinden met uw Azure Container Service-cluster

De laatste stappen voor het ophalen van de CI/CD-pijp lijn zijn het configureren van externe verbindingen met uw docker Swarm-cluster in Azure. 

1. Voor het docker Swarm-cluster voegt u een eind punt van het type **SSH**toe. Voer vervolgens de SSH-verbindings gegevens in van uw Swarm-cluster (hoofd knooppunt).

    ![Azure DevOps-SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Alle configuratie wordt nu uitgevoerd. In de volgende stappen maakt u de CI/CD-pijp lijn die de toepassing bouwt en implementeert op het docker Swarm-cluster. 

## <a name="step-2-create-the-build-pipeline"></a>Stap 2: de build-pijp lijn maken

In deze stap stelt u een build-pijp lijn in voor uw Azure DevOps-project en definieert u de build-werk stroom voor uw container installatie kopieën

### <a name="initial-pipeline-setup"></a>Eerste pijp lijn instellen

1. Als u een build-pijp lijn wilt maken, maakt u verbinding met uw Azure DevOps-project en klikt u op **build & release**. Klik in de sectie **Build Definitions** op **+ New**. 

    ![Azure DevOps-nieuwe build-pijp lijn](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Selecteer het **lege proces**.

    ![Azure DevOps-nieuwe, lege build-pijp lijn](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Klik vervolgens op het tabblad **variabelen** en maak twee nieuwe variabelen: **RegistryURL** en **AgentURL**. Plak de waarden van het REGI ster en de cluster agents DNS.

    ![Azure DevOps-configuratie van variabelen samen stellen](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. Open op de pagina **opbouw definities** het tabblad **Triggers** en configureer de build voor het gebruik van doorlopende integratie met de Fork van het MyShop-project dat u hebt gemaakt in de vereisten. Selecteer vervolgens **batch wijzigingen**. Zorg ervoor dat u *docker-Linux* als **vertakkings specificatie**selecteert.

    ![Azure DevOps-opslagplaats configuratie maken](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Ten slotte klikt u op het tabblad **Opties** en configureert u de standaard agent wachtrij voor het **hosten van Linux preview**.

    ![Azure DevOps-host-agent configuratie](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>De werk stroom voor bouwen definiëren
In de volgende stappen wordt de werk stroom bouwen gedefinieerd. Eerst moet u de bron van de code configureren. U doet dit door **github** en uw **opslag plaats** en **vertakking** (docker-Linux) te selecteren.

![Azure DevOps-code bron configureren](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Er zijn vijf container installatie kopieën die kunnen worden gemaakt voor de *MyShop* -toepassing. Elke installatie kopie wordt gemaakt met behulp van de Dockerfile die zich in de project mappen bevinden:

* ProductsApi
* Proxy
* RatingsApi
* RecommendationsApi
* ShopFront

U hebt twee docker-stappen voor elke installatie kopie nodig, een om de installatie kopie te bouwen en één om de installatie kopie te pushen in het Azure container Registry. 

1. Als u een stap wilt toevoegen in de werk stroom bouwen, klikt u op **+ Build-stap toevoegen** en selecteert u **docker**.

    ![Azure-DevOps: bouw stappen toevoegen](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Configureer voor elke installatie kopie één stap die gebruikmaakt van de `docker build` opdracht.

    ![Azure DevOps-docker-build](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    Voor de bouw bewerking selecteert u uw Azure Container Registry, de actie **een installatie kopie maken** en de Dockerfile die elke afbeelding definieert. Stel de **werkmap** in als de hoofdmap van de Dockerfile, definieer de naam van de **installatie kopie**en selecteer **laatste tag toevoegen**.
    
    De naam van de installatie kopie moet de volgende indeling hebben: ```$(RegistryURL)/[NAME]:$(Build.BuildId)```. Vervang **[name]** door de naam van de installatie kopie:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Configureer voor elke installatie kopie een tweede stap die gebruikmaakt van de `docker push` opdracht.

    ![Azure DevOps-docker-push](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    Voor de push-bewerking selecteert u uw Azure container Registry, de actie **een installatie kopie pushen** , voert u de naam van de **installatie kopie** in die in de vorige stap is ingebouwd en selecteert u **laatste tag toevoegen**.

4. Nadat u de stappen voor het bouwen en pushen voor elk van de vijf installatie kopieën hebt geconfigureerd, voegt u nog drie stappen in de werk stroom bouwen toe.

   ![Azure DevOps-opdracht regel taak toevoegen](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

   1. Een opdracht regel taak die gebruikmaakt van een bash-script om het *RegistryURL* -exemplaar in het bestand docker-Compose. yml te vervangen door de variabele RegistryURL. 
    
       ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

       ![Azure-DevOps: een opstel bestand met een register-URL bijwerken](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

   2. Een opdracht regel taak die gebruikmaakt van een bash-script om het *AgentURL* -exemplaar in het bestand docker-Compose. yml te vervangen door de variabele AgentURL.
  
       ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

      1. Een taak die het bijgewerkte opgebouwde bestand als een bouw artefact heeft neergezet zodat het in de release kan worden gebruikt. Raadpleeg het volgende scherm voor meer informatie.

      ![Azure-DevOps-publicatie artefact](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

      ![Azure-DevOps-bestand publiceren opstellen](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Klik op **& wachtrij opslaan** om uw build-pijp lijn te testen.

   ![Azure DevOps-opslaan en wachtrij](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Azure DevOps-nieuwe wachtrij](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Als de **Build** juist is, ziet u dit scherm:

   ![Azure DevOps-build geslaagd](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-pipeline"></a>Stap 3: de release pijplijn maken

Met Azure DevOps kunt u [releases in meerdere omgevingen beheren](https://www.visualstudio.com/team-services/release-management/). U kunt continue implementatie inschakelen om ervoor te zorgen dat uw toepassing op een eenvoudige manier wordt geïmplementeerd in uw andere omgevingen (zoals dev, test, pre-productie en productie). U kunt een omgeving maken die uw Azure Container Service docker Swarm-modus cluster vertegenwoordigt.

![Azure DevOps-vrijgeven aan ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Installatie van de eerste release

1. Als u een release pijplijn wilt maken, klikt u op **Releases** >  **+ release**

2. Als u de artefact bron wilt configureren, klikt u op **artefacten** > **een artefact bron te koppelen**. Koppel deze nieuwe release pijplijn aan de build die u in de vorige stap hebt gedefinieerd. Daarna is het docker-Compose. yml-bestand beschikbaar in het release proces.

    ![Azure DevOps-release-artefacten](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Als u de release trigger wilt configureren, klikt u op **Triggers** en selecteert u **doorlopende implementatie**. Stel de trigger in op dezelfde artefact bron. Deze instelling zorgt ervoor dat een nieuwe release wordt gestart wanneer de build is voltooid.

    ![Azure DevOps-release triggers](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Als u de release variabelen wilt configureren, klikt u op **variabelen** en selecteert u **+ variabele** om drie nieuwe variabelen te maken met de informatie van het REGI ster: **docker. username**, **docker. password**en **docker. Registry**. Plak de waarden van het REGI ster en de cluster agents DNS.

    ![Azure DevOps-opslagplaats configuratie maken](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Zoals in het vorige scherm wordt weer gegeven, klikt u op het selectie vakje **vergren delen** in docker. wacht woord. Deze instelling is belang rijk om het wacht woord te beperken.
    >

### <a name="define-the-release-workflow"></a>De release werk stroom definiëren

De release werk stroom bestaat uit twee taken die u toevoegt.

1. Configureer een taak voor het veilig kopiëren van het opstellende bestand naar een *Deploy* -map op het knoop punt docker Swarm-Master, met behulp van de SSH-verbinding die u eerder hebt geconfigureerd. Raadpleeg het volgende scherm voor meer informatie.
    
    Bronmap: ```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![SCP voor Azure DevOps-release](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Configureer een tweede taak om een bash-opdracht uit te voeren om `docker` en `docker stack deploy` opdrachten uit te voeren op het hoofd knooppunt. Raadpleeg het volgende scherm voor meer informatie.

    ```
    docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth
    ```

    ![Azure DevOps-release bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    De opdracht die wordt uitgevoerd op de Master, maakt gebruik van de docker-CLI en de docker-CLI om de volgende taken uit te voeren:

   - Meld u aan bij het Azure container Registry (hiervoor worden drie build-variabelen gebruikt die zijn gedefinieerd op het tabblad **variabelen** )
   - Definieer de **DOCKER_HOST** variabele die moet worden gebruikt voor het Swarm-eind punt (: 2375)
   - Ga naar de *Deploy* -map die is gemaakt door de vorige beveiligde Kopieer taak en die het docker-Compose. yml-bestand bevat 
   - Voer `docker stack deploy` opdrachten uit om de nieuwe installatie kopieën op te halen en de containers te maken.

     >[!IMPORTANT]
     > Zoals u in het vorige scherm kunt zien, schakelt u het selectie vakje **Fail on stderr** uit. Met deze instelling kunnen we het release proces volt ooien omdat `docker-compose` verschillende diagnostische berichten hebt afgedrukt, zoals containers die worden gestopt of verwijderd, op de standaard fout uitvoer. Als u het selectie vakje inschakelt, meldt Azure DevOps dat er fouten zijn opgetreden tijdens de release, zelfs als alles goed gaat.
     >
3. Sla deze nieuwe release pijplijn op.

## <a name="step-4-test-the-cicd-pipeline"></a>Stap 4: de CI/CD-pijp lijn testen

Nu u klaar bent met de configuratie, is het tijd om deze nieuwe CI/CD-pijp lijn te testen. De eenvoudigste manier om dit te testen is het bijwerken van de bron code en het door voeren van de wijzigingen in uw GitHub-opslag plaats. Een paar seconden na het pushen van de code ziet u een nieuwe build die wordt uitgevoerd in azure DevOps. Zodra de implementatie is voltooid, wordt een nieuwe release geactiveerd en wordt de nieuwe versie van de toepassing op het Azure Container Service cluster geïmplementeerd.

## <a name="next-steps"></a>Volgende stappen

* Voor meer informatie over CI/CD met Azure DevOps raadpleegt u het artikel over [Azure pipelines](/azure/devops/pipelines/?view=azure-devops) .
* Zie de [ACS-engine github opslag plaats](https://github.com/Azure/acs-engine)voor meer informatie over de ACS-engine.
* Zie het overzicht van de [docker Swarm-modus](https://docs.docker.com/engine/swarm/)voor meer informatie over de modus docker Swarm.
