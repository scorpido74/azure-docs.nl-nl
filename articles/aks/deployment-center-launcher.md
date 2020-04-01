---
title: Implementatiecentrum voor Azure Kubernetes
description: Implementatiecentrum in Azure DevOps vereenvoudigt het instellen van een robuuste Azure DevOps-pijplijn voor uw toepassing
ms.author: puagarw
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
ms.openlocfilehash: b0a9597e370648faab3787218c7d038798dbd455
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048112"
---
# <a name="deployment-center-for-azure-kubernetes"></a>Implementatiecentrum voor Azure Kubernetes

Deployment Center in Azure DevOps vereenvoudigt het instellen van een robuuste Azure DevOps-pijplijn voor uw toepassing. Implementatiecentrum configureert standaard een Azure DevOps-pijplijn om uw toepassingsupdates te implementeren in het Kubernetes-cluster. U de standaard geconfigureerde Azure DevOps-pijplijn uitbreiden en ook uitgebreidere mogelijkheden toevoegen: de mogelijkheid om goedkeuring te krijgen voordat u deze implementeert, extra Azure-bronnen intevoorzien, scripts uit te voeren, uw toepassing te upgraden en zelfs meer validatietests uit te voeren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Configureer een Azure DevOps-pijplijn om uw toepassingsupdates te implementeren in het Kubernetes-cluster.
> * Bestudeer de ci-pijplijn (continuous integration).
> * Bestudeer de continue delivery (CD) pijplijn.
> * Ruim de grondstoffen op.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. U kunt er een gratis krijgen via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

* Een AKS-cluster (Azure Kubernetes Service)

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

