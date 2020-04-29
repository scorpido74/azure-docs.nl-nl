---
title: Implementatie centrum voor Azure Kubernetes
description: Implementatie centrum in azure DevOps vereenvoudigt het instellen van een robuuste Azure DevOps-pijp lijn voor uw toepassing
ms.author: puagarw
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
ms.openlocfilehash: b0a9597e370648faab3787218c7d038798dbd455
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80048112"
---
# <a name="deployment-center-for-azure-kubernetes"></a>Implementatie centrum voor Azure Kubernetes

Implementatie centrum in azure DevOps vereenvoudigt het instellen van een robuuste Azure DevOps-pijp lijn voor uw toepassing. Met het implementatie centrum wordt standaard een Azure DevOps-pijp lijn geconfigureerd om uw toepassings updates te implementeren op het Kubernetes-cluster. U kunt de standaard geconfigureerde Azure DevOps-pijp lijn uitbreiden en ook rijkere mogelijkheden toevoegen: de mogelijkheid om goed keuring te verkrijgen voordat u implementeert, extra Azure-resources te implementeren, scripts uit te voeren, uw toepassing bij te werken en zelfs meer validatie tests uit te voeren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Configureer een Azure DevOps-pijp lijn om uw toepassings updates te implementeren op het Kubernetes-cluster.
> * Controleer de CI-pijp lijn (continue integratie).
> * Bekijk de pijp lijn voor continue levering (CD).
> * De resources opschonen.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. U kunt er een gratis krijgen via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

* Een Azure Kubernetes service-cluster (AKS).

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

