---
title: 'Zelfstudie: een CI/CD-pijplijn voor uw bestaande code maken met behulp van het Azure DevOps Projects'
description: Azure DevOps Projects zorgt ervoor dat u eenvoudig aan de slag kunt met Azure. DevOps Projects kunt u in een paar snelle stappen uw eigen code en GitHub opslag plaats gebruiken om een app te starten op een Azure-service.
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
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615128"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Zelfstudie: een CI/CD-pijplijn voor uw bestaande code maken met behulp van het Azure DevOps Projects

Azure DevOps Projects biedt een vereenvoudigd proces voor het maken van een doorlopende integratie (CI) en een doorlopende levering (CD)-pijp lijn naar Azure. U kunt uw bestaande code en git-opslag plaats meenemen, maar u kunt ook een voorbeeld toepassing selecteren.

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
* Toegang tot een GitHub of externe Git-opslag plaats die .NET, Java, PHP, node. js, python of static web code bevat.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In Azure DevOps Projects wordt een CI/CD-pijplijn gemaakt in Azure Pipelines. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. Azure DevOps Projects maakt ook Azure-resources in het Azure-abonnement van uw keuze.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

2. Selecteer in het menu Azure Portal de optie **een resource maken**.

   ![Azure Portal menu: een resource maken](_img/azure-devops-project-github/createaresource.png)

3. Selecteer **DevOps** > **DevOps-project**.

   ![Het DevOps Projects-dashboard](_img/azure-devops-project-github/azuredashboard.png)

1. Selecteer **Neem uw eigen code mee** en selecteer **Volgende**.

## <a name="configure-access-to-your-github-repo-and-select-a-framework"></a>Toegang tot uw GitHub-opslag plaats configureren en een framework selecteren

1. Selecteer **github** of een externe **Git** -code opslagplaats. Voor deze zelf studie selecteert u **github**. Mogelijk moet u zich bij GitHub de eerste keer verifiëren om Azure toegang te geven tot uw GitHub-opslag plaats.

1. Selecteer een **opslag plaats** en een **vertakking**en selecteer **volgende**.

1. Als u docker-containers gebruikt, wijzigt u **app Dockerized** in **Ja**. Voor deze zelf studie **mag** u niets selecteren en vervolgens op **volgende**klikken. Voor meer informatie over het gebruik van docker-containers, houdt u de muis aanwijzer over het pictogram **i** .

   ![Selectie van toepassings raamwerk in vervolg keuzemenu](_img/azure-devops-project-github/appframework.png)

1. Selecteer in de vervolg keuzelijst een runtime voor de **toepassing** en een **toepassings raamwerk**en selecteer vervolgens **volgende**. Het toepassings raamwerk bepaalt het type Azure service-implementatie doel dat beschikbaar is.

1. Selecteer een **Azure-service** om de toepassing te implementeren en selecteer vervolgens **volgende**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps en een Azure-abonnement configureren

1. Voer een naam in voor de **project naam**.

1. Maak een nieuwe gratis organisatie in de **Azure DevOps-organisatie** of selecteer een bestaande organisatie in de vervolg keuzelijst.

1. Selecteer uw abonnement in het **Azure-abonnement**en voer een naam in de web- **app** in of gebruik de standaard waarde. Selecteer een **locatie**en selecteer vervolgens **gereed**. Na een paar minuten wordt het DevOps Projects implementatie overzicht weer gegeven in de Azure Portal.

1. Selecteer **naar resource gaan** om het DevOps projects-dash board weer te geven. In de rechter bovenhoek kunt u het **project** vastmaken aan uw dash board voor snelle toegang. Azure DevOps Projects configureert automatisch een CI-build en een versietrigger. Uw code blijft in uw GitHub-opslag plaats of een andere externe opslag plaats en een voor beeld-app wordt ingesteld in een opslag plaats in **Azure DevOps-organisatie**. Azure DevOps Projects voert de build uit en implementeert de app in Azure.

   ![Azure DevOps Projects dashboard weergave](_img/azure-devops-project-github/projectsdashboard.png)