1. Meld u aan bij uw [Azure-portal.](https://portal.azure.com/)

1. Selecteer de optie [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) aan de rechterkant van de menubalk in de Azure-portal.

1. Voer de volgende opdrachten uit om het AKS-cluster te maken:

    ```azurecli
    # Create a resource group in the South India location:

    az group create --name azooaks --location southindia

    # Create a cluster named azookubectl with one node.

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-a-kubernetes-cluster"></a>Toepassingsupdates implementeren voor een Kubernetes-cluster

1. Ga naar de resourcegroep die u in de vorige sectie hebt gemaakt.

1. Selecteer het AKS-cluster en selecteer **vervolgens Implementatiecentrum (voorbeeld)** op het linkerblad. Selecteer **Aan de slag**.

   ![instellingen](media/deployment-center-launcher/settings.png)

1. Kies de locatie van de code en selecteer **Volgende**. Selecteer vervolgens een van de momenteel ondersteunde opslagplaatsen: **[Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)** of **GitHub**.

    Azure Repos is een set versiebeheertools waarmee u uw code beheren. Of uw softwareproject nu groot of klein is, het is een goed idee om zo vroeg mogelijk versiebeheer te gebruiken.

    - **Azure Repos:** kies een opslagplaats uit uw bestaande project en organisatie.

        ![Azure-opslagplaatsen](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**: De opslagplaats voor uw GitHub-account autoriseren en selecteren.

        ![GitHub](media/deployment-center-launcher/github.gif)


1. Deployment Center analyseert de repository en detecteert uw Dockerfile. Als u het Dockerbestand wilt bijwerken, u het geïdentificeerde poortnummer bewerken.

    ![Toepassingsinstellingen](media/deployment-center-launcher/application-settings.png)

    Als de repository het Dockerfile niet bevat, wordt er in het systeem een bericht weergegeven om er een te maken.

    ![Dockerbestand](media/deployment-center-launcher/dockerfile.png)

1. Selecteer een bestaand containerregister of maak er een en selecteer **Voltooien**. De pijplijn wordt automatisch gemaakt en een build in [Azure Pipelines wachtrijen](https://docs.microsoft.com/azure/devops/pipelines/index?view=azure-devops).

    Azure Pipelines is een cloudservice die u gebruiken om uw codeproject automatisch te bouwen en te testen en beschikbaar te maken voor andere gebruikers. Azure Pipelines combineert continue integratie en continue levering om uw code voortdurend en consequent te testen en te bouwen en deze naar elk doel te verzenden.

    ![Container Registry](media/deployment-center-launcher/container-registry.png)

1. Selecteer de koppeling om de doorlopende pijplijn te bekijken.

1. U ziet de geslaagde logboeken nadat de implementatie is voltooid.

    ![Logboeken](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>De CI-pijplijn onderzoeken

Deployment Center configureert automatisch de CI/CD-pijplijn van uw Azure DevOps-organisatie. De pijplijn kan worden verkend en aangepast.

1. Ga naar het dashboard van het implementatiecentrum.  

1. Selecteer het buildnummer in de lijst met geslaagde logboeken om de buildpijplijn voor uw project weer te geven.

1. Selecteer de ellips (...) in de rechterbovenhoek. Een menu toont verschillende opties, zoals het in de rij zetten van een nieuwe build, het behouden van een build en het bewerken van de buildpijplijn. Selecteer **Pijplijn bewerken**. 

1. U de verschillende taken voor uw buildpijplijn in dit deelvenster bekijken. De build voert verschillende taken uit, zoals het verzamelen van bronnen uit de Git-opslagplaats, het maken van een afbeelding, het pushen van een afbeelding naar het containerregister en het publiceren van uitvoer die worden gebruikt voor implementaties.

1. Selecteer de naam van de buildpijplijn boven aan de pijplijn.

1. Wijzig de naam van uw buildpijplijn in iets beschrijvender, selecteer **& wachtrij opslaan**en selecteer **Opslaan**.

1. Selecteer **Geschiedenis**onder uw buildpijplijn . In dit deelvenster ziet u een controlespoor van uw recente wijzigingen in de build. Azure DevOps controleert alle wijzigingen in de buildpijplijn en stelt u in staat versies te vergelijken.

1. Selecteer **Triggers**. U branches opnemen of uitsluiten van het CI-proces.

1. Selecteer **Retentie**. U beleidsregels opgeven om een aantal builds te behouden of te verwijderen, afhankelijk van uw scenario.

## <a name="examine-the-cd-pipeline"></a>De CD-pijplijn onderzoeken

Deployment Center maakt en configureert automatisch de relatie tussen uw Azure DevOps-organisatie en uw Azure-abonnement. De stappen omvatten het instellen van een Azure-serviceverbinding om uw Azure-abonnement te verifiëren met Azure DevOps. Het geautomatiseerde proces maakt ook een releasepijplijn, die continue levering aan Azure biedt.

1. Selecteer **Pijplijnen**en selecteer **Releases**.

1. Als u de releasepijplijn wilt bewerken, selecteert u **Bewerken**.

1. Selecteer **Neerzetten** in de lijst **Artefacten.** In de vorige stappen produceert de bouwpijplijn die u hebt onderzocht de uitvoer die voor het artefact wordt gebruikt. 

1. Selecteer de **trigger voor continue implementatie** rechts van de optie **Drop.** Deze releasepijplijn heeft een ingeschakelde cd-trigger die een implementatie uitvoert wanneer er een nieuw buildartefact beschikbaar is. U de trigger ook uitschakelen om handmatige uitvoering voor uw implementaties te vereisen.

1. Als u alle taken voor uw pijplijn wilt onderzoeken, selecteert u **Taken**. De release stelt de helmstokomgeving `imagePullSecrets` in, configureert de parameter, installeert Helm-tools en implementeert de Helm-diagrammen in het Kubernetes-cluster.

1. Als u de releasegeschiedenis wilt weergeven, selecteert u **Releases weergeven**.

1. Als u het overzicht wilt bekijken, selecteert u **Vrijgeven**. Selecteer een van de fasen om meerdere menu's te verkennen, zoals een releaseoverzicht, bijbehorende werkitems en tests. 

1. Selecteer **Doorvoeringen**. In deze weergave worden codecommits weergegeven die betrekking hebben op deze implementatie. Vergelijk releases om de verschillen tussen implementaties te zien.

1. Selecteer **Logboeken**. De logboeken bevatten nuttige implementatie-informatie, die u tijdens en na implementaties weergeven.

## <a name="clean-up-resources"></a>Resources opschonen

U de gerelateerde bronnen verwijderen die u hebt gemaakt wanneer u ze niet meer nodig hebt. Gebruik de verwijderfunctionaliteit op het dashboard DevOps Projects.

## <a name="next-steps"></a>Volgende stappen

U kunt deze build- en release-pipelines desgewenst wijzigen in overeenstemming met de behoeften van uw team. U dit CI/CD-model ook gebruiken als sjabloon voor uw andere pijplijnen.
