---
title: Continue implementatie inschakelen
description: Meer informatie over het inschakelen van CI/CD naar Azure App Service vanuit GitHub, BitBucket, Azure Repos of andere repo's. Selecteer de buildpijplijn die aan uw behoeften voldoet.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 08/23/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: d58eb333c930d2ffac4eb57340ea776338325181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266037"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Continue implementatie naar Azure App Service

[Azure App Service](overview.md) maakt continue implementatie mogelijk vanuit GitHub-, BitBucket- en [Azure Repos-repositories](https://azure.microsoft.com/services/devops/repos/) door de nieuwste updates in te voeren. In dit artikel ziet u hoe u de Azure-portal gebruiken om uw app continu te implementeren via de Kudu-buildservice of [Azure Pipelines.](https://azure.microsoft.com/services/devops/pipelines/) 

Zie [Een repo (GitHub)]maken, [Een repo maken (BitBucket)]of [Een nieuwe Git repo maken (Azure Repos)]voor meer informatie over de bronbeheerservices.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Azure-appservice autoriseren 

Als u Azure Repos wilt gebruiken, controleert u of uw Azure DevOps-organisatie is gekoppeld aan uw Azure-abonnement. Zie [Een Azure DevOps Services-account instellen zodat het kan worden geïmplementeerd in een web-app](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)voor meer informatie.

Voor Bitbucket of GitHub moet u Azure App Service autoriseren om verbinding te maken met uw opslagplaats. U hoeft slechts één keer te autoriseren met een bronbeheerservice. 

1. Zoek in de [Azure-portal](https://portal.azure.com)naar en selecteer **App Services**. 

   ![Zoeken naar App-services.](media/app-service-continuous-deployment/search-for-app-services.png)

1. Selecteer de web-app die u wilt implementeren.

   ![Selecteer uw app.](media/app-service-continuous-deployment/select-your-app.png)
   
1. Selecteer op de app-pagina **Implementatiecentrum** in het linkermenu.
   
1. Selecteer **github** of **bitbucket**op de pagina **Implementatiecentrum** en selecteer **Vervolgens Autoriseren**. 
   
   ![Selecteer bronbeheerservice en selecteer Vervolgens Autoriseren.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Meld u indien nodig aan bij de service en volg de autorisatieprompts. 

## <a name="enable-continuous-deployment"></a>Continue implementatie inschakelen 

Nadat u een bronbeheerservice hebt geautoriseerd, configureert u uw app voor continue implementatie via de ingebouwde [Kudu App Service-buildserver](#option-1-use-the-app-service-build-service)of via [Azure Pipelines.](#option-2-use-azure-pipelines) 

### <a name="option-1-use-the-app-service-build-service"></a>Optie 1: De app-service gebruiken om te bouwen

U de ingebouwde Kudu App Service-buildserver gebruiken om continu te implementeren vanuit GitHub, Bitbucket of Azure Repos. 

1. Zoek en selecteer **app-services**in de [Azure-portal](https://portal.azure.com)en selecteer vervolgens de web-app die u wilt implementeren. 
   
1. Selecteer op de app-pagina **Implementatiecentrum** in het linkermenu.
   
1. Selecteer de geautoriseerde bronbesturingselementprovider op de pagina **Implementatiecentrum** en selecteer **Doorgaan**. Voor GitHub of Bitbucket u ook **Account wijzigen** selecteren om het geautoriseerde account te wijzigen. 
   
   > [!NOTE]
   > Als u Azure Repos wilt gebruiken, controleert u of uw Azure DevOps Services-organisatie is gekoppeld aan uw Azure-abonnement. Zie [Een Azure DevOps Services-account instellen zodat het kan worden geïmplementeerd in een web-app](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)voor meer informatie.
   
1. Selecteer voor GitHub of Azure Repos op de pagina **App-service build service**en selecteer **Build provider** **Doorgaan**. Bitbucket maakt altijd gebruik van de App Service build service.
   
   ![Selecteer de app-service voor het opbouwen van apps en selecteer Doorgaan.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. Op de pagina **Configureren:**
   
   - Val voor GitHub de **optie Organisatie,** **Repository**en Branch in en **selecteer** deze u continu wilt implementeren.
     
     > [!NOTE]
     > Als u geen opslagplaatsen ziet, moet u mogelijk Azure App Service autoriseren in GitHub. Blader naar uw GitHub-opslagplaats en ga naar **Instellingen** > **toepassingen** > **geautoriseerde OAuth Apps**. Selecteer **Azure App Service**en selecteer Vervolgens **Verlenen**. Voor organisatierepositories moet u eigenaar zijn van de organisatie om de machtigingen te verlenen.
     
   - Selecteer voor Bitbucket het **Bitbucket-team,** **repository**en **branch dat** u continu wilt implementeren.
     
   - Selecteer voor Azure Repos de **Azure DevOps Organization,** **Project**, **Repository**en **Branch** die u continu wilt implementeren.
     
     > [!NOTE]
     > Als uw Azure DevOps-organisatie niet wordt vermeld, controleert u of deze is gekoppeld aan uw Azure-abonnement. Zie [Een Azure DevOps Services-account instellen zodat het kan worden geïmplementeerd in een web-app](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)..
     
1. Selecteer **Doorgaan**.
   
   ![Vul repository-gegevens in en selecteer Doorgaan.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. Nadat u de buildprovider hebt geconfigureerd, controleert u de instellingen op de **pagina Overzicht** en selecteert u **Voltooien**.
   
   Nieuwe commits in de geselecteerde repository en branch worden nu continu geïmplementeerd in je App Service-app. U de commits en implementaties volgen op de pagina **Implementatiecentrum.**
   
   ![Commits en implementaties bijhouden in implementatiecentrum](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>Optie 2: Azure-pijplijnen gebruiken 

Als uw account over de benodigde machtigingen beschikt, u Azure Pipelines instellen om continu te implementeren vanuit GitHub- of Azure Repos-repositories. Zie [Een web-app implementeren voor Azure App Services voor](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)meer informatie over het implementeren via Azure Pipelines.

Azure App Service wilt azure-pijplijnen voor continue levering maken in uw Azure DevOps-organisatie: 

- Uw Azure-account moet machtigingen hebben om naar Azure Active Directory te schrijven en een service te maken. 
  
- Uw Azure-account moet de **rol Eigenaar** hebben in uw Azure-abonnement.

- U moet een beheerder zijn in het Azure DevOps-project dat u wilt gebruiken.

Azure Pipelines configureren (voorbeeld:

1. Zoek en selecteer **app-services**in de [Azure-portal](https://portal.azure.com)en selecteer vervolgens de web-app die u wilt implementeren. 
   
1. Selecteer op de app-pagina **Implementatiecentrum** in het linkermenu.
   
1. Selecteer op de pagina Azure **Pipelines (Preview)** op **de** pagina Azure Pipelines en selecteer **Doorgaan**. 
   
1. Ga **op** de pagina Configureren in de sectie **Code:**
   
   - Val voor GitHub de **optie Organisatie,** **Repository**en Branch in en **selecteer** deze u continu wilt implementeren.
     
     > [!NOTE]
     > Als u geen opslagplaatsen ziet, moet u mogelijk Azure App Service autoriseren in GitHub. Blader naar uw GitHub-opslagplaats en ga naar **Instellingen** > **toepassingen** > **geautoriseerde OAuth Apps**. Selecteer **Azure App Service**en selecteer Vervolgens **Verlenen**. Voor organisatierepositories moet u eigenaar zijn van de organisatie om de machtigingen te verlenen.
     
   - Selecteer voor Azure Repos de **Azure DevOps Organization,** **Project,** **Repository**en **Branch** die u continu wilt implementeren of configureer een nieuwe Azure DevOps-organisatie.
     
     > [!NOTE]
     > Als uw bestaande Azure DevOps-organisatie niet wordt vermeld, moet u deze mogelijk koppelen aan uw Azure-abonnement. Zie [Uw cd-releasepijplijn definiëren](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)voor meer informatie .
     
1. Selecteer **Doorgaan**.
   
1. Geef voor Azure Repos in de sectie **Build** het taalkader op dat Azure Pipelines moeten gebruiken om buildtaken uit te voeren en selecteer **Doorgaan**.
   
1. Kies **op** de pagina Testen of u belastingstests wilt inschakelen en selecteer **Doorgaan**.
   
1. Afhankelijk van de [prijscategorie app-service-abonnement](https://azure.microsoft.com/pricing/details/app-service/plans/)ziet u mogelijk een pagina **Implementeren naar fasering.** Kies of [u implementatieruimten wilt inschakelen](deploy-staging-slots.md)en selecteer **Doorgaan**.
   
   > [!NOTE]
   > Azure Pipelines staat geen continue levering aan de productiesleuf toe. Deze beperking voorkomt onbedoelde implementaties naar de productie. Stel continue levering in een faseringssleuf in, controleer de wijzigingen daar en wissel de sleuven wanneer u er klaar voor bent.
   
1. Nadat u de buildprovider hebt geconfigureerd, controleert u de instellingen op de **pagina Overzicht** en selecteert u **Voltooien**.
   
   Nieuwe commits in de geselecteerde repository en branch worden nu continu geïmplementeerd in je App Service-app. U de commits en implementaties volgen op de pagina **Implementatiecentrum.**
   
   ![Commits en implementaties bijhouden in implementatiecentrum](media/app-service-continuous-deployment/github-finished.png)

## <a name="disable-continuous-deployment"></a>Continue implementatie uitschakelen

Als u continue implementatie wilt uitschakelen, selecteert u **Verbinding verbreken** boven aan de **pagina Implementatiecentrum** van uw app.

![Continue implementatie uitschakelen](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>Niet-ondersteunde repo's gebruiken

Voor Windows-apps u handmatig continue implementatie configureren vanuit een cloud Git- of Mercurial-repository die de portal niet rechtstreeks ondersteunt, zoals [GitLab.](https://gitlab.com/) U doet dit door het vak Extern te kiezen op de pagina **Implementatiecentrum.** Zie [Continue implementatie instellen met handmatige stappen](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Veelvoorkomende problemen met continue implementatie onderzoeken](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Azure PowerShell gebruiken](/powershell/azureps-cmdlets-docs)
* [Git-documentatie](https://git-scm.com/documentation)
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)

[Een repo maken (GitHub)]: https://help.github.com/articles/create-a-repo
[Een repo maken (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Een nieuwe Git-repo maken (Azure Repos)]: /azure/devops/repos/git/creatingrepo
