---
title: 'Zelf studie: een CI/CD-pijp lijn maken voor uw bestaande code met behulp van Azure DevOps starter'
description: Met Azure DevOps starter kunt u gemakkelijk aan de slag met Azure. DevOps Projects kunt u in een paar snelle stappen uw eigen code en GitHub opslag plaats gebruiken om een app te starten op een Azure-service.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: mvc
ms.openlocfilehash: 55c6cbc18e37368dd47c47227041024b13987c47
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233207"
---
# <a name="create-a-cicd-pipeline-for-github-repo-using-azure-devops-starter"></a>Een CI/CD-pijp lijn maken voor GitHub opslag plaats met behulp van Azure DevOps starter

Azure DevOps starter bevat een vereenvoudigd proces voor het maken van een doorlopende integratie (CI) en een doorlopende levering (CD)-pijp lijn naar Azure. U kunt uw bestaande code en git-opslag plaats meenemen, maar u kunt ook een voorbeeld toepassing selecteren.

U gaat het volgende doen:

> [!div class="checklist"]
> * DevOps starter gebruiken om een CI/CD-pijp lijn te maken
> * De toegang tot uw GitHub-opslagplaats configureren en een framework kiezen
> * Azure DevOps en een Azure-abonnement configureren 
> * Wijzigingen doorvoeren in GitHub en automatisch implementeren in Azure
> * De CI/CD-pijplijn van Azure Pipelines onderzoeken
> * Resources opschonen

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. U kunt er een gratis krijgen via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Toegang tot een GitHub of externe Git-opslag plaats die .NET, Java, PHP, node. js, python of static web code bevat.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Azure DevOps Starter maakt een CI/CD-pijp lijn in azure-pijp lijnen. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. Azure DevOps Starter maakt ook Azure-resources in het Azure-abonnement van uw keuze.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Typ **DevOps starter**in het zoekvak en selecteer. Klik op **toevoegen** om een nieuw item te maken.

    ![Het DevOps-starter dash board](_img/azure-devops-starter-aks/search-devops-starter.png)
    
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

1. Selecteer uw abonnement in het **Azure-abonnement**en voer een naam in de web- **app** in of gebruik de standaard waarde. Selecteer een **locatie**en selecteer vervolgens **gereed**. Na een paar minuten wordt het overzicht van de DevOps starter-implementatie weer gegeven in de Azure Portal.

1. Selecteer **naar resource gaan** om het DevOps starter-dash board weer te geven. In de rechter bovenhoek kunt u het **project** vastmaken aan uw dash board voor snelle toegang. Azure DevOps starter configureert automatisch een CI-build en release-trigger. Uw code blijft in uw GitHub-opslag plaats of een andere externe opslag plaats en een voor beeld-app wordt ingesteld in een opslag plaats in **Azure DevOps-organisatie**. Azure DevOps starter voert de build uit en implementeert de app in Azure.

   ![Azure DevOps Projects dashboard weergave](_img/azure-devops-project-github/projectsdashboard.png)

1. In het dash board worden uw code opslag plaats, uw CI/CD-pijp lijn en uw app in azure weer gegeven. Klik aan de rechter kant onder Azure-resources op **Bladeren** om de actieve app weer te geven.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Wijzigingen doorvoeren in GitHub en automatisch implementeren in Azure

U kunt nu aan uw app samen werken met een team. Het CI/CD-proces implementeert automatisch uw laatste werk op uw website. Elke wijziging van de GitHub opslag plaats start een build in azure DevOps en een CD-pijp lijn voert een implementatie naar Azure uit.

1. Selecteer in uw DevOps-starter dash board **opslag**plaatsen. Uw GitHub-opslag plaats wordt geopend in een nieuw browser tabblad. Maak een wijziging in uw toepassing en selecteer vervolgens **wijzigingen door voeren**.

1. Na enkele ogenblikken wordt een build in Azure Pipelines gestart. U kunt de status van de build in het DevOps-start dashboard bewaken. U kunt deze ook controleren in uw Azure DevOps-organisatie door het tabblad **pijp lijnen bouwen** te selecteren in het DevOps-starter-dash board.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>De CI/CD-pijplijn van Azure Pipelines onderzoeken

Azure DevOps starter configureert automatisch een CI/CD-pijp lijn in azure-pijp lijnen. U kunt de pijplijn verkennen en zo nodig aanpassen. Ga als volgt te werk om vertrouwd te raken met de build- en release-pijplijnen:

1. Selecteer in het DevOps-starter dash board **Build pijp lijnen**.

1. Nadat de pagina met **Azure-pijp lijnen** is geopend, ziet u een geschiedenis van de meest recente builds en de status voor elke build.

   ![Pagina met builds van Azure-pijp lijnen](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. In de rechter bovenhoek van de pagina **builds** kunt u **bewerken** selecteren om de huidige build te wijzigen, een **wachtrij** te maken om een nieuwe build toe te voegen of de knop met verticale beletsel tekens (**&#8942;**) om een menu met meer opties te openen. Selecteer **bewerken**.

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