1. Meld u aan bij uw [Azure Portal](https://portal.azure.com/).

1. Selecteer de optie [Cloud shell](https://docs.microsoft.com/azure/cloud-shell/overview) aan de rechter kant van de menu balk in de Azure Portal.

1. Voer de volgende opdrachten uit om het AKS-cluster te maken:

    ```azurecli
    # Create a resource group in the South India location:

    az group create --name azooaks --location southindia

    # Create a cluster named azookubectl with one node.

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-a-kubernetes-cluster"></a>Toepassings updates implementeren in een Kubernetes-cluster

1. Ga naar de resource groep die u in de vorige sectie hebt gemaakt.

1. Selecteer het AKS-cluster en selecteer vervolgens **Deployment Center (preview)** op de Blade links. Selecteer **aan de slag**.

   ![instellingen](media/deployment-center-launcher/settings.png)

1. Kies de locatie van de code en selecteer **volgende**. Selecteer vervolgens een van de momenteel ondersteunde opslag plaatsen: **[Azure opslag plaatsen](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)** of **github**.

    Azure opslag plaatsen is een set hulpprogram ma's voor versie beheer waarmee u uw code kunt beheren. Of uw software project groot of klein is, met behulp van versie beheer zo snel mogelijk is een goed idee.

    - **Azure-opslag plaatsen**: Kies een opslag plaats in uw bestaande project en organisatie.

        ![Azure-opslagplaatsen](media/deployment-center-launcher/azure-repos.gif)

    - **Github**: autoriseer en selecteer de opslag plaats voor uw github-account.

        ![GitHub](media/deployment-center-launcher/github.gif)


1. Het implementatie Centrum analyseert de opslag plaats en detecteert uw Dockerfile. Als u de Dockerfile wilt bijwerken, kunt u het geïdentificeerde poort nummer bewerken.

    ![Toepassingsinstellingen](media/deployment-center-launcher/application-settings.png)

    Als de opslag plaats de Dockerfile niet bevat, wordt in het systeem een bericht weer gegeven dat er een moet worden doorgevoerd.

    ![Dockerfile](media/deployment-center-launcher/dockerfile.png)

1. Selecteer een bestaand container register of maak er een, en selecteer vervolgens **volt ooien**. De pijp lijn wordt automatisch gemaakt en wacht op een build in [Azure-pijp lijnen](https://docs.microsoft.com/azure/devops/pipelines/index?view=azure-devops).

    Azure-pijp lijnen is een Cloud service die u kunt gebruiken om automatisch uw code project te bouwen en te testen en deze beschikbaar te maken voor andere gebruikers. Azure-pijp lijnen combi neren doorlopende integratie en doorlopende levering om voortdurend en consistent te testen en uw code te bouwen en te verzenden naar een doel.

    ![Container Registry](media/deployment-center-launcher/container-registry.png)

1. Selecteer de koppeling om de actieve pijp lijn weer te geven.

1. U ziet de geslaagde Logboeken nadat de implementatie is voltooid.

    ![Logboeken](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>De CI-pijplijn onderzoeken

Het implementatie centrum configureert automatisch de CI/CD-pijp lijn van uw Azure DevOps-organisatie. De pijp lijn kan worden verkend en aangepast.

1. Ga naar het dash board van het implementatie centrum.  

1. Selecteer het buildnummer in de lijst met geslaagde Logboeken om de build-pijp lijn voor uw project weer te geven.

1. Selecteer in de rechter bovenhoek het beletsel teken (...). Een menu bevat verschillende opties, zoals een nieuwe build in een wachtrij plaatsen, een build behouden en de build-pijp lijn bewerken. Selecteer **pijp lijn bewerken**. 

1. In dit deel venster kunt u de verschillende taken voor uw build-pijp lijn onderzoeken. De build voert verschillende taken uit, zoals het verzamelen van bronnen uit de Git-opslag plaats, het maken van een installatie kopie, het pushen van een installatie kopie naar het container register en het publiceren van uitvoer die wordt gebruikt voor implementaties.

1. Selecteer de naam van de build-pijp lijn aan de bovenkant van de pijp lijn.

1. Wijzig de naam van uw build-pijp lijn in een beschrijvende, selecteer **& wachtrij opslaan**en selecteer vervolgens **Opslaan**.

1. Selecteer de optie **geschiedenis**onder uw build-pijp lijn. Dit deel venster toont een audittrail van uw recente wijzigingen in de build. Azure DevOps controleert alle wijzigingen die zijn aangebracht in de build-pijp lijn en maakt het u mogelijk om versies te vergelijken.

1. Selecteer **Triggers**. U kunt vertakkingen opnemen in of uitsluiten van het CI-proces.

1. Selecteer **Retentie**. U kunt beleids regels opgeven om een aantal builds te hand haven of te verwijderen, afhankelijk van uw scenario.

## <a name="examine-the-cd-pipeline"></a>De CD-pijplijn onderzoeken

Het implementatie centrum maakt en configureert automatisch de relatie tussen uw Azure DevOps-organisatie en uw Azure-abonnement. De volgende stappen zijn van toepassing op het instellen van een Azure-service verbinding om uw Azure-abonnement te verifiëren met Azure DevOps. Tijdens het geautomatiseerde proces wordt ook een release pijplijn gemaakt die continue levering aan Azure biedt.

1. Selecteer **pijp lijnen**en selecteer vervolgens **releases**.

1. Als u de release pijplijn wilt bewerken, selecteert u **bewerken**.

1. Selecteer **neerzetten** in de lijst **artefacten** . In de vorige stappen produceert de bouw pijp lijn die u hebt onderzocht de uitvoer die wordt gebruikt voor het artefact. 

1. Selecteer de trigger voor **continue implementatie** aan de rechter kant van de optie voor **neerzetten** . Deze release pijplijn heeft een ingeschakelde CD-trigger die een implementatie uitvoert wanneer er een nieuw build-artefact beschikbaar is. U kunt de trigger ook uitschakelen om hand matige uitvoering van uw implementaties te vereisen.

1. Selecteer **taken**om alle taken voor de pijp lijn te controleren. De release stelt de Tiller omgeving in, configureert de `imagePullSecrets` para meter, installeert helm-hulpprogram ma's en implementeert de helm-grafieken naar het Kubernetes-cluster.

1. Selecteer **releases weer geven**om de release geschiedenis weer te geven.

1. Selecteer **vrijgeven**om de samen vatting te bekijken. Selecteer een van de fasen om meerdere menu's te verkennen, zoals een release samenvatting, gekoppelde werk items en tests. 

1. Selecteer **Doorvoeringen**. In deze weer gave worden code doorvoer bewerkingen weer gegeven die betrekking hebben op deze implementatie. Vergelijkings versies om de doorvoer verschillen tussen implementaties te bekijken.

1. Selecteer **Logboeken**. De logboeken bevatten nuttige informatie over de implementatie, die u tijdens en na de implementaties kunt bekijken.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de gerelateerde resources die u hebt gemaakt, verwijderen wanneer u ze niet meer nodig hebt. Gebruik de functie verwijderen op het dash board van DevOps Projects.

## <a name="next-steps"></a>Volgende stappen

U kunt deze build- en release-pipelines desgewenst wijzigen in overeenstemming met de behoeften van uw team. U kunt dit CI/CD-model ook gebruiken als een sjabloon voor uw andere pijp lijnen.
