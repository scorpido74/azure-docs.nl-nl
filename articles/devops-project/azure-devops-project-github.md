---
title: 'Zelfstudie: een CI/CD-pijplijn voor uw bestaande code maken met behulp van het Azure DevOps Projects'
description: Azure DevOps Projects zorgt ervoor dat u eenvoudig aan de slag kunt met Azure. DevOps Projects helpt u uw eigen code en GitHub-opslagplaats te gebruiken om slechts in enkele stappen een app op een Azure-service van uw keuze te starten.
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
ms.openlocfilehash: 4a48b5c413f6dfb58a1ba3f896ded49250cde306
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286280"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Zelfstudie: een CI/CD-pijplijn voor uw bestaande code maken met behulp van het Azure DevOps Projects

Azure DevOps Projects biedt een vereenvoudigde ervaring waar u uw bestaande code en Git-opslagplaats gebruikt of een voorbeeldtoepassing kiest voor het maken van een pijplijn voor CI (Continue integratie) en CD (Continue levering) naar Azure.

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
* Toegang tot een GitHub- of externe Git-opslagplaats met .NET, Java, PHP, Node, Python of statische webcode.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In Azure DevOps Projects wordt een CI/CD-pijplijn gemaakt in Azure Pipelines. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. Azure DevOps Projects maakt ook Azure-resources in het Azure-abonnement van uw keuze.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Selecteer in het linkerdeel venster **+ een resource maken** in de linkernavigatiebalk en zoek vervolgens naar **DevOps projects**en selecteer **maken**.

   ![Het DevOps Projects-dashboard](_img/azure-devops-project-github/azuredashboard.png)

3. Selecteer **uw eigen code nemen**en selecteer vervolgens **volgende**.

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>De toegang tot uw GitHub-opslagplaats configureren en een framework kiezen

1. Selecteer **github** of een externe **Git** -code opslagplaats. Voor deze zelf studie selecteert u **github**. Mogelijk moet u zich bij GitHub de eerste keer verifiëren om Azure toegang te geven tot uw GitHub-opslag plaats.

2. Voltooi door de **opslag plaats** en **vertakking**te selecteren en selecteer **volgende**.

3. Als u docker-containers wijzigt, **is app Dockerized** naar **Ja**, voor deze zelf studie **niets geselecteerd,** selecteert u **volgende**. Voor meer informatie over het gebruik van docker-containers, plaatst u de muis aanwijzer op het pictogram **i** .

   ![.NET Framework](_img/azure-devops-project-github/appframework.png)

4. Selecteer in de vervolg keuzelijsten de runtime en het **Framework**van de **toepassing** en selecteer vervolgens **volgende**. Het toepassings raamwerk dat u kiest, bepaalt het type Azure service-implementatie doel dat beschikbaar is.

5. Selecteer de **Azure-service** om de toepassing te implementeren en selecteer vervolgens **volgende**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps en een Azure-abonnement configureren

1. Voer een **project naam**in.

2. Maak een nieuwe gratis **Azure DevOps-organisatie** of kies een bestaande organisatie in de vervolg keuzelijst.

3. Selecteer uw **Azure-abonnement**, voer een naam in voor uw **Web-app** of haal de standaard waarde op. Selecteer een **locatie**en selecteer vervolgens **gereed**. Na een paar minuten wordt het overzicht van de DevOps-project implementatie weer gegeven in de Azure Portal.

4. Selecteer **naar resource gaan** om het DevOps-Project dashboard weer te geven. In de rechter bovenhoek kunt u het **project** vastmaken aan uw dash board voor snelle toegang. Azure DevOps Projects configureert automatisch een CI-build en een versietrigger. Uw code blijft in uw GitHub-opslagplaats of andere externe opslagplaats. Een voor beeld-app is ingesteld in een opslag plaats in uw **Azure DevOps-organisatie**. Er wordt een build uitgevoerd en uw app is geïmplementeerd in Azure.

   ![DevOps Projects-dashboardweergave](_img/azure-devops-project-github/projectsdashboard.png)

5. Het dashboard biedt meer inzicht in uw codeopslagplaats, CI/CD-pijplijn en app in Azure. Klik aan de rechter kant onder Azure-resources op **Bladeren** om de actieve app weer te geven.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Wijzigingen doorvoeren in GitHub en automatisch implementeren in Azure

