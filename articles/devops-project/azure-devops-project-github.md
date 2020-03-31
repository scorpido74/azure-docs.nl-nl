---
title: 'Zelfstudie: een CI/CD-pijplijn voor uw bestaande code maken met behulp van het Azure DevOps Projects'
description: Azure DevOps Projects zorgt ervoor dat u eenvoudig aan de slag kunt met Azure. In een paar korte stappen helpt DevOps Projects u uw eigen code en GitHub repo te gebruiken om een app op een Azure-service te starten.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/27/2019
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: a0496999a9a5dc3e9bfd57df0ec035e6db77d620
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73615128"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Zelfstudie: een CI/CD-pijplijn voor uw bestaande code maken met behulp van het Azure DevOps Projects

Azure DevOps Projects presenteert een vereenvoudigd proces voor het maken van een permanente integratie (CI) en cd-pijplijn (continuous delivery) naar Azure. U uw bestaande code en Git repo meenemen, of u een voorbeeldtoepassing selecteren.

U gaat het volgende doen:

> [!div class="checklist"]
> * DevOps Projects gebruiken om een CI/CD-pijplijn te maken
> * De toegang tot uw GitHub-opslagplaats configureren en een framework kiezen
> * Azure DevOps en een Azure-abonnement configureren 
> * Wijzigingen doorvoeren in GitHub en automatisch implementeren in Azure
> * De CI/CD-pijplijn van Azure Pipelines onderzoeken
> * Resources opschonen

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. U kunt er een gratis krijgen via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Toegang tot een GitHub of externe Git repo die .NET, Java, PHP, Node.js, Python of statische webcode bevat.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In Azure DevOps Projects wordt een CI/CD-pijplijn gemaakt in Azure Pipelines. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. Azure DevOps Projects maakt ook Azure-resources in het Azure-abonnement van uw keuze.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer in het menu azure portal de optie **Een resource maken**.

   ![Azure-portalmenu - Een bron maken](_img/azure-devops-project-github/createaresource.png)

3. Selecteer **DevOps** > **DevOps Project**.

   ![Het DevOps Projects-dashboard](_img/azure-devops-project-github/azuredashboard.png)

1. Selecteer **Neem uw eigen code mee** en selecteer **Volgende**.

## <a name="configure-access-to-your-github-repo-and-select-a-framework"></a>Toegang tot uw GitHub-repo configureren en een framework selecteren

1. Selecteer **GitHub** of een externe Git-coderepository. **Git** Selecteer **GitHub**voor deze zelfstudie . Mogelijk moet u de eerste keer met GitHub verifiëren om Azure toegang te geven tot uw GitHub-repository.

1. Selecteer een **opslagplaats** en een **branch**en selecteer **Volgende**.

1. Als u Docker-containers gebruikt, wijzigt **u De app Dockerized** in **JA.** Laat VOOR deze zelfstudie **GEEN** geselecteerd achter en selecteer **Volgende**. Voor meer informatie over het gebruik van **i** Docker containers, zweven over de i-pictogram.

   ![Selectie van toepassingskaders in vervolgkeuzemenu](_img/azure-devops-project-github/appframework.png)

1. Selecteer in de vervolgkeuzemenu's een **toepassingsruntime** en een **toepassingskader**en selecteer **Volgende**. Het toepassingskader bepaalt het type Azure-serviceimplementatiedoel dat beschikbaar is.

1. Selecteer een **Azure-service** om de toepassing te implementeren en selecteer **Volgende**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps en een Azure-abonnement configureren

1. Voer een naam in voor **Projectnaam**.

1. Maak een nieuwe gratis organisatie in **Azure DevOps Organization** of selecteer een bestaande organisatie in het vervolgkeuzemenu.

1. Selecteer uw abonnement in **Azure-abonnement**en voer een naam in **de web-app** in of gebruik de standaardinstelling. Selecteer een **locatie**en selecteer **Gereed**. Na enkele minuten wordt het implementatieoverzicht DevOps Projects weergegeven in de Azure-portal.

1. Selecteer **Ga naar resource om** het dashboard DevOps Projects weer te geven. In de rechterbovenhoek moet u het **project** vastmaken aan uw dashboard voor snelle toegang. Azure DevOps Projects configureert automatisch een CI-build en een versietrigger. Uw code blijft in uw GitHub-repo of een andere externe repo staan en een voorbeeld-app is ingesteld in een repo in **Azure DevOps Organization.** Azure DevOps Projects voert de build uit en implementeert de app naar Azure.

   ![Dashboardweergave Azure DevOps Projects](_img/azure-devops-project-github/projectsdashboard.png)

