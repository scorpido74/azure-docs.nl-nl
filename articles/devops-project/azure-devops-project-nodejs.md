---
title: Een CI/CD-pijp lijn maken voor een PWA met GatsbyJS en Azure DevOps starter
description: DevOps Starter maakt het eenvoudig om aan de slag te gaan met Azure. Hiermee kunt u een web-app voor een Azure-service van uw keuze starten in slechts enkele stappen.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: arob98
manager: angrobe
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: angrobe
ms.custom: mvc
ms.openlocfilehash: 7db4fa2a780a3a1f53ecd73a40c247583cb6a79a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233824"
---
# <a name="create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-starter"></a>Een CI/CD-pijp lijn maken in azure-pijp lijnen voor node. js met Azure DevOps starter

In deze Quick Start maakt u een NodeJS progressieve web-app (PWA) met behulp van [GatsbyJS](https://www.gatsbyjs.org/) en de vereenvoudigde ervaring voor het maken van Azure DevOps. Wanneer u klaar bent, hebt u een pijp lijn voor continue integratie (CI) en continue levering (CD) voor uw PWA in azure-pijp lijnen. Azure DevOps starter definieert wat u nodig hebt voor het ontwikkelen, implementeren en bewaken.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Een [Azure DevOps](https://azure.microsoft.com/services/devops/) -organisatie.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

DevOps Starter maakt een CI/CD-pijp lijn in azure-pijp lijnen. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. DevOps Starter maakt ook Azure-resources in het Azure-abonnement van uw keuze.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en selecteer in het linkerdeel venster **een resource maken**. 

   ![Een Azure-resource maken in Azure Portal](_img/azure-devops-project-nodejs/create-azure-resource.png)

1. Typ **DevOps starter**in het zoekvak en selecteer. Klik op **toevoegen** om een nieuw item te maken.

    ![Het DevOps-starter dash board](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Een voorbeeldtoepassing en Azure-service selecteren

1. Selecteer de Node.js-voorbeeldtoepassing.   

    ![Het voor beeld van node. js selecteren](_img/azure-devops-project-nodejs/select-nodejs.png) 

1. Het standaardvoorbeeldframework is **Express.js**. Wijzig de selectie in **eenvoudige node. js-app** en selecteer **volgende**. 

    ![De eenvoudige node. js-app selecteren](_img/azure-devops-project-nodejs/select-nodejs-framework.png) 

1. De beschik bare implementatie doelen in deze stap worden bepaald door het toepassings raamwerk dat u hebt geselecteerd in stap 2. In dit voor beeld is **Windows Web app** het standaard implementatie doel. Verlaat **Web App for containers** instellen en selecteer **volgende**.

    ![Het implementatie doel selecteren](_img/azure-devops-project-nodejs/select-web-server.png)

## <a name="configure-a-project-name-and-an-azure-subscription"></a>Een project naam en een Azure-abonnement configureren

1. In de laatste stap van de werk stroom voor het maken van DevOps-starter wijst u een project naam toe, selecteert u een Azure-abonnement en selecteert u **gereed**.  

    ![Een project naam toewijzen en een abonnement selecteren](_img/azure-devops-project-nodejs/assign-project-name.png)

1. Er wordt een overzichts pagina weer gegeven terwijl uw project is gebouwd en uw toepassing wordt geïmplementeerd naar Azure. Na een korte periode wordt een project gemaakt in uw [Azure DevOps-organisatie](https://dev.azure.com/) met een Git opslag plaats, een kanbanbord, een implementatie pijplijn, test plannen en de artefacten die uw app nodig heeft.  

## <a name="managing-your-project"></a>Uw project beheren

1. Ga naar **alle resources** en zoek uw DevOps starter. Selecteer uw **DevOps-starter**.

    ![Azure DevOps-dash board in de lijst met resources](_img/azure-devops-project-nodejs/devops-starter-list.png)

1. U wordt omgeleid naar een dash board dat inzicht biedt in uw project startpagina, code opslagplaats, de CI/CD-pijp lijn en een koppeling naar uw actieve app. Selecteer de **Introductie pagina** van het project om uw toepassing in **Azure DevOps** weer te geven en selecteer op een ander browser tabblad het **eind punt** van de toepassing om de Live-voor beeld-app weer te geven. We wijzigen dit voor beeld later om GatsbyJS gegenereerde PWA te gebruiken.

    ![Azure DevOps-dash board](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 

1. Vanuit uw Azure DevOps-project kunt u team leden uitnodigen om samen te werken en een Kanbanbord te maken om te beginnen met het bijhouden van uw werk. Zie [hier](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops?view=azure-devops)voor meer informatie.

![Overzicht van Azure DevOps](_img/azure-devops-project-nodejs/azure-devops-overview.png)

## <a name="clone-the-repo-and-install-your-gatsby-pwa"></a>De opslag plaats klonen en uw Gatsby PWA installeren

DevOps Starter maakt een Git-opslag plaats in azure opslag plaatsen of GitHub. In dit voor beeld is een Azure-opslag plaats gemaakt. De volgende stap is om de opslag plaats te klonen en wijzigingen aan te brengen.

1. Selecteer **opslag plaatsen** in uw **DevOps-project** en klik vervolgens op **klonen**.  Er zijn verschillende mechanismen om de Git-opslag plaats te klonen op uw bureau blad.  Kies het abonnement dat aansluit bij uw ontwikkel ervaring.  

    ![De opslagplaats klonen](_img/azure-devops-project-nodejs/clone-the-repo.png)

1. Nadat de opslag plaats naar het bureau blad is gekloond, brengt u enkele wijzigingen aan in de start sjabloon. Begin met het installeren van de GatsbyJS CLI vanaf uw Terminal.

   ```powershell
    npm install -g gatsby
   ```

1. Navigeer vanuit de terminal naar de hoofdmap van uw opslag plaats. Deze moet drie mappen bevatten die er als volgt uitzien:

    ```powershell
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----        2/23/2020  10:42 PM                Application
    d-----        2/23/2020   3:05 PM                ArmTemplates
    d-----        2/23/2020   3:05 PM                Tests
    ```
    
1. We willen niet alle bestanden in de toepassingsmap omdat we deze vervangen door een Gatsby-starter. Voer de volgende opdrachten in de juiste volg orde uit om deze uit te snijden.
    
    ```powershell
    cp .\Application\Dockerfile .
    rmdir Application
    ```

1. Gebruik de Gatsby CLI om een voor beeld-PWA te genereren. Voer `gatsby new` uit vanaf de terminal om de PWA-wizard te `gatsby-starter-blog` starten en selecteer voor uw start sjabloon. Dit moet eruitzien als dit voor beeld:

    ```powershell
    c:\myproject> gatsby new
    √ What is your project called? ... my-gatsby-project
    ? What starter would you like to use? » - Use arrow-keys. Return to submit.
        gatsby-starter-default
        gatsby-starter-hello-world
    >   gatsby-starter-blog
        (Use a different starter)
    ```
    
1. U hebt nu een map met `my-gatsby-project`de naam. Wijzig de `Dockerfile` naam `Application` ervan in en kopieer deze naar het bestand.
    
    ```powershell
    mv my-gatsby-project Application
    mv Dockerfile Application
    ```
    
1. Open de Dockerfile in uw favoriete editor en wijzig de eerste regel van `FROM node:8` in. `FROM node:12` Met deze wijziging wordt ervoor gezorgd dat de container de node. js-versie 12. x gebruikt in plaats van versie 8. x. GatsbyJS vereist meer moderne versies van node. js.

1. Open vervolgens het bestand Package. json in de toepassingsmap en bewerk het [veld scripts](https://docs.npmjs.com/files/package.json#scripts) om ervoor te zorgen dat uw ontwikkel-en productie servers op alle beschik bare netwerk interfaces (bijvoorbeeld 0.0.0.0) en poort 80 Luis teren. Zonder deze instellingen kan de container app service geen verkeer door sturen naar uw node. js-app die in de container wordt uitgevoerd. Het `scripts` veld moet er als volgt uitzien. In het bijzonder wilt u de `develop`standaard instellingen `serve`, en `start` doelen wijzigen.

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
    
## <a name="edit-your-cicd-pipelines"></a>Uw CI/CD-pijp lijnen bewerken

1. Voordat u de code in de vorige sectie doorvoert, brengt u enkele wijzigingen aan in uw build-en release pijplijnen. Bewerk uw build-pijp lijn en werk de knooppunt taak bij voor het gebruik van node. js versie 12. x. Stel het veld **taak versie** in op 1. x en het veld **versie** in op 12. x.

    ![Knoop punt. js bijwerken naar 12. x](_img/azure-devops-project-nodejs/build-pipeline-update-node.png)

1. In deze Snelstartgids worden er geen eenheids tests gemaakt en worden de stappen in onze build-pijp lijn uitgeschakeld. Wanneer u tests schrijft, kunt u deze stappen opnieuw inschakelen. Klik met de rechter muisknop om de taken te selecteren **afhankelijkheden voor installatie testen** en **Voer eenheids tests uit** en schakel ze uit.

    ![Compilatie tests uitschakelen](_img/azure-devops-project-nodejs/disable-build-unittests.png)

1. Bewerk uw release pijplijn.

    ![De release pijplijn bewerken](_img/azure-devops-project-nodejs/edit-release-pipeline.png)

1. Net als bij de build-pijp lijn wijzigt u de knooppunt taak voor het gebruik van 12. x en schakelt u de twee test taken uit. Uw release moet eruitzien als deze scherm afbeelding.

    ![Release-pijp lijn voltooid](_img/azure-devops-project-nodejs/release-pipeline-complete.png)

1. Ga aan de linkerkant van de browser naar het bestand **views/index.pug**.

1. Selecteer **Bewerken** en breng vervolgens een wijziging aan in de h2-kop.  Voer bijvoorbeeld meteen aan de **slag met Azure DevOps starter** of breng een andere wijziging aan.

1. Selecteer **Doorvoeren** en sla vervolgens de wijzigingen op.

1. Ga in uw browser naar het DevOps-starter-dash board.   
Als het goed is, ziet u nu dat er een build wordt gemaakt. De wijzigingen die u hebt aangebracht, worden automatisch gemaakt en geïmplementeerd via een CI/CD-pijp lijn.

## <a name="commit-your-changes-and-examine-the-azure-cicd-pipeline"></a>Uw wijzigingen door voeren en de Azure CI/CD-pijp lijn onderzoeken

In de vorige twee stappen hebt u een door Gatsby gegenereerde PWA toegevoegd aan uw Git opslag plaats en uw pijp lijnen bewerkt om de code te bouwen en te implementeren. We kunnen de code door voeren en de voortgang bekijken via de pijp lijn build en release.

1. Voer in de hoofdmap van het git-opslag plaats van uw project in een Terminal de volgende opdrachten uit om uw code naar uw Azure DevOps-project te pushen:

    ```powershell
    git add .
    git commit -m "My first Gatsby PWA"
    git push
    ```
    
1. Een build wordt gestart zodra dit is `git push` voltooid. U kunt de voortgang van het **dash board van Azure DevOps**volgen.

3. Na enkele minuten moeten uw build-en release-pijp lijnen worden voltooid en moet uw PWA worden geïmplementeerd in een container. Klik op de koppeling **toepassings eindpunt** op het dash board hierboven en er wordt een Gatsby-start project voor blogs weer geven.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt Azure App Service en andere gerelateerde resources die u hebt gemaakt, verwijderen wanneer u de resources niet meer nodig hebt. Gebruik de **verwijderings** functionaliteit op het DevOps-starter-dash board.

## <a name="next-steps"></a>Volgende stappen

Wanneer u uw CI/CD-proces configureert, worden er automatisch builds-en release-pijp lijnen gemaakt. U kunt deze builds en release pijplijnen wijzigen om te voldoen aan de behoeften van uw team. Voor meer informatie over de CI/CD-pijplijn, zie:

> [!div class="nextstepaction"]
> [CD-proces aanpassen](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

