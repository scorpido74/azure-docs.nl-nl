---
title: Continue implementatie inschakelen
description: Meer informatie over het inschakelen van CI/CD-Azure App Service van GitHub, BitBucket, Azure opslag plaatsen of een andere opslag plaatsen. Selecteer de build-pijp lijn die aan uw behoeften voldoet.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 08/23/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 692b07c82c329a93d79ad3a87beec5dbe1c595d3
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74669983"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Continue implementatie naar Azure App Service

[Azure app service](overview.md) maakt continue implementatie mogelijk van github-, BitBucket-en [Azure opslag plaatsen](https://azure.microsoft.com/services/devops/repos/) -opslag plaatsen door de nieuwste updates op te halen. In dit artikel wordt beschreven hoe u de Azure Portal kunt gebruiken om uw app voortdurend te implementeren via de kudu build-service of [Azure-pijp lijnen](https://azure.microsoft.com/services/devops/pipelines/). 

Zie [Een repo maken (GitHub)], [Een repo maken (BitBucket)]maken of [een nieuwe Git-opslag plaats maken (Azure opslag plaatsen)]voor meer informatie over de bron beheer Services.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Azure App Service autoriseren 

Als u Azure opslag plaatsen wilt gebruiken, moet u ervoor zorgen dat uw Azure DevOps-organisatie is gekoppeld aan uw Azure-abonnement. Zie [een Azure DevOps Services-account instellen zodat het kan worden geïmplementeerd in een web-app](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)voor meer informatie.

Autoriseer Azure App Service voor bitbucket of GitHub om verbinding te maken met uw opslag plaats. U hoeft slechts één keer te autoriseren met een broncode beheer service. 

1. Selecteer **app Services** in het [Azure Portal](https://portal.azure.com) linkernavigatiebalk en selecteer vervolgens de web-app die u wilt implementeren. 
   
1. Selecteer op de pagina app het menu **implementatie centrum** in het linkermenu.
   
1. Selecteer op de pagina **implementatie centrum** **github** of **bitbucket**, en selecteer vervolgens **autoriseren**. 
   
   ![Selecteer broncode beheer service en selecteer vervolgens autoriseren.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Meld u indien nodig aan bij de service en volg de autorisatie prompts. 

## <a name="enable-continuous-deployment"></a>Continue implementatie inschakelen 

Nadat u een broncode beheer service hebt geautoriseerd, configureert u uw app voor continue implementatie via de ingebouwde [kudu-app service build-server](#option-1-use-the-app-service-build-service)of via [Azure-pijp lijnen](#option-2-use-azure-pipelines). 

### <a name="option-1-use-the-app-service-build-service"></a>Optie 1: de App Service build-service gebruiken

U kunt de ingebouwde kudu-App Service build-server gebruiken om voortdurend te implementeren vanuit GitHub, bitbucket of Azure opslag plaatsen. 

1. Selecteer **app Services** in het [Azure Portal](https://portal.azure.com) linkernavigatiebalk en selecteer vervolgens de web-app die u wilt implementeren. 
   
1. Selecteer op de pagina app het menu **implementatie centrum** in het linkermenu.
   
1. Selecteer uw geautoriseerde broncode beheer provider op de pagina **implementatie centrum** en selecteer **door gaan**. Voor GitHub of bitbucket kunt u ook **account wijzigen** selecteren om het geautoriseerde account te wijzigen. 
   
   > [!NOTE]
   > Als u Azure opslag plaatsen wilt gebruiken, moet u ervoor zorgen dat uw Azure DevOps Services-organisatie is gekoppeld aan uw Azure-abonnement. Zie [een Azure DevOps Services-account instellen zodat het kan worden geïmplementeerd in een web-app](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)voor meer informatie.
   
1. Voor GitHub of Azure opslag plaatsen selecteert u op de pagina **Build** -provider **app service build-service**en selecteert u vervolgens **door gaan**. Bitbucket maakt altijd gebruik van de App Service build-service.
   
   ![Selecteer App Service build-service en selecteer vervolgens door gaan.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. Op de pagina **configureren** :
   
   - Voor GitHub, vervolg keuzelijst en selecteer de **organisatie**, **opslag plaats**en **vertakking** die u continu wilt implementeren.
     
     > [!NOTE]
     > Als u geen opslag plaatsen ziet, moet u mogelijk Azure App Service autoriseren in GitHub. Blader naar de GitHub-opslag plaats en ga naar **instellingen** > **toepassingen** > **geautoriseerde OAuth-apps**. Selecteer **Azure app service**en selecteer vervolgens **verlenen**.
     
   - Selecteer voor bitbucket het bitbucket- **team**, de **opslag plaats**en de **vertakking** die u continu wilt implementeren.
     
   - Selecteer voor Azure opslag plaatsen de **Azure DevOps-organisatie**, **het project**, de **opslag plaats**en de **vertakking** die u continu wilt implementeren.
     
     > [!NOTE]
     > Als uw Azure DevOps-organisatie niet wordt weer gegeven, controleert u of deze is gekoppeld aan uw Azure-abonnement. Zie [een Azure DevOps Services-account instellen zodat het kan worden geïmplementeerd in een web-app](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)voor meer informatie.
     
1. Selecteer **Doorgaan**.
   
   ![Vul de gegevens van de opslag plaats in en selecteer door gaan.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. Nadat u de build-provider hebt geconfigureerd, controleert u de instellingen op de pagina **samen vatting** en selecteert u vervolgens **volt ooien**.
   
   Nieuwe door voeringen in de geselecteerde opslag plaats en vertakking worden nu doorlopend geïmplementeerd in uw App Service-app. U kunt de door voeringen en implementaties volgen op de pagina **implementatie centrum** .
   
   ![Door voeringen en implementaties bijhouden in het implementatie centrum](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>Optie 2: Azure-pijp lijnen gebruiken 

Als uw account over de benodigde machtigingen beschikt, kunt u Azure-pijp lijnen zo instellen dat ze voortdurend worden geïmplementeerd vanuit GitHub of Azure opslag plaatsen-opslag plaatsen. Zie [een web-app implementeren voor Azure-app Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)voor meer informatie over de implementatie via Azure-pijp lijnen.

Azure App Service voor het maken van continue levering Azure-pijp lijnen in uw Azure DevOps-organisatie: 

- Uw Azure-account moet machtigingen hebben om naar Azure Active Directory te schrijven en een service te maken. 
  
- Uw Azure-account moet de rol **eigenaar** hebben in uw Azure-abonnement.

- U moet een beheerder zijn in het Azure DevOps-project dat u wilt gebruiken.

Azure-pijp lijnen configureren (preview):

1. Selecteer **app Services** in het [Azure Portal](https://portal.azure.com) linkernavigatiebalk en selecteer vervolgens de web-app die u wilt implementeren. 
   
1. Selecteer op de pagina app het menu **implementatie centrum** in het linkermenu.
   
1. Selecteer op de pagina **Build** -provider **Azure-pijp lijnen (preview)** en selecteer vervolgens **door gaan**. 
   
1. Op de pagina **configureren** , in de sectie **code** :
   
   - Voor GitHub, vervolg keuzelijst en selecteer de **organisatie**, **opslag plaats**en **vertakking** die u continu wilt implementeren.
     
     > [!NOTE]
     > Als u geen opslag plaatsen ziet, moet u mogelijk Azure App Service autoriseren in GitHub. Blader naar de GitHub-opslag plaats en ga naar **instellingen** > **toepassingen** > **geautoriseerde OAuth-apps**. Selecteer **Azure app service**en selecteer vervolgens **verlenen**.
     
   - Selecteer voor Azure opslag plaatsen de **Azure DevOps-organisatie**, **het project**, de **opslag plaats**en de **vertakking** die u continu wilt implementeren of configureer een nieuwe Azure DevOps-organisatie.
     
     > [!NOTE]
     > Als uw bestaande Azure DevOps-organisatie niet wordt weer gegeven, moet u deze mogelijk koppelen aan uw Azure-abonnement. Zie [uw CD release-pijp lijn definiëren](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)voor meer informatie.
     
1. Selecteer **Doorgaan**.
   
1. Voor Azure opslag plaatsen, in de sectie **Build** , geeft u het taal raamwerk op dat door Azure-pijp lijnen moet worden gebruikt om Build-taken uit te voeren en selecteert u vervolgens **door gaan**.
   
1. Kies op de pagina **testen** of u de laad tests wilt inschakelen en selecteer **door gaan**.
   
1. Afhankelijk van de [prijs categorie](https://azure.microsoft.com/pricing/details/app-service/plans/)van uw app service-abonnement, ziet u mogelijk de pagina **implementeren naar staging** . Kies of u [implementatie sleuven wilt inschakelen](deploy-staging-slots.md)en selecteer **door gaan**.
   
   > [!NOTE]
   > Azure-pijp lijnen staan geen continue levering naar de productie site toe. Deze beperking voor komt onbedoelde implementaties naar productie. Stel continue levering in op een staging-sleuf, Controleer de wijzigingen daar en verwissel vervolgens de sleuven wanneer u klaar bent.
   
1. Nadat u de build-provider hebt geconfigureerd, controleert u de instellingen op de pagina **samen vatting** en selecteert u vervolgens **volt ooien**.
   
   Nieuwe door voeringen in de geselecteerde opslag plaats en vertakking worden nu doorlopend geïmplementeerd in uw App Service-app. U kunt de door voeringen en implementaties volgen op de pagina **implementatie centrum** .
   
   ![Door voeringen en implementaties bijhouden in het implementatie centrum](media/app-service-continuous-deployment/github-finished.png)

## <a name="disable-continuous-deployment"></a>Continue implementatie uitschakelen

Als u continue implementatie wilt uitschakelen, selecteert u de **verbinding verbreken** boven aan de pagina van de **implementatie centrum** van uw app.

![Continue implementatie uitschakelen](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>Niet-ondersteunde opslag plaatsen gebruiken

Voor Windows-apps kunt u een continue implementatie hand matig configureren vanuit een Git-of mecurial-opslag plaats in de cloud die niet rechtstreeks wordt ondersteund door de portal, zoals [GitLab](https://gitlab.com/). U doet dit door het externe vak op de pagina **implementatie centrum** te kiezen. Zie [continue implementatie instellen met behulp van hand matige stappen](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Veelvoorkomende problemen met doorlopende implementatie onderzoeken](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Azure PowerShell gebruiken](/powershell/azureps-cmdlets-docs)
* [Git-documentatie](https://git-scm.com/documentation)
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)

[Een repo maken (GitHub)]: https://help.github.com/articles/create-a-repo
[Een repo maken (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Een nieuwe Git-opslag plaats maken (Azure opslag plaatsen)]: /azure/devops/repos/git/creatingrepo