1. In het dash board worden uw code opslag plaats, uw CI/CD-pijp lijn en uw app in azure weer gegeven. Klik aan de rechter kant onder Azure-resources op **Bladeren** om de actieve app weer te geven.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Wijzigingen doorvoeren in GitHub en automatisch implementeren in Azure

U kunt nu aan uw app samen werken met een team. Het CI/CD-proces implementeert automatisch uw laatste werk op uw website. Elke wijziging van de GitHub opslag plaats start een build in azure DevOps en een CD-pijp lijn voert een implementatie naar Azure uit.

1. Selecteer in het dash board van DevOps Projects **opslag**plaatsen. Uw GitHub-opslag plaats wordt geopend in een nieuw browser tabblad. Maak een wijziging in uw toepassing en selecteer vervolgens **wijzigingen door voeren**.

1. Na enkele ogenblikken wordt een build in Azure Pipelines gestart. U kunt de status van de build controleren in het dash board van DevOps Projects. U kunt deze ook controleren in uw Azure DevOps-organisatie door het tabblad **pijp lijnen bouwen** te selecteren in het dash board van DevOps projects.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>De CI/CD-pijplijn van Azure Pipelines onderzoeken

In Azure DevOps Projects wordt automatisch een CI/CD-pijplijn geconfigureerd in Azure Pipelines. U kunt de pijplijn verkennen en zo nodig aanpassen. Ga als volgt te werk om vertrouwd te raken met de build- en release-pijplijnen:

1. Selecteer in het dash board van DevOps Projects **Build-pijp lijnen**.

1. Nadat de pagina met **Azure-pijp lijnen** is geopend, ziet u een geschiedenis van de meest recente builds en de status voor elke build.

   ![Pagina met builds van Azure-pijp lijnen](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. In de rechter bovenhoek van de pagina **builds** kunt u **bewerken** selecteren om de huidige build te wijzigen, een **wachtrij** te plaatsen om een nieuwe build toe te voegen of de knop **&#8942;** met verticale beletsel tekens () om een menu met meer opties te openen. Selecteer **Bewerken**.

1. De build voert diverse taken uit, zoals het ophalen van bronnen uit de opslag plaats, het herstellen van afhankelijkheden en het publiceren van uitvoer voor implementaties. Wijzig aan de rechter kant onder **naam**de naam van de build-pijp lijn in iets meer beschrijvende. Selecteer **& wachtrij opslaan**en selecteer vervolgens **Opslaan**. Voer een opmerking in en selecteer vervolgens **Opslaan** opnieuw.

   ![Pagina Azure DevOps builds](_img/azure-devops-project-github/buildpage.png)

1. Als u een audittrail van uw recente wijzigingen voor de build wilt zien, selecteert u het tabblad **geschiedenis** .  Azure DevOps traceert alle wijzigingen die zijn aangebracht in de build-pijp lijn en stelt u in staat om versies te vergelijken.

1. Selecteer het tabblad **Triggers** . Azure DevOps projects maakt automatisch een CI-trigger met enkele standaard instellingen. U kunt triggers instellen, zoals het **inschakelen van continue integratie** om elke keer dat u een code wijziging doorvoert, een build uit te voeren. U kunt ook triggers instellen voor het plannen van builds om op specifieke tijdstippen uit te voeren.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u Azure App Service en de gerelateerde resources die u in deze zelf studie hebt gemaakt, niet meer nodig hebt, kunt u deze verwijderen. Gebruik de functionaliteit **Verwijderen** op het DevOps Projects-dashboard.

## <a name="next-steps"></a>Volgende stappen

Wanneer u uw CI/CD-proces in deze zelf studie hebt geconfigureerd, hebt u automatisch een pijp lijn voor Build en release gemaakt in Azure DevOps Projects. U kunt deze build- en release-pipelines desgewenst wijzigen in overeenstemming met de behoeften van uw team.

Voor meer informatie over de CI/CD-pijplijn, zie:

> [!div class="nextstepaction"]
> [Een CD-pijplijn (continue implementatie) met meerdere fasen definiëren](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

Zie voor meer informatie over toepassings bewaking:
  
 > [!div class="nextstepaction"]
 > [Wat is Azure monitor?](https://docs.microsoft.com/azure/azure-monitor/overview)
