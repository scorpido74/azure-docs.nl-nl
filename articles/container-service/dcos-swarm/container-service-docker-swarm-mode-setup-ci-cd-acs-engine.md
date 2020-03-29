---
title: (AFGESCHAFT) CI/CD met Azure Container Service Engine en Zwermmodus
description: Azure Container Service Engine gebruiken met Docker Swarm Mode, een Azure Container Registry en Azure DevOps om continu een .NET Core-toepassing met meerdere containers te leveren
author: diegomrtnzg
ms.service: container-service
ms.topic: conceptual
ms.date: 05/27/2017
ms.author: dimart
ms.custom: mvc
ms.openlocfilehash: 1ec7ece6f5afd1bbd2613ae08af04b82e8a156b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277920"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-azure-devops"></a>(AFGESCHAFT) Volledige CI/CD-pijplijn voor het implementeren van een multicontainertoepassing op Azure Container Service met ACS Engine en Docker Swarm-modus met Azure DevOps

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

*Dit artikel is gebaseerd op [een volledige CI/CD-pijplijn om een multicontainertoepassing op Azure Container Service met Docker Swarm te implementeren met Azure DevOps-documentatie](container-service-docker-swarm-setup-ci-cd.md)*

Een van de grootste uitdagingen bij het ontwikkelen van moderne applicaties voor de cloud is het continu kunnen leveren van deze applicaties. In dit artikel leert u hoe u een ci/cd-pijplijn (volledige continue integratie en implementatie) implementeert met behulp van: 
* Azure Container Service Engine met Docker Zwermmodus
* Azure Container Registry
* Azure DevOps


