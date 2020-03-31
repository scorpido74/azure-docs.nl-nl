---
title: (AFGESCHAFT) CI/CD met Azure Container Service en Swarm
description: Azure Container Service gebruiken met Docker Swarm, een Azure Container Registry en Azure DevOps om continu een .NET Core-toepassing met meerdere containers te leveren
author: jcorioland
ms.service: container-service
ms.topic: conceptual
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 11a6debe735459b617f6f93c3f67a32350dd4623
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76549050"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-azure-devops-services"></a>(AFGESCHAFT) Volledige CI/CD-pijplijn voor het implementeren van een multicontainertoepassing op Azure Container Service met Docker Swarm met Azure DevOps Services

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Een van de grootste uitdagingen bij het ontwikkelen van moderne applicaties voor de cloud is het continu kunnen leveren van deze applicaties. In dit artikel leert u hoe u een CI/CD-pijplijn (full continuous integration and deployment) implementeert met Azure Container Service met Docker Swarm, Azure Container Registry en Azure Pipelines-beheer.


![Voorbeeldtoepassing MyShop](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

Het doel is om deze toepassing continu te leveren in een Docker Swarm-cluster, met azure DevOps-services. De volgende figuur beschrijft deze continue leveringspijplijn:

![Voorbeeldtoepassing MyShop](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Hier is een korte uitleg van de stappen:

1. Codewijzigingen zijn vastgelegd in de broncoderepository (hier, GitHub) 
1. GitHub activeert een build in Azure DevOps Services 
1. Azure DevOps Services krijgt de nieuwste versie van de bronnen en bouwt alle afbeeldingen die de toepassing samenstellen 
1. Azure DevOps Services duwt elke afbeelding naar een Docker-register dat is gemaakt met behulp van de Azure Container Registry-service 
1. Azure DevOps Services activeert een nieuwe release 
1. Met de release worden enkele opdrachten uitgevoerd met SSH op het clustermasterknooppunt Azure containerservice 
1. Docker Zwerm op het cluster trekt de nieuwste versie van de beelden 
1. De nieuwe versie van de toepassing wordt geïmplementeerd met Docker Compose 

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelfstudie begint, moet u de volgende taken voltooien:

- [Een Swarm-cluster in Azure Container Service maken](container-service-deployment.md)
- [Verbinding maken met het Swarm-cluster in Azure Container Service](../container-service-connect.md)
- [Een Azure-containerregister maken](../../container-registry/container-registry-get-started-portal.md)
- [Een Azure DevOps Services-organisatie en -project laten maken](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Fork de GitHub repository naar uw GitHub-account](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Je hebt ook een Ubuntu (14.04 of 16.04) machine nodig met Docker geïnstalleerd. Deze machine wordt gebruikt door Azure DevOps Services tijdens de Azure Pipelines-processen. Een manier om deze machine te maken is door de afbeelding te gebruiken die beschikbaar is in de Azure Marketplace. 

## <a name="step-1-configure-your-azure-devops-services-organization"></a>Stap 1: Uw Azure DevOps Services-organisatie configureren 

In deze sectie configureert u uw Azure DevOps Services-organisatie.

### <a name="configure-an-azure-devops-services-linux-build-agent"></a>Een Linux-buildagent van Azure DevOps Services configureren

Als u Docker-afbeeldingen wilt maken en deze afbeeldingen vanuit een Azure DevOps Services-build naar een Azure DevOps Services-build wilt pushen, moet u een Linux-agent registreren. U hebt de volgende installatieopties:

* [Een agent implementeren op Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Docker gebruiken om de Azure DevOps Services-agent uit te voeren](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-azure-devops-services-extension"></a>De azure DevOps Services-extensie docker-integratie installeren

Microsoft biedt een Azure DevOps Services-extensie om te werken met Docker in Azure Pipelines-processen. Deze extensie is beschikbaar in de [Azure DevOps Services Marketplace.](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker) Klik **op Installeren** om deze extensie toe te voegen aan uw Azure DevOps Services-organisatie:

![De Docker-integratie installeren](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

U wordt gevraagd verbinding te maken met uw Azure DevOps Services-organisatie met uw referenties. 

### <a name="connect-azure-devops-services-and-github"></a>Azure DevOps Services en GitHub verbinden

Stel een verbinding in tussen uw Azure DevOps Services-project en uw GitHub-account.

1. Klik in uw Azure DevOps Services-project op het pictogram **Instellingen** op de werkbalk en selecteer **Services**.

    ![Azure DevOps Services - Externe verbinding](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. Klik aan de linkerkant op **Nieuw serviceeindpunt** > **GitHub**.

    ![Azure DevOps Services - GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Als u Azure DevOps Services wilt autoriseren om met uw GitHub-account te werken, klikt u op **Autoriseren** en volgt u de procedure in het venster dat wordt geopend.

    ![Azure DevOps Services - GitHub autoriseren](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-azure-devops-services-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Azure DevOps Services verbinden met uw Azure-containerregister en Azure Container Service-cluster

De laatste stappen voordat u in de CI/CD-pijplijn stapt, zijn het configureren van externe verbindingen met uw containerregister en uw Docker Swarm-cluster in Azure. 

1. Voeg in de **Services-instellingen** van uw Azure DevOps Services-project een serviceeindpunt toe van het **type Docker Registry.** 

1. Voer in de pop-up die wordt geopend de URL en de referenties van uw Azure-containerregister in.

    ![Azure DevOps Services - Docker-register](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. Voeg voor het cluster Docker Swarm een eindpunt van het type **SSH**toe. Voer vervolgens de SSH-verbindingsgegevens van uw Swarm-cluster in.

    ![Azure DevOps Services - SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Alle configuratie is nu gedaan. In de volgende stappen maakt u de CI/CD-pijplijn die de toepassing bouwt en implementeert naar het Docker Zwerm-cluster. 

## <a name="step-2-create-the-build-pipeline"></a>Stap 2: De buildpijplijn maken

In deze stap stelt u een buildpijplijn in voor uw Azure DevOps Services-project en definieert u de buildworkflow voor uw containerafbeeldingen

### <a name="initial-pipeline-setup"></a>Initiële pijplijninstallatie

1. Als u een buildpijplijn wilt maken, maakt u verbinding met uw Azure DevOps Services-project en klikt u op **Build & Release**. 

1. Klik **in** de sectie Definities bouwen op **+ Nieuw**. Selecteer de sjabloon **Leeg** maken.

    ![Azure DevOps - Nieuwe buildpijplijn](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. Configureer de nieuwe build met een GitHub-bron, controleer **Continue integratie**en selecteer de agentwachtrij waar u uw Linux-agent hebt geregistreerd. Klik **op Maken** om de buildpijplijn te maken.

    ![Azure DevOps-services - Buildpipeline maken](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. Open op de pagina **Definities bouwen** eerst het tabblad **Repository** en configureer de build om de vork van het MyShop-project te gebruiken dat u in de vereisten hebt gemaakt. Zorg ervoor dat u *acs-documenten* selecteert als **standaardvertakking**.

    ![Azure DevOps-services - Repository-configuratie bouwen](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. Configureer op het tabblad **Triggers** de build die na elke commit moet worden geactiveerd. Selecteer **Continue integratie** en **batchwijzigingen**.

    ![Azure DevOps-services - Triggerconfiguratie bouwen](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>De buildwerkstroom definiëren
In de volgende stappen wordt de buildworkflow gedefinieerd. Er zijn vijf containerafbeeldingen om te bouwen voor de *MyShop-applicatie.* Elke afbeelding wordt gemaakt met behulp van het Dockerfile in de projectmappen:

* ProductsApi
* Proxy
* RatingsApi
* AanbevelingenApi
* Winkelvoor

U moet twee Docker-stappen toevoegen voor elke afbeelding, één om de afbeelding te maken en één om de afbeelding in het Azure-containerregister te duwen. 

1. Als u een stap in de buildwerkstroom wilt toevoegen, klikt u op **+ Buildstap toevoegen** en selecteert u **Docker**.

    ![Azure DevOps-services - Buildstappen toevoegen](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Configureer voor elke afbeelding één `docker build` stap die de opdracht gebruikt.

    ![Azure DevOps Services - Docker Build](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Selecteer voor de buildbewerking uw Azure-containerregister, de **actie Een afbeelding maken** en het Dockerbestand dat elke afbeelding definieert. Stel de **context Build** in als de hoofdmap Dockerfile en definieer de **afbeeldingsnaam**. 
    
    Zoals op het vorige scherm wordt weergegeven, start u de afbeeldingsnaam met de URI van uw Azure-containerregister. (U ook een buildvariabele gebruiken om de tag van de afbeelding te parameteriseren, zoals de build-id in dit voorbeeld.)

1. Configureer voor elke afbeelding een `docker push` tweede stap die de opdracht gebruikt.

    ![Azure DevOps Services - Docker Push](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Selecteer voor de pushbewerking uw Azure-containerregister, de **actie Een afbeelding pushen** en voer de **afbeeldingsnaam** in die in de vorige stap is gebouwd.

1. Nadat u de build- en pushstappen voor elk van de vijf afbeeldingen hebt geconfigureerd, voegt u nog twee stappen toe in de werkstroom voor bouwen.

    a. Een opdrachtregeltaak die een bashscript gebruikt om de gebeurtenis *BuildNumber* in het docker-compose.yml-bestand te vervangen door de huidige build-id. Zie het volgende scherm voor meer informatie.

    ![Azure DevOps Services - Bestand samenstellen bijwerken](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Een taak die het bijgewerkte bestand Compose als een build-artefact laat vallen, zodat het in de release kan worden gebruikt. Zie het volgende scherm voor meer informatie.

    ![Azure DevOps Services - Bestand opstellen publiceren](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. Klik **op Opslaan** en een naam geven aan de buildpijplijn.

## <a name="step-3-create-the-release-pipeline"></a>Stap 3: De releasepijplijn maken

Met Azure DevOps Services u [releases beheren in verschillende omgevingen.](https://www.visualstudio.com/team-services/release-management/) U continue implementatie inschakelen om ervoor te zorgen dat uw toepassing op een soepele manier wordt geïmplementeerd in uw verschillende omgevingen (zoals dev, test, pre-productie en productie). U een nieuwe omgeving maken die uw Azure Container Service Docker Swarm-cluster vertegenwoordigt.

![Azure DevOps Services - Vrijgeven aan ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Initiële release-installatie

1. Als u een releasepijplijn wilt maken, klikt u op **Releases** > **+ Release**

1. Als u de artefactbron wilt configureren, klikt u op **Artefacten** > **Koppeling van een artefactbron**. Koppel deze nieuwe releasepijplijn hier aan de build die u in de vorige stap hebt gedefinieerd. Door dit te doen, is het docker-compose.yml-bestand beschikbaar in het releaseproces.

    ![Azure DevOps Services - Artefacten vrijgeven](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. Als u de releasetrigger wilt configureren, klikt u op **Triggers** en selecteert u **Continue implementatie**. Stel de trigger in op dezelfde artefactbron. Deze instelling zorgt ervoor dat een nieuwe release wordt gestart zodra de build is voltooid.

    ![Azure DevOps Services - Triggers vrijgeven](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>De releasewerkstroom definiëren

De releaseworkflow bestaat uit twee taken die u toevoegt.

1. Configureer een taak om het compositiebestand veilig te kopiëren naar een *implementatiemap* op het hoofdknooppunt Docker Swarm, met behulp van de SSH-verbinding die u eerder hebt geconfigureerd. Zie het volgende scherm voor meer informatie.

    ![Azure DevOps Services - SCP vrijgeven](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Configureer een tweede taak om `docker` een `docker-compose` bashopdracht uit te voeren om uit te voeren en opdrachten op het hoofdknooppunt. Zie het volgende scherm voor meer informatie.

    ![Azure DevOps Services - Release Bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    De opdracht die op de master is uitgevoerd, gebruikt de Docker CLI en de Docker-Compose CLI om de volgende taken uit te voeren:

   - Inloggen bij het Azure-containerregister (het maakt gebruik van drie buildvariab'les die zijn gedefinieerd op het tabblad **Variabelen)**
   - De **DOCKER_HOST** variabele definiëren om met het Swarm-eindpunt te werken (:2375)
   - Navigeer naar de *map implementeren* die is gemaakt door de vorige beveiligde kopieertaak en die het docker-compose.yml-bestand bevat 
   - Voer `docker-compose` opdrachten uit die de nieuwe afbeeldingen trekken, de services stoppen, de services verwijderen en de containers maken.

     >[!IMPORTANT]
     > Zoals op het vorige scherm wordt weergegeven, laat u het selectievakje **Fail ontsmet op STDERR** onaangevinkt. Dit is een belangrijke `docker-compose` instelling, omdat verschillende diagnostische berichten, zoals containers, worden gestopt of verwijderd, worden afgedrukt op de standaardfoutuitvoer. Als u het selectievakje inschakelt, meldt Azure DevOps Services dat er fouten zijn opgetreden tijdens de release, zelfs als alles goed gaat.
     >
1. Sla deze nieuwe releasepijplijn op.


>[!NOTE]
>Deze implementatie omvat enige downtime omdat we de oude services stoppen en de nieuwe uitvoeren. Het is mogelijk om dit te voorkomen door het doen van een blauw-groene inzet.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Stap 4. De CI/CD-pijplijn testen

Nu u klaar bent met de configuratie, is het tijd om deze nieuwe CI / CD-pijplijn te testen. De eenvoudigste manier om het te testen is om de broncode bij te werken en de wijzigingen in uw GitHub-repository te verbinden. Een paar seconden nadat u de code hebt ingedrukt, ziet u een nieuwe build in Azure DevOps Services. Zodra deze is voltooid, wordt een nieuwe release geactiveerd en wordt de nieuwe versie van de toepassing geïmplementeerd op het Azure Container Service-cluster.

## <a name="next-steps"></a>Volgende stappen

* Zie het [documentaalpipelinesdocumentatie](/azure/devops/pipelines/?view=azure-devops) van Azure Pipelines voor meer informatie over CI/CD met Azure DevOps Services.
