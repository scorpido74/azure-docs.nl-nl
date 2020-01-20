---
title: KEUR CI/CD met Azure Container Service en Swarm
description: Gebruik Azure Container Service met docker Swarm, een Azure Container Registry en Azure DevOps om voortdurend een .NET core-toepassing met meerdere containers te leveren
author: jcorioland
ms.service: container-service
ms.topic: conceptual
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 860c277e88918dc37eceb496d852691ced2af114
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277909"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-azure-devops-services"></a>KEUR Volledige CI/CD-pijp lijn voor het implementeren van een toepassing met meerdere containers op Azure Container Service met docker Swarm met Azure DevOps Services

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Een van de grootste uitdagingen bij het ontwikkelen van moderne toepassingen voor de Cloud is dat deze toepassingen continu kunnen worden geleverd. In dit artikel leert u hoe u een volledige doorlopende integratie-en implementatie pijplijn (CI/CD) kunt implementeren met Azure Container Service met docker Swarm, Azure Container Registry en Azure pipelines management.


![Voorbeeld toepassing MyShop](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

Het doel is om deze toepassing voortdurend te leveren in een docker Swarm-cluster met behulp van Azure DevOps Services. In de volgende afbeelding vindt u meer informatie over deze continue bezorgings pijplijn:

![Voorbeeld toepassing MyShop](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Hier volgt een korte uitleg van de stappen:

1. Code wijzigingen worden doorgevoerd in de opslag plaats van de bron code (hier, GitHub) 
1. GitHub activeert een build in azure DevOps Services 
1. Azure DevOps Services haalt de nieuwste versie van de bronnen op en bouwt alle installatie kopieën op waaruit de toepassing is samengesteld. 
1. Azure DevOps Services pusht elke installatie kopie naar een docker-REGI ster dat is gemaakt met behulp van de Azure Container Registry-service 
1. Azure DevOps Services activeert een nieuwe release 
1. De release voert enkele opdrachten uit met SSH op het hoofd knooppunt van het Azure container service-cluster 
1. Docker Swarm op het cluster haalt de nieuwste versie van de installatie kopieën op 
1. De nieuwe versie van de toepassing wordt geïmplementeerd met behulp van docker opstellen 

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelf studie begint, moet u de volgende taken uitvoeren:

- [Een Swarm-cluster in Azure Container Service maken](container-service-deployment.md)
- [Verbinding maken met het Swarm-cluster in Azure Container Service](../container-service-connect.md)
- [Een Azure container Registry maken](../../container-registry/container-registry-get-started-portal.md)
- [Een Azure DevOps Services-organisatie en een project maken](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [De GitHub-opslag plaats splitsen in uw GitHub-account](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

U hebt ook een Ubuntu-machine (14,04 of 16,04) nodig waarop docker is geïnstalleerd. Deze computer wordt door Azure DevOps Services gebruikt tijdens de processen van Azure pipeline. Een manier om deze machine te maken, is met behulp van de installatie kopie die beschikbaar is in [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/). 

## <a name="step-1-configure-your-azure-devops-services-organization"></a>Stap 1: uw Azure DevOps Services-organisatie configureren 

In deze sectie gaat u uw Azure DevOps Services-organisatie configureren.

### <a name="configure-an-azure-devops-services-linux-build-agent"></a>Een Azure DevOps Services Linux-bouw agent configureren

Als u docker-installatie kopieën wilt maken en deze installatie kopieën wilt pushen naar een Azure container Registry vanuit een Azure DevOps Services-build, moet u een Linux-agent registreren. U hebt de volgende installatie opties:

* [Een agent implementeren in Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Docker gebruiken om de Azure DevOps Services-agent uit te voeren](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-azure-devops-services-extension"></a>De docker-integratie Azure DevOps Services-extensie installeren

Micro soft biedt een Azure DevOps Services-extensie om te werken met docker in azure Pipelins-processen. Deze uitbrei ding is beschikbaar in [Azure DevOps Services Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Klik op **installeren** om deze extensie toe te voegen aan uw Azure DevOps Services-organisatie:

![Docker-integratie installeren](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

U wordt gevraagd om verbinding te maken met uw Azure DevOps Services-organisatie met behulp van uw referenties. 

### <a name="connect-azure-devops-services-and-github"></a>Verbinding maken met Azure DevOps Services en GitHub

Stel een verbinding in tussen uw Azure DevOps Services-project en uw GitHub-account.

1. Klik in uw Azure DevOps Services-project op het **instellingen** pictogram op de werk balk en selecteer **Services**.

    ![Azure DevOps Services-externe verbinding](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. Klik aan de linkerkant op **nieuw service-eind punt** > **github**.

    ![Azure DevOps Services-GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Als u Azure DevOps-Services wilt machtigen voor gebruik met uw GitHub-account, klikt u op **autoriseren** en volgt u de procedure in het venster dat wordt geopend.

    ![Azure DevOps Services-GitHub autoriseren](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-azure-devops-services-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Azure DevOps-Services verbinden met uw Azure container Registry en Azure Container Service-cluster

De laatste stappen voor het ophalen van de CI/CD-pijp lijn zijn het configureren van externe verbindingen met uw container register en uw docker Swarm-cluster in Azure. 

1. Voeg in de **Services** -instellingen van uw Azure DevOps Services-project een service-eind punt van het type **docker-REGI ster**toe. 

1. Voer in het pop-upvenster dat wordt geopend de URL en de referenties in van uw Azure container Registry.

    ![Azure DevOps Services-docker-REGI ster](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. Voor het docker Swarm-cluster voegt u een eind punt van het type **SSH**toe. Voer vervolgens de SSH-verbindings gegevens van uw Swarm-cluster in.

    ![Azure DevOps Services-SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Alle configuratie wordt nu uitgevoerd. In de volgende stappen maakt u de CI/CD-pijp lijn die de toepassing bouwt en implementeert op het docker Swarm-cluster. 

## <a name="step-2-create-the-build-pipeline"></a>Stap 2: de build-pijp lijn maken

In deze stap stelt u een build-pijp lijn in voor uw Azure DevOps Services-project en definieert u de build-werk stroom voor uw container installatie kopieën

### <a name="initial-pipeline-setup"></a>Eerste pijp lijn instellen

1. Als u een build-pijp lijn wilt maken, maakt u verbinding met uw Azure DevOps Services-project en klikt u op **build & release**. 

1. Klik in de sectie **Build Definitions** op **+ New**. Selecteer de **lege** sjabloon.

    ![Azure DevOps-nieuwe build-pijp lijn](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. Configureer de nieuwe build met een GitHub-opslagplaats bron, Controleer **doorlopende integratie**en selecteer de agent wachtrij waarin u uw Linux-agent hebt geregistreerd. Klik op **maken** om de build-pijp lijn te maken.

    ![Azure DevOps Services-build-pijp lijn maken](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. Open op de pagina **samenstellings definities** eerst het tabblad **opslag plaats** en configureer de build om de Fork te gebruiken van het MyShop-project dat u hebt gemaakt in de vereisten. Zorg ervoor dat u *ACS-docs* als **standaard vertakking**selecteert.

    ![Azure DevOps Services: opslagplaats configuratie samen stellen](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. Configureer op het tabblad **Triggers** dat de build moet worden geactiveerd na elke door voer. Selecteer **doorlopende integratie** en **batch wijzigingen**.

    ![Azure DevOps Services-build trigger-configuratie](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>De werk stroom voor bouwen definiëren
In de volgende stappen wordt de werk stroom bouwen gedefinieerd. Er zijn vijf container installatie kopieën die kunnen worden gemaakt voor de *MyShop* -toepassing. Elke installatie kopie wordt gemaakt met behulp van de Dockerfile die zich in de project mappen bevinden:

* ProductsApi
* Proxy
* RatingsApi
* RecommendationsApi
* ShopFront

U moet voor elke installatie kopie twee stappen voor docker toevoegen, één om de installatie kopie te bouwen en één om de installatie kopie te pushen in het Azure container Registry. 

1. Als u een stap wilt toevoegen in de werk stroom bouwen, klikt u op **+ Build-stap toevoegen** en selecteert u **docker**.

    ![Azure DevOps Services-build-stappen toevoegen](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Configureer voor elke installatie kopie één stap die gebruikmaakt van de `docker build` opdracht.

    ![Azure DevOps Services-docker-build](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Voor de bouw bewerking selecteert u uw Azure container Registry, de actie **een installatie kopie maken** en de Dockerfile die elke installatie kopie definieert. Stel de **Build-context** in als de hoofdmap van de Dockerfile en definieer de naam van de **installatie kopie**. 
    
    Zoals in het voor gaande scherm wordt weer gegeven, start u de naam van de installatie kopie met de URI van uw Azure container Registry. (U kunt ook een build-variabele gebruiken om de tag van de afbeelding te para meters, zoals de build-id in dit voor beeld).

1. Configureer voor elke installatie kopie een tweede stap die gebruikmaakt van de `docker push` opdracht.

    ![Azure DevOps Services-docker-push](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Voor de push-bewerking selecteert u uw Azure container Registry, de actie **een installatie kopie pushen** en voert u de naam van de **installatie kopie** in die in de vorige stap is ingebouwd.

1. Nadat u de stappen voor het bouwen en pushen voor elk van de vijf installatie kopieën hebt geconfigureerd, voegt u twee extra stappen toe aan de werk stroom bouwen.

    a. Een opdracht regel taak die gebruikmaakt van een bash-script om het *BuildNumber* -exemplaar in het bestand docker-Compose. yml te vervangen door de huidige build-id. Raadpleeg het volgende scherm voor meer informatie.

    ![Azure DevOps Services-update opstellen van bestand](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Een taak die het bijgewerkte opgebouwde bestand als een bouw artefact heeft neergezet zodat het in de release kan worden gebruikt. Raadpleeg het volgende scherm voor meer informatie.

    ![Azure DevOps Services-opstellen van opstel bestand publiceren](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. Klik op **Opslaan** en geef uw build-pijp lijn een naam.

## <a name="step-3-create-the-release-pipeline"></a>Stap 3: de release pijplijn maken

Met Azure DevOps Services kunt u [releases in meerdere omgevingen beheren](https://www.visualstudio.com/team-services/release-management/). U kunt continue implementatie inschakelen om ervoor te zorgen dat uw toepassing op een eenvoudige manier wordt geïmplementeerd in uw andere omgevingen (zoals dev, test, pre-productie en productie). U kunt een nieuwe omgeving maken die uw Azure Container Service docker Swarm-cluster vertegenwoordigt.

![Azure DevOps Services: release naar ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Installatie van de eerste release

1. Als u een release pijplijn wilt maken, klikt u op **Releases** >  **+ release**

1. Als u de artefact bron wilt configureren, klikt u op **artefacten** > **een artefact bron te koppelen**. Koppel deze nieuwe release pijplijn aan de build die u in de vorige stap hebt gedefinieerd. Op deze manier is het bestand docker-Compose. yml beschikbaar in het release proces.

    ![Azure DevOps Services-release artefacten](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. Als u de release trigger wilt configureren, klikt u op **Triggers** en selecteert u **doorlopende implementatie**. Stel de trigger in op dezelfde artefact bron. Deze instelling zorgt ervoor dat een nieuwe release wordt gestart zodra de build is voltooid.

    ![Azure DevOps Services-release triggers](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>De release werk stroom definiëren

De release werk stroom bestaat uit twee taken die u toevoegt.

1. Configureer een taak voor het veilig kopiëren van het opstellende bestand naar een *Deploy* -map op het knoop punt docker Swarm-Master, met behulp van de SSH-verbinding die u eerder hebt geconfigureerd. Raadpleeg het volgende scherm voor meer informatie.

    ![Azure DevOps Services-release-SCP](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Configureer een tweede taak om een bash-opdracht uit te voeren om `docker` en `docker-compose` opdrachten uit te voeren op het hoofd knooppunt. Raadpleeg het volgende scherm voor meer informatie.

    ![Azure DevOps Services-release bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    De opdracht die wordt uitgevoerd op het hoofd gebruik van de docker-CLI en de docker-CLI om de volgende taken uit te voeren:

   - Meld u aan bij het Azure container Registry (er worden drie build variab'les gebruikt die zijn gedefinieerd op het tabblad **variabelen** )
   - Definieer de **DOCKER_HOST** variabele die moet worden gebruikt voor het Swarm-eind punt (: 2375)
   - Ga naar de *Deploy* -map die is gemaakt door de vorige beveiligde Kopieer taak en die het docker-Compose. yml-bestand bevat 
   - Voer `docker-compose` opdrachten uit waarmee de nieuwe installatie kopieën worden opgehaald, stop de services, verwijder de services en maak de containers.

     >[!IMPORTANT]
     > Zoals u in het voor gaande scherm ziet, schakelt u het selectie vakje **mislukken op stderr** uit. Dit is een belang rijke instelling, omdat `docker-compose` verschillende diagnostische berichten afdrukt, zoals containers die worden gestopt of verwijderd, op de standaard fout uitvoer. Als u het selectie vakje inschakelt, wordt in azure DevOps Services gerapporteerd dat er fouten zijn opgetreden tijdens de release, zelfs als alles goed gaat.
     >
1. Sla deze nieuwe release pijplijn op.


>[!NOTE]
>Deze implementatie omvat enige downtime omdat de oude services worden gestopt en de nieuwe wordt uitgevoerd. U kunt dit voor komen door een Blue-groen implementatie uit te voeren.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Stap 4. De CI/CD-pijp lijn testen

Nu u klaar bent met de configuratie, is het tijd om deze nieuwe CI/CD-pijp lijn te testen. De eenvoudigste manier om dit te testen is het bijwerken van de bron code en het door voeren van de wijzigingen in uw GitHub-opslag plaats. Een paar seconden na het pushen van de code ziet u een nieuwe build die wordt uitgevoerd in azure DevOps Services. Zodra de implementatie is voltooid, wordt een nieuwe release geactiveerd en wordt de nieuwe versie van de toepassing op het Azure Container Service cluster geïmplementeerd.

## <a name="next-steps"></a>Volgende stappen

* Voor meer informatie over CI/CD met Azure DevOps Services raadpleegt u het artikel over [Azure pipelines](/azure/devops/pipelines/?view=azure-devops) .