![Voorbeeldtoepassing MyShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

Het doel is om deze toepassing continu te leveren in een Docker Swarm Mode-cluster, met azure DevOps. De volgende figuur beschrijft deze continue leveringspijplijn:

![Voorbeeldtoepassing MyShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Hier is een korte uitleg van de stappen:

1. Codewijzigingen zijn vastgelegd in de broncoderepository (hier, GitHub) 
2. GitHub activeert een build in Azure DevOps 
3. Azure DevOps krijgt de nieuwste versie van de bronnen en bouwt alle afbeeldingen die de toepassing samenstellen 
4. Azure DevOps duwt elke afbeelding naar een Docker-register dat is gemaakt met behulp van de Azure Container Registry-service 
5. Azure DevOps activeert een nieuwe release 
6. Met de release worden enkele opdrachten uitgevoerd met SSH op het clustermasterknooppunt Azure containerservice 
7. Docker Swarm Mode op het cluster trekt de nieuwste versie van de beelden 
8. De nieuwe versie van de toepassing wordt geïmplementeerd met Docker Stack 

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelfstudie begint, moet u de volgende taken voltooien:

- [Een cluster van zwermmodus maken in Azure Container Service met ACS Engine](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Verbinding maken met het Swarm-cluster in Azure Container Service](../container-service-connect.md)
- [Een Azure-containerregister maken](../../container-registry/container-registry-get-started-portal.md)
- [Een Azure DevOps-organisatie en -project laten maken](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Fork de GitHub repository naar uw GitHub-account](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> De Docker Swarm-orchestrator in Azure Container Service maakt gebruik van legacy standalone Swarm. Momenteel is de geïntegreerde [Swarm-modus](https://docs.docker.com/engine/swarm/) (in Docker 1.12 en hoger) geen ondersteunde orchestrator in Azure Container Service. Daarom gebruiken we [ACS Engine,](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md)een [quickstartsjabloon](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/)die door de community is bijgedragen of een Docker-oplossing in de [Azure Marketplace.](https://azuremarketplace.microsoft.com)
>

## <a name="step-1-configure-your-azure-devops-organization"></a>Stap 1: Uw Azure DevOps-organisatie configureren 

In deze sectie configureert u uw Azure DevOps-organisatie. Als u Azure DevOps Services-eindpunten wilt configureren, klikt u in uw Azure DevOps-project op het pictogram **Instellingen** op de werkbalk en selecteert u **Services**.

![Eindpunt van Services openen](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-azure-devops-and-azure-account"></a>Azure DevOps en Azure-account verbinden

Stel een verbinding in tussen uw Azure DevOps-project en uw Azure-account.

1. Klik aan de linkerkant op **Nieuw serviceeindpunt** > **Azure Resource Manager**.
2. Als u Azure DevOps wilt autoriseren om met uw Azure-account te werken, selecteert u uw **abonnement** en klikt u op **OK**.

    ![Azure DevOps - Azure autoriseren](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-azure-devops-and-github"></a>Azure DevOps en GitHub verbinden

Stel een verbinding in tussen uw Azure DevOps-project en uw GitHub-account.

1. Klik aan de linkerkant op **Nieuw serviceeindpunt** > **GitHub**.
2. Als u Azure DevOps wilt autoriseren om met uw GitHub-account te werken, klikt u op **Autoriseren** en volgt u de procedure in het venster dat wordt geopend.

    ![Azure DevOps - GitHub autoriseren](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-azure-devops-to-your-azure-container-service-cluster"></a>Azure DevOps verbinden met uw Azure Container Service-cluster

De laatste stappen voordat u in de CI/CD-pijplijn stapt, zijn het configureren van externe verbindingen met uw Docker Swarm-cluster in Azure. 

1. Voeg voor het cluster Docker Swarm een eindpunt van het type **SSH**toe. Voer vervolgens de SSH-verbindingsgegevens van uw Swarm-cluster (hoofdknooppunt) in.

    ![Azure DevOps - SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Alle configuratie is nu gedaan. In de volgende stappen maakt u de CI/CD-pijplijn die de toepassing bouwt en implementeert naar het Docker Zwerm-cluster. 

## <a name="step-2-create-the-build-pipeline"></a>Stap 2: De buildpijplijn maken

In deze stap stelt u een buildpijplijn in voor uw Azure DevOps-project en definieert u de buildworkflow voor uw containerafbeeldingen

### <a name="initial-pipeline-setup"></a>Initiële pijplijninstallatie

1. Als u een buildpijplijn wilt maken, maakt u verbinding met uw Azure DevOps-project en klikt u op **Build & Release**. Klik **in** de sectie Definities bouwen op **+ Nieuw**. 

    ![Azure DevOps - Nieuwe buildpijplijn](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Selecteer het **proces Leeg**.

    ![Azure DevOps - Nieuwe lege buildpijplijn](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Klik vervolgens op het tabblad **Variabelen** en maak twee nieuwe variabelen: **RegistryURL** en **AgentURL**. Plak de waarden van dns-register- en clusteragents.

    ![Azure DevOps - Configuratie variabelen bouwen](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. Open op de pagina **Definities bouwen** het tabblad **Triggers** en configureer de build om continue integratie te gebruiken met de vork van het MyShop-project dat u in de vereisten hebt gemaakt. Selecteer vervolgens **Batchwijzigingen**. Zorg ervoor dat u *docker-linux* selecteert als de **Branch-specificatie.**

    ![Azure DevOps - Repository-configuratie bouwen](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Klik tot slot op het tabblad **Opties** en configureer de standaardagentwachtrij naar **Hosted Linux Preview**.

    ![Azure DevOps - Configuratie hostagent](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>De buildwerkstroom definiëren
In de volgende stappen wordt de buildworkflow gedefinieerd. Eerst moet u de bron van de code configureren. Selecteer hiervoor **GitHub** en uw **repository** en **branch** (docker-linux).

![Azure DevOps - Codebron configureren](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Er zijn vijf containerafbeeldingen om te bouwen voor de *MyShop-applicatie.* Elke afbeelding wordt gemaakt met behulp van het Dockerfile in de projectmappen:

* ProductsApi
* Proxy
* RatingsApi
* AanbevelingenApi
* Winkelvoor

U hebt twee Docker-stappen nodig voor elke afbeelding, één om de afbeelding te maken en één om de afbeelding in het Azure-containerregister te pushen. 

1. Als u een stap in de buildwerkstroom wilt toevoegen, klikt u op **+ Buildstap toevoegen** en selecteert u **Docker**.

    ![Azure DevOps - Buildstappen toevoegen](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Configureer voor elke afbeelding één `docker build` stap die de opdracht gebruikt.

    ![Azure DevOps - Docker Build](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    Selecteer voor de buildbewerking uw Azure Container Registry, de **actie Een afbeelding maken** en het Dockerbestand dat elke afbeelding definieert. Stel de **werkmap** in als de hoofdmap dockerfile, definieer de **naam afbeelding**en selecteer **Laatste tag opnemen**.
    
    De naam van de afbeelding ```$(RegistryURL)/[NAME]:$(Build.BuildId)```moet in deze indeling staan: . Vervang **[NAAM]** door de afbeeldingsnaam:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Configureer voor elke afbeelding een `docker push` tweede stap die de opdracht gebruikt.

    ![Azure DevOps - Docker Push](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    Selecteer voor de pushbewerking uw Azure-containerregister, de **actie Een afbeelding pushen,** voert de **afbeeldingsnaam** in die in de vorige stap is gebouwd en selecteer **Laatste tag opnemen**.

4. Nadat u de build- en pushstappen voor elk van de vijf afbeeldingen hebt geconfigureerd, voegt u nog drie stappen toe in de werkstroom voor bouwen.

   ![Azure DevOps - Opdrachtregeltaak toevoegen](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

   1. Een opdrachtregeltaak die een bashscript gebruikt om de *gebeurtenis RegistryURL* in het docker-compose.yml-bestand te vervangen door de variabele RegistryURL. 
    
       ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

       ![Azure DevOps - Bestand samenstellen bijwerken met register-URL](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

   2. Een opdrachtregeltaak die een bashscript gebruikt om de *agentURL-gebeurtenis* in het docker-compose.yml-bestand te vervangen door de agentURL-variabele.
  
       ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

      1. Een taak die het bijgewerkte bestand Compose als een build-artefact laat vallen, zodat het in de release kan worden gebruikt. Zie het volgende scherm voor meer informatie.

      ![Azure DevOps - Artefact publiceren](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

      ![Azure DevOps - Bestand opstellen publiceren](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Klik **op & wachtrij opslaan** om de buildpijplijn te testen.

   ![Azure DevOps - Opslaan en wachtrijen](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Azure DevOps - Nieuwe wachtrij](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Als de **build** correct is, moet u dit scherm zien:

   ![Azure DevOps - Build geslaagd](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-pipeline"></a>Stap 3: De releasepijplijn maken

Met Azure DevOps u [releases beheren in verschillende omgevingen.](https://www.visualstudio.com/team-services/release-management/) U continue implementatie inschakelen om ervoor te zorgen dat uw toepassing op een soepele manier wordt geïmplementeerd in uw verschillende omgevingen (zoals dev, test, pre-productie en productie). U een omgeving maken die uw Azure Container Service Docker Swarm Mode-cluster vertegenwoordigt.

![Azure DevOps - Release naar ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Initiële release-installatie

1. Als u een releasepijplijn wilt maken, klikt u op **Releases** > **+ Release**

2. Als u de artefactbron wilt configureren, klikt u op **Artefacten** > **Koppeling van een artefactbron**. Koppel deze nieuwe releasepijplijn hier aan de build die u in de vorige stap hebt gedefinieerd. Daarna is het docker-compose.yml-bestand beschikbaar in het releaseproces.

    ![Azure DevOps - Artefacten vrijgeven](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Als u de releasetrigger wilt configureren, klikt u op **Triggers** en selecteert u **Continue implementatie**. Stel de trigger in op dezelfde artefactbron. Deze instelling zorgt ervoor dat een nieuwe release wordt gestart wanneer de build is voltooid.

    ![Azure DevOps - Triggers vrijgeven](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Als u de releasevariabelen wilt configureren, klikt u op **Variabelen** en selecteert **u +Variable** om drie nieuwe variabelen te maken met de informatie van het register: **docker.username**, **docker.password**en **docker.registry**. Plak de waarden van dns-register- en clusteragents.

    ![Azure DevOps - Repository-configuratie bouwen](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Zoals op het vorige scherm wordt weergegeven, klikt u op het selectievakje **Vergrendelen** in docker.password. Deze instelling is belangrijk om het wachtwoord te beperken.
    >

### <a name="define-the-release-workflow"></a>De releasewerkstroom definiëren

De releaseworkflow bestaat uit twee taken die u toevoegt.

1. Configureer een taak om het compositiebestand veilig te kopiëren naar een *implementatiemap* op het hoofdknooppunt Docker Swarm, met behulp van de SSH-verbinding die u eerder hebt geconfigureerd. Zie het volgende scherm voor meer informatie.
    
    Bronmap:```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Azure DevOps - SCP vrijgeven](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Configureer een tweede taak om `docker` een `docker stack deploy` bashopdracht uit te voeren om uit te voeren en opdrachten op het hoofdknooppunt. Zie het volgende scherm voor meer informatie.

    ```
    docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth
    ```

    ![Azure DevOps - Release Bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    De opdracht die op de master wordt uitgevoerd, gebruikt de Docker CLI en de Docker-Compose CLI om de volgende taken uit te voeren:

   - Log in bij het Azure-containerregister (het maakt gebruik van drie buildvariabelen die zijn gedefinieerd op het tabblad **Variabelen)**
   - De **DOCKER_HOST** variabele definiëren om met het Swarm-eindpunt te werken (:2375)
   - Navigeer naar de *map implementeren* die is gemaakt door de vorige beveiligde kopieertaak en die het docker-compose.yml-bestand bevat 
   - Voer `docker stack deploy` opdrachten uit die de nieuwe afbeeldingen trekken en de containers maken.

     >[!IMPORTANT]
     > Zoals op het vorige scherm wordt weergegeven, laat u het selectievakje **Fail op STDERR** onaangevinkt. Met deze instelling kunnen we het `docker-compose` releaseproces voltooien vanwege het afdrukken van verschillende diagnostische berichten, zoals containers die stoppen of worden verwijderd, op de standaardfoutuitvoer. Als u het selectievakje inschakelt, meldt Azure DevOps dat er fouten zijn opgetreden tijdens de release, zelfs als alles goed gaat.
     >
3. Sla deze nieuwe releasepijplijn op.

## <a name="step-4-test-the-cicd-pipeline"></a>Stap 4: De CI/CD-pijplijn testen

Nu u klaar bent met de configuratie, is het tijd om deze nieuwe CI / CD-pijplijn te testen. De eenvoudigste manier om het te testen is om de broncode bij te werken en de wijzigingen in uw GitHub-repository te verbinden. Een paar seconden nadat u de code hebt ingedrukt, ziet u een nieuwe build in Azure DevOps. Zodra deze is voltooid, wordt een nieuwe versie geactiveerd en geïmplementeerd in de nieuwe versie van de toepassing op het Azure Container Service-cluster.

## <a name="next-steps"></a>Volgende stappen

* Zie het [documentaalpipelinesdocumentatie](/azure/devops/pipelines/?view=azure-devops) van Azure Pipelines voor meer informatie over CI/CD met Azure DevOps.
* Zie de [ACS Engine GitHub repo](https://github.com/Azure/acs-engine)voor meer informatie over ACS Engine.
* Zie het overzicht van de [Docker Swarm-modus](https://docs.docker.com/engine/swarm/)voor meer informatie over de Docker Swarm-modus.