U bent nu klaar om met een team samen te werken aan de app met behulp van een CI/CD-proces waarmee automatisch uw meest recente werk op uw website wordt geïmplementeerd. Bij elke wijziging in de GitHub-opslagplaats wordt een build gestart in Azure DevOps, en met een CD-pijplijn wordt een implementatie uitgevoerd in Azure.

1. Selecteer in uw DevOps project-dash board **opslag**plaatsen. Uw GitHub-opslag plaats wordt geopend in een nieuw browser tabblad. Breng een wijziging aan in de toepassing en klik vervolgens op **wijzigingen door voeren**.

2. Na enkele ogenblikken wordt een build in Azure Pipelines gestart. U kunt de status van de build in het dash board van DevOps Projects controleren of deze controleren in uw Azure DevOps-organisatie door het tabblad **pijp lijnen bouwen** te selecteren in het project dashboard.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>De CI/CD-pijplijn van Azure Pipelines onderzoeken

In Azure DevOps Projects wordt automatisch een CI/CD-pijplijn geconfigureerd in Azure Pipelines. U kunt de pijplijn verkennen en zo nodig aanpassen. Ga als volgt te werk om vertrouwd te raken met de build- en release-pijplijnen:

1. Selecteer in het dash board van DevOps Projects **Build-pijp lijnen**.

2. Zodra de pagina met **Azure-pijp lijnen** wordt geopend, ziet u een geschiedenis van de meest recente builds en de status voor elke build.

   ![Builds van Azure DevOps-pijp lijn](_img/azure-devops-project-github/pipelinesbuildpage.png)

3. In de rechter bovenhoek van de pagina **builds** ziet u opties voor het **bewerken** van de huidige build, **wachtrij** om een nieuwe build te maken en het beletsel **&#8942;** teken () om een menu met meer opties te openen, selecteert u **bewerken**.

4. De build voert verschillende taken uit, zoals het ophalen van bronnen uit de opslag plaats, het herstellen van afhankelijkheden en het publiceren van uitvoer voor implementaties. Wijzig aan de rechter kant onder **naam**de naam van de build-pijp lijn in een duidelijkere beschrijving. Selecteer **& wachtrij opslaan**, **Sla**het bestand op, sluit een opmerking en selecteer vervolgens opnieuw **Opslaan** .

   ![Pagina Azure DevOps builds](_img/azure-devops-project-github/buildpage.png)

5. Als u een audittrail van uw recente wijzigingen voor de build wilt zien, selecteert u het tabblad **geschiedenis** . Azure DevOps traceert alle wijzigingen die zijn aangebracht in de build-pijp lijn en stelt u in staat om versies te vergelijken.

6. Selecteer het tabblad **Triggers** . Azure DevOps project maakt automatisch een CI-trigger met enkele standaard instellingen. Triggers zoals het **inschakelen van continue integratie** kunnen worden ingesteld op het uitvoeren van een build telkens wanneer een code wijziging is doorgevoerd, of het plannen van builds op specifieke tijdstippen.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de Azure-app-service en de gerelateerde resources die u in deze zelfstudie hebt gemaakt, verwijderen wanneer u ze niet meer nodig hebt. Gebruik hiervoor de functionaliteit **Verwijderen** op het DevOps Projects-dashboard.

## <a name="next-steps"></a>Volgende stappen

Toen u het CI/CD-proces in deze quickstart configureerde, zijn er automatisch een build- en release-pijplijn in Azure DevOps Projects gemaakt. U kunt deze build- en release-pipelines desgewenst wijzigen in overeenstemming met de behoeften van uw team. U hebt geleerd hoe u:

> [!div class="checklist"]
>  * DevOps Projects gebruiken om een CI/CD-pijplijn te maken
> * De toegang tot uw GitHub-opslagplaats configureren en een framework kiezen
> * Azure DevOps en een Azure-abonnement configureren
> * Wijzigingen doorvoeren in GitHub en automatisch implementeren in Azure
> * De CI/CD-pijplijn van Azure Pipelines onderzoeken
> * Resources opschonen

Voor meer informatie over de CI/CD-pijplijn raadpleegt u:

> [!div class="nextstepaction"]
> [Een CD-pijplijn (continue implementatie) met meerdere fasen definiëren](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

Zie voor meer informatie over toepassings bewaking:
  
 > [!div class="nextstepaction"]
 > [Wat is Azure monitor?](https://docs.microsoft.com/azure/azure-monitor/overview)
