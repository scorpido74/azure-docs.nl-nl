---
title: Een CI/CD-pijplijn maken voor een PWA met GatsbyJS en Azure DevOps-projecten
description: Met DevOps Projects kunt u eenvoudig aan de slag met Azure. Hiermee kunt u een web-app voor een Azure-service van uw keuze starten in slechts enkele stappen.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: arob98
manager: angrobe
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 02/24/2020
ms.author: angrobe
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 508a61d6bbb00692855e09601aed67ab3be9cc8d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78208960"
---
#  <a name="quickstart-create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-projects"></a>Snelstart: een CI/CD-pijplijn maken in Azure Pipelines voor Node.js met Azure DevOps-projecten
In deze quickstart maakt u een NodeJS progressive web app (PWA) met [GatsbyJS](https://www.gatsbyjs.org/) en de vereenvoudigde Azure DevOps-projectcreatie-ervaring. Wanneer u klaar bent, beschikt u over een permanente integratie (CI) en een continue leveringspijplijn (CD) voor uw PWA in Azure Pipelines. Azure DevOps Projects stelt in wat u nodig hebt voor het ontwikkelen, implementeren en bewaken.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 
- Een [Azure DevOps-organisatie.](https://azure.microsoft.com/services/devops/)

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In DevOps Projects wordt een CI/CD-pijplijn gemaakt in Azure Pipelines. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. Met DevOps Projects worden ook Azure-resources gemaakt in het Azure-abonnement van uw keuze.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en selecteer in het linkerdeelvenster **Een resource maken**. 

   ![Een Azure-bron maken in Azure-portal](_img/azure-devops-project-nodejs/create-azure-resource.png)

2. Zoeken naar en selecteer **DevOps-projecten**en selecteer vervolgens **Maken**.

 ![Een Ontwikkelaarsproject maken](_img/azure-devops-project-nodejs/create-devops-project.png) 

## <a name="select-a-sample-application-and-azure-service"></a>Een voorbeeldtoepassing en Azure-service selecteren

1. Selecteer de Node.js-voorbeeldtoepassing.   

 ![Selecteer het voorbeeld van Node.js](_img/azure-devops-project-nodejs/select-nodejs-devops-project.png) 

2. Het standaardvoorbeeldframework is **Express.js**. Wijzig de selectie in **Eenvoudige knooppunt.js-app** en selecteer **Volgende**. 

 ![Selecteer de app Simple Node.js](_img/azure-devops-project-nodejs/simple-nodejs-project.png) 

3. De implementatiedoelen die beschikbaar zijn in deze stap worden bepaald door het toepassingskader dat in stap 2 is geselecteerd.  In dit voorbeeld is **Windows Web App** het standaardimplementatiedoel.  **Web-app voor containers** laten instellen en selecteer **Volgende**.

 ![Het implementatiedoel selecteren](_img/azure-devops-project-nodejs/select-web-server.png) 

## <a name="configure-a-project-name-and-an-azure-subscription"></a>Een projectnaam en een Azure-abonnement configureren

1. In de laatste stap van de werkstroom voor het maken van Ontwikkelaars-projecten wijst u een projectnaam toe, selecteert u een Azure-abonnement en selecteert **u Gereed**.  

 ![Een projectnaam toewijzen en een abonnement selecteren](_img/azure-devops-project-nodejs/assign-project-name.png) 

2. Er wordt een overzichtspagina weergegeven terwijl uw project wordt gebouwd en uw toepassing wordt geïmplementeerd in Azure. Na een korte periode wordt een project gemaakt in uw [Azure DevOps-organisatie](https://dev.azure.com/) met een git repo, een Kanban-bord, een implementatiepijplijn, testplannen en de artefacten die uw app vereist.  

## <a name="managing-your-project"></a>Uw project beheren

1. Navigeer naar **Alle bronnen** en vind uw DevOps-project. Selecteer uw **DevOps-project.**

![Azure DevOps-dashboard in resourcelijst](_img/azure-devops-project-nodejs/azure-devops-project-in-resource-list.png)

2. U wordt doorverwezen naar een dashboard dat inzicht biedt in uw projectstartpagina, coderepository, de CI/CD-pijplijn en een koppeling naar uw hardloop-app. Selecteer de **startpagina van Het project** om uw toepassing in Azure **DevOps weer** te geven en selecteer op een ander tabblad De browser het eindpunt van **toepassing** om de live voorbeeld-app weer te geven.  We wijzigen dit voorbeeld later om GatsbyJS generated PWA te gebruiken.

![Azure DevOps-dashboard](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 

3. Vanuit uw Azure DevOps-project u teamleden uitnodigen om samen te werken en een Kanban-bord op te richten om uw werk te volgen.  Voor meer informatie, zie [hier](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops?view=azure-devops).

![Overzicht van Azure DevOps](_img/azure-devops-project-nodejs/azure-devops-overview.png)

## <a name="clone-the-repo-and-install-your-gatsby-pwa"></a>Kloon de repo en installeer uw Gatsby PWA

DevOps Projects maakt een git-repository in Azure Repos of GitHub. In dit voorbeeld is een Azure Repo gemaakt.  De volgende stap is om de repo te klonen en wijzigingen aan te brengen.

1. Selecteer **Repo's** in uw **DevOps-project** en klik op **Kloon**.  Er zijn verschillende mechanismen om de git repo kloon naar uw bureaublad.  Kies degene die past bij uw ontwikkelingservaring.  

![De opslagplaats klonen](_img/azure-devops-project-nodejs/clone-the-repo.png)

2. Nadat de repo is gekloond op uw bureaublad, brengt u enkele wijzigingen aan in de startersjabloon. Begin met het installeren van de GatsbyJS CLI vanaf uw terminal.
```powershell
npm install -g gatsby
```

3. Navigeer vanaf de terminal naar de wortel van uw repo. Het moet drie mappen bevatten die er als volgt uitzien:
```powershell
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        2/23/2020  10:42 PM                Application
d-----        2/23/2020   3:05 PM                ArmTemplates
d-----        2/23/2020   3:05 PM                Tests
```

4. We willen niet dat alle bestanden in de map Toepassing, omdat we gaan om het te vervangen door een Gatsby starter. Voer de volgende opdrachten in volgorde uit om deze bij te snijden.
```powershell
cp .\Application\Dockerfile .
rmdir Application
```

5. Gebruik de Gatsby CLI om een voorbeeld PWA te genereren. Ren `gatsby new` vanaf de terminal om de `gatsby-starter-blog` wizard PWA te starten en selecteer voor uw startersjabloon. Het moet lijken op dit monster:
```powershell
c:\myproject> gatsby new
√ What is your project called? ... my-gatsby-project
? What starter would you like to use? » - Use arrow-keys. Return to submit.
    gatsby-starter-default
    gatsby-starter-hello-world
>   gatsby-starter-blog
    (Use a different starter)
```

6. Je hebt nu `my-gatsby-project`een map met de naam . Hernoem het `Application` naar `Dockerfile` en kopieer de in.
```powershell
mv my-gatsby-project Application
mv Dockerfile Application
```

7. Open in uw favoriete editor het Dockerfile `FROM node:8` en `FROM node:12`wijzig de eerste regel van . Deze wijziging zorgt ervoor dat uw container Node.js versie 12.x gebruikt in plaats van versie 8.x. GatsbyJS vereist modernere versies van Node.js.

8. Open vervolgens het bestand package.json in de map Toepassing en bewerk het [veld scripts](https://docs.npmjs.com/files/package.json#scripts) om ervoor te zorgen dat uw ontwikkel- en productieservers luisteren op alle beschikbare netwerkinterfaces (bijvoorbeeld 0.0.0.0) en poort 80. Zonder deze instellingen kan de container-app-service het verkeer niet doorsturen naar uw Node.js-app die in uw container wordt uitgevoerd. Het `scripts` veld moet lijken op wat er onder. U wilt in het `develop` `serve`bijzonder `start` de , en doelen wijzigen van hun standaardinstellingen.
```json
  "scripts": {
    "build": "gatsby build",
    "develop": "gatsby develop  -H 0.0.0.0 -p 80",
    "format": "prettier --write \"**/*.{js,jsx,json,md}\"",
    "start": "npm run serve",
    "serve": "npm run build && gatsby serve -H 0.0.0.0 -p 80",
    "clean": "gatsby clean",
    "test": "echo \"Write tests! -> https://gatsby.dev/unit-testing\" && exit 1"
  }
```

## <a name="edit-your-cicd-pipelines"></a>Uw CI/CD-pijplijnen bewerken

1. Voordat u de code in de vorige sectie vastlegt, brengt u enkele wijzigingen aan in uw build- en releasepijplijnen. Bewerk uw 'Pijplijn bouwen' en werk de taak Knooppunt bij om Node.js versie 12.x te gebruiken. Stel het veld **Taakversie** in op 1.x en het veld **Versie** op 12.x.
![Update Node.js naar 12.x](_img/azure-devops-project-nodejs/build-pipeline-update-node.png)

2. In deze quickstart maken we geen eenheidstests en schakelen we die stappen in onze buildpijplijn uit. Wanneer u tests schrijft, u deze stappen opnieuw inschakelen. Klik met de rechtermuisknop om de taken met het label **Testafhankelijkheden installeren** te selecteren en **unittests uit te voeren** en uit te schakelen.

![Build-tests uitschakelen](_img/azure-devops-project-nodejs/disable-build-unittests.png)

3. Bewerk uw releasepijplijn.
![De releasepijplijn bewerken](_img/azure-devops-project-nodejs/edit-release-pipeline.png)

4. Net als bij de opbouwpijplijn wijzigt u de taak Knooppunt om 12.x te gebruiken en schakelt u de twee testtaken uit. Uw release moet lijken op deze screenshot.

![Voltooide releasepijplijn](_img/azure-devops-project-nodejs/release-pipeline-complete.png)

1. Ga aan de linkerkant van de browser naar het bestand **views/index.pug**.

1. Selecteer **Bewerken** en breng vervolgens een wijziging aan in de h2-kop.  
    Voer bijvoorbeeld **meteen aan de slag met Azure DevOps-projecten** of voer een andere wijziging aan.

1. Selecteer **Doorvoeren** en sla vervolgens de wijzigingen op.

1. Ga in de browser naar het DevOps Projects-dashboard.   
Als het goed is, ziet u nu dat er een build wordt gemaakt. De wijzigingen die u hebt aangebracht, worden automatisch gebouwd en geïmplementeerd via een CI/CD-pijplijn.

## <a name="commit-your-changes-and-examine-the-azure-cicd-pipeline"></a>Uw wijzigingen vastleggen en de Azure CI/CD-pijplijn onderzoeken

In de vorige twee stappen hebt u een door Gatsby gegenereerde PWA aan uw git repo toegevoegd en uw pijplijnen bewerkt om de code te bouwen en te implementeren. We kunnen de code vastleggen en de voortgang van de voortgang van de build- en releasepijplijn bekijken.

1. Voer vanaf de hoofdmap van de git repo van uw project in een terminal de volgende opdrachten uit om uw code naar uw Azure DevOps-project te pushen:
```powershell
git add .
git commit -m "My first Gatsby PWA"
git push
```

2. Een build wordt gestart `git push` zodra deze is voltooid. U de voortgang volgen vanuit het **Azure DevOps-dashboard.**

![Azure DevOps-dashboard in resourcelijst](_img/azure-devops-project-nodejs/azure-devops-project-in-resource-list.png)

3. Na een paar minuten moeten uw build- en releasepijplijnen zijn voltooid en moet uw PWA worden geïmplementeerd in een container. Klik op de koppeling **Eindpunt van toepassing** in het bovenstaande dashboard en u ziet een Gatsby-startproject voor blogs.



## <a name="clean-up-resources"></a>Resources opschonen

U Azure App Service en andere gerelateerde bronnen verwijderen die u hebt gemaakt wanneer u de resources niet meer nodig hebt. Gebruik de functionaliteit **Verwijderen** op het DevOps Projects-dashboard.


## <a name="next-steps"></a>Volgende stappen

Wanneer u uw CI/CD-proces configureert, worden er automatisch pijplijnen voor bouwen en vrijgeven gemaakt. U deze build- en release-pijplijnen wijzigen om aan de behoeften van uw team te voldoen. Voor meer informatie over de CI/CD-pijplijn, zie:

> [!div class="nextstepaction"]
> [CD-proces aanpassen](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

