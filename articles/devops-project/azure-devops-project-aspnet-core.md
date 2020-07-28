---
title: 'Snelstartgids: een CI/CD-pijp lijn maken voor .NET met Azure DevOps starter'
description: Met Azure DevOps starter kunt u gemakkelijk aan de slag met Azure. Hiermee kunt u een .NET-app voor een Azure-service van uw keuze starten in slechts enkele stappen.
ms.prod: devops
ms.technology: devops-cicd
services: azure-devops-project
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 3f0d937737cb261fb81dc4cdad3579ee593b5981
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82233296"
---
# <a name="create-a-cicd-pipeline-for-net-with-azure-devops-starter"></a>Een CI/CD-pijp lijn maken voor .NET met Azure DevOps starter

Configureer doorlopende integratie (CI) en continue levering (CD) voor uw .NET core-of ASP.NET-toepassing met DevOps starter. DevOps starter vereenvoudigt de initiële configuratie van een build en release-pijp lijn in azure-pijp lijnen.

Als u geen Azure-abonnement hebt, kunt u er gratis een krijgen via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

DevOps Starter maakt een CI/CD-pijp lijn in azure DevOps. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. DevOps Starter maakt ook Azure-resources in het Azure-abonnement van uw keuze.

1. Meld u aan bij de [Microsoft Azure-Portal](https://portal.azure.com).

1. Typ **DevOps starter**in het zoekvak en selecteer. Klik op **toevoegen** om een nieuw item te maken. 

    ![Het DevOps-starter dash board](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Een voorbeeldtoepassing en Azure-service selecteren

1. Selecteer de **.NET**-voorbeeldtoepassing. Bij de .NET-voorbeelden kunt u kiezen uit het open source-framework ASP.NET of het platformoverschrijdende .NET Core-framework.

   ![.NET Framework](_img/azure-devops-project-aspnet-core/select-dotnet.png)

2. Dit voorbeeld gebruikt een ASP.NET Core MVC-toepassing. Selecteer het **.net core** -toepassings raamwerk en selecteer vervolgens **volgende**.    
    
3. Selecteer **Windows Web app** als een implementatie doel en selecteer **volgende**. U kunt desgewenst andere Azure-Services voor uw implementatie kiezen. Het toepassings raamwerk, dat u eerder hebt gekozen, bepaalt het type Azure service-implementatie doel dat u hier kunt vinden.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps en een Azure-abonnement configureren 

1. Voer een **project naam**in.

2. Maak een nieuwe gratis **Azure DevOps-organisatie** of kies een bestaande organisatie in de vervolg keuzelijst.

3. Selecteer uw **Azure-abonnement**, voer een naam in voor uw **Web-app** of neem de standaard waarden op en selecteer vervolgens **gereed**. Na een paar minuten wordt het overzicht van de DevOps starter-implementatie weer gegeven in de Azure Portal. 

4. Selecteer **naar resource gaan** om het DevOps starter-dash board weer te geven. In de rechter bovenhoek kunt u het **project** vastmaken aan uw dash board voor snelle toegang. Een voor beeld-app is ingesteld in een opslag plaats in uw **Azure DevOps-organisatie**. Er wordt een build uitgevoerd en uw app is geïmplementeerd in Azure.

5. Het dashboard biedt meer inzicht in uw codeopslagplaats, CI/CD-pijplijn en app in Azure. Klik aan de rechter kant onder Azure-resources op **Bladeren** om de actieve app weer te geven.

   ![Dashboardweergave](_img/azure-devops-project-aspnet-core/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>Codewijzigingen doorvoeren en CI/CD uitvoeren

DevOps starter heeft een Git-opslag plaats gemaakt in azure opslag plaatsen of GitHub. Ga als volgt te werk om de opslagplaats weer te geven en codewijzigingen aan de brengen in de toepassing:

1. Selecteer aan de linkerkant van het DevOps starter-dash board de koppeling voor uw **hoofd** vertakking. Met deze koppeling opent u een weergave in de zojuist gemaakte Git-opslagplaats.

2. In de volgende stappen kunt u de webbrowser gebruiken om code wijzigingen rechtstreeks aan de **hoofd** vertakking door te voeren. U kunt ook uw Git-opslag plaats in uw favoriete IDE klonen door **klonen** te selecteren in de rechter bovenhoek van de pagina met de opslag plaats. 

3. Ga aan de linkerkant naar de structuur van het toepassings bestand naar **Application/ASPNET-core-DotNet-core/pages/index. cshtml**.

4. Selecteer **Bewerken** en breng vervolgens een wijziging aan in de h2-kop. Typ bijvoorbeeld direct aan **de slag met Azure DevOps starter** of breng een andere wijziging aan.

      ![Code-bewerkingen](_img/azure-devops-project-aspnet-core/codechange.png)

5. Selecteer **door voeren**, behoud een opmerking en selecteer opnieuw **door voeren** .

6. Ga in uw browser naar het Azure DevOps starter-dash board.  Als het goed is, ziet u nu dat er een build wordt gemaakt. De aangebrachte wijzigingen worden automatisch gebouwd en geïmplementeerd via een CI/CD-pijplijn.

## <a name="examine-the-cicd-pipeline"></a>De CI/CD-pijplijn onderzoeken

In de vorige stap heeft Azure DevOps starter automatisch een volledige CI/CD-pijp lijn geconfigureerd. U kunt de pijplijn verkennen en zo nodig aanpassen. Ga als volgt te werk om vertrouwd te raken met de build- en release-pijplijnen van Azure DevOps.

1. Selecteer boven aan het DevOps-starter-dash board **Build pijp lijnen**. Met deze koppeling worden een browsertabblad en de Azure DevOps build-pijplijn van Azure DevOps voor het nieuwe project geopend.

1. Selecteer het beletsel teken (...).  Met deze actie opent u een menu waarin u verschillende activiteiten kunt starten, zoals het in de wachtrij plaatsen van een nieuwe build, het onderbreken van een build en het bewerken van de build-pijp lijn.

1. Selecteer **Bewerken**.

    ![Build-pipeline](_img/azure-devops-project-aspnet-core/builddef.png)

1. In dit deelvenster kunt u de verschillende taken voor uw build-pijplijn onderzoeken. De build voert verschillende taken uit, zoals het ophalen van bronnen uit de Git-opslagplaats, het herstellen van afhankelijkheden en het publiceren van uitvoer die wordt gebruikt voor implementaties.

1. Selecteer bovenaan de build-pijplijn de naam van de build-pijplijn.

1. Wijzig de naam van de build-pijplijn in een meer beschrijvende naam. Selecteer **Opslaan en wachtrij** en selecteer vervolgens **Opslaan**.

1. Selecteer onder de naam van de build-pipeline de optie **Geschiedenis**.   
In het deelvenster **Geschiedenis** ziet u een audittrail van recente wijzigingen voor de build.  Azure Pipelines houdt alle wijzigingen in de build-pipeline bij en biedt de mogelijkheid om versies te vergelijken.

1. Selecteer **Triggers**. DevOps starter heeft automatisch een CI-trigger gemaakt en elke door voering aan de opslag plaats start een nieuwe build. U kunt desgewenst kiezen of u vertakkingen van het CI-proces wilt opnemen of uitsluiten.

1. Selecteer **Retentie**. Afhankelijk van het scenario kunt u beleidsregels opgeven om een bepaald aantal builds te behouden of te verwijderen.

1. Selecteer **Build en release** en selecteer vervolgens **Releases**.  
DevOps Starter maakt een release pijplijn voor het beheren van implementaties naar Azure.

1.  Selecteer aan de linkerkant het beletselteken (...) naast de release-pijplijn en selecteer **Bewerken**. De release-pijplijn bevat een pijplijn die het releaseproces definieert.  

1. Onder **Artefacten** selecteert u **Neerzetten**. De build-pijplijn die u in de vorige stappen hebt onderzocht, produceert de uitvoer die wordt gebruikt voor het artefact. 

1. Selecteer naast het pictogram **Neerzetten** de optie **Continue implementatietrigger**. Deze release-pijplijn heeft een ingeschakelde CD-trigger op basis waarvan een implementatie wordt uitgevoerd telkens wanneer een nieuw build-artefact beschikbaar is. U kunt de trigger eventueel uitschakelen zodat de implementaties handmatig moeten worden uitgevoerd.  

1. Selecteer aan de linkerkant **Taken**.  De taken zijn de acties die tijdens het implementatieproces worden uitgevoerd. In dit voorbeeld is een taak gemaakt om te implementeren in Azure App Service.

1. Selecteer aan de rechterkant **Releases weergeven**. In deze weergave wordt een versiesgeschiedenis weergegeven.

1. Selecteer het beletselteken (...) naast een van de releases en selecteer vervolgens **Openen**. Er zijn verschillende menu's die u kunt verkennen, zoals een versieoverzicht, gekoppelde werkitems en tests.

1. Selecteer **Doorvoeringen**. In deze weergave worden de codedoorvoeringen weergegeven die zijn gekoppeld aan deze implementatie. 

1. Selecteer **Logboeken**. De logboeken bevatten nuttige informatie over het implementatieproces. Ze kunnen worden weergegeven tijdens en na de implementaties.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt Azure App Service en gerelateerde resources die u hebt gemaakt, verwijderen wanneer u ze niet meer nodig hebt. Gebruik de **verwijderings** functionaliteit op het DevOps-starter-dash board.

## <a name="next-steps"></a>Volgende stappen

Zie deze zelfstudie voor meer informatie over het wijzigen van de build- en release-pipelines in overeenstemming met de behoeften van uw team:

> [!div class="nextstepaction"]
> [CD-proces aanpassen](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Video's

> [!VIDEO https://www.youtube.com/embed/itwqMf9aR0w]