1. Het dashboard toont uw coderepo, uw CI/CD-pijplijn en uw app in Azure. Selecteer rechts onder Azure-bronnen de optie **Bladeren** om uw hardloopapp weer te geven.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Wijzigingen doorvoeren in GitHub en automatisch implementeren in Azure

U bent nu klaar om samen te werken aan uw app met een team. Het CI/CD-proces implementeert automatisch uw nieuwste werk op uw website. Elke wijziging in de GitHub repo start een build in Azure DevOps en een cd-pijplijn voert een implementatie uit naar Azure.

1. Selecteer **repositories**in het dashboard Van DevOps Projects. Uw GitHub-repository wordt geopend in een nieuw browsertabblad. Breng een wijziging aan in uw toepassing en selecteer **Wijzigingen vastleggen**.

1. Na enkele ogenblikken wordt een build in Azure Pipelines gestart. U de buildstatus controleren in het dashboard DevOps Projects. U deze ook controleren in uw Azure DevOps-organisatie door het tabblad **Pijplijnen bouwen** te selecteren in het dashboard DevOps Projects.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>De CI/CD-pijplijn van Azure Pipelines onderzoeken

In Azure DevOps Projects wordt automatisch een CI/CD-pijplijn geconfigureerd in Azure Pipelines. U kunt de pijplijn verkennen en zo nodig aanpassen. Ga als volgt te werk om vertrouwd te raken met de build- en release-pijplijnen:

1. Selecteer in het dashboard DevOps Projects de optie **Pijplijnen bouwen**.

1. Nadat de pagina **Azure Pipelines** is geopend, ziet u een geschiedenis van de meest recente builds en de status voor elke build.

   ![Azure Pipelines bouwt pagina](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. In de rechterbovenhoek van de pagina **Builds** u **Bewerken** selecteren om de huidige build, **Wachtrij** om een nieuwe build toe te voegen of de verticale ellipsknop** (&#8942;) **om een menu met meer opties te openen. Selecteer **Bewerken**.

1. De build doet verschillende taken, zoals het ophalen van bronnen uit de repo, het herstellen van afhankelijkheden en het publiceren van uitvoer voor implementaties. Rechts onder **Naam**de naam van de buildpijplijn wijzigen in iets meer beschrijvend. Selecteer **& wachtrij opslaan**en selecteer **Opslaan**. Voer een opmerking in en selecteer Opnieuw **opslaan.**

   ![Azure DevOps bouwt pagina](_img/azure-devops-project-github/buildpage.png)

1. Als u een controlespoor wilt zien van uw recente wijzigingen voor de build, selecteert u het tabblad **Historie.**  Azure DevOps houdt alle wijzigingen in de buildpijplijn bij en stelt u in staat versies te vergelijken.

1. Selecteer het tabblad **Triggers.** Azure DevOps Projects maakt automatisch een CI-trigger met enkele standaardinstellingen. U triggers instellen, zoals **Continue integratie inschakelen** om een build uit te voeren telkens wanneer u een codewijziging inschrijft. U ook triggers instellen om builds te plannen om op specifieke tijdstippen uit te voeren.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u geen Azure App Service meer nodig hebt en de bijbehorende resources die u in deze zelfstudie hebt gemaakt, u deze verwijderen. Gebruik de functionaliteit **Verwijderen** op het DevOps Projects-dashboard.

## <a name="next-steps"></a>Volgende stappen

Wanneer u uw CI/CD-proces in deze zelfstudie hebt geconfigureerd, hebt u automatisch een build- en releasepijplijn gemaakt in Azure DevOps-projecten. U kunt deze build- en release-pipelines desgewenst wijzigen in overeenstemming met de behoeften van uw team.

Voor meer informatie over de CI/CD-pijplijn, zie:

> [!div class="nextstepaction"]
> [Een CD-pijplijn (continue implementatie) met meerdere fasen definiëren](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

Zie voor meer informatie over toepassingsbewaking:
  
 > [!div class="nextstepaction"]
 > [Wat is Azure-monitor?](https://docs.microsoft.com/azure/azure-monitor/overview)
