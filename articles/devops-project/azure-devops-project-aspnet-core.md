---
title: 'Quickstart: een CI/CD-pijplijn voor .NET maken met Azure DevOps Projects'
description: Azure DevOps Projects zorgt ervoor dat u eenvoudig aan de slag kunt met Azure. Hiermee kunt u een .NET-app voor een Azure-service van uw keuze starten in slechts enkele stappen.
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
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 7d2ccdfa1fe553d0795a82856dd255f4a54138bf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "70898026"
---
# <a name="create-a-cicd-pipeline-for-net-with-azure-devops-projects"></a>Een CI/CD-pijplijn voor .NET maken met Azure DevOps Projects

Configureer continue integratie (CI) en continue levering (CD) voor uw .NET Core- of ASP.NET-toepassing met Azure DevOps Projects. Azure DevOps Projects vereenvoudigt de eerste configuratie van een build- en release-pijplijn in Azure Pipelines.

Als u geen Azure-abonnement hebt, kunt u er gratis een krijgen via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

DevOps Projects maakt een CI/CD-pijplijn in Azure DevOps. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. Met DevOps Projects worden ook Azure-resources gemaakt in het Azure-abonnement van uw keuze.

1. Meld u aan bij de [Microsoft Azure-portal](https://portal.azure.com).

1. Kies in het linkerdeelvenster het pictogram **Een resource maken** in de linkernavigatiebalk en zoek naar **DevOps Projects**.  

3.  Selecteer **Maken**.

    ![Continue levering starten](_img/azure-devops-project-aspnet-core/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Een voorbeeldtoepassing en Azure-service selecteren

1. Selecteer de **.NET**-voorbeeldtoepassing. Bij de .NET-voorbeelden kunt u kiezen uit het open source-framework ASP.NET of het platformoverschrijdende .NET Core-framework.

   ![.NET Framework](_img/azure-devops-project-aspnet-core/chooselanguagedotnet.png)

2. Dit voorbeeld gebruikt een ASP.NET Core MVC-toepassing. Selecteer het **toepassingskader .NET Core** en selecteer **Volgende**.    
    
3. Selecteer **Windows Web App** als implementatiedoel en selecteer **Volgende**. Optioneel u andere Azure-services voor uw implementatie kiezen. Het toepassingskader, dat u eerder hebt gekozen, bepaalt het type Azure-serviceimplementatiedoel dat hier beschikbaar is.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps en een Azure-abonnement configureren 

1. Voer een **projectnaam**in .

2. Maak een nieuwe gratis **Azure DevOps-organisatie** of kies een bestaande organisatie in de vervolgkeuzelijst.

3. Selecteer uw **Azure-abonnement,** voer een naam in voor uw **web-app** of voer de standaardversie uit en selecteer **Gereed**. Na enkele minuten wordt het implementatieoverzicht van DevOps-projecten weergegeven in de Azure-portal. 

4. Selecteer **Ga naar resource om** het dashboard Van DevOps Project weer te geven. In de rechterbovenhoek moet u het **project** vastmaken aan uw dashboard voor snelle toegang. Er wordt een voorbeeld-app ingesteld in een repo in uw **Azure DevOps-organisatie.** Een build wordt uitgevoerd en uw app wordt geïmplementeerd in Azure.

5. Het dashboard biedt meer inzicht in uw codeopslagplaats, CI/CD-pijplijn en app in Azure. Selecteer rechts onder Azure-resources de optie **Bladeren** om uw hardloopapp weer te geven.

   ![Dashboardweergave](_img/azure-devops-project-aspnet-core/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>Codewijzigingen doorvoeren en CI/CD uitvoeren

Met DevOps Projects is een Git-opslagplaats gemaakt in Azure Repos of in GitHub. Ga als volgt te werk om de opslagplaats weer te geven en codewijzigingen aan de brengen in de toepassing:

1. Selecteer links van het dashboard DevOps Projects de koppeling voor uw **masterbranch.** Met deze koppeling opent u een weergave in de zojuist gemaakte Git-opslagplaats.

2. In de volgende stappen u de webbrowser gebruiken om codewijzigingen rechtstreeks aan te brengen en te vastleggen in de **hoofdbranch.** Je je Git-repository ook klonen in je favoriete IDE door **Clone** te selecteren rechtsboven op de repository-pagina. 

3. Navigeer aan de linkerkant door de toepassingsbestandsstructuur naar **Application/aspnet-core-dotnet-core/Pages/Index.cshtml**.

4. Selecteer **Bewerken** en breng vervolgens een wijziging aan in de h2-kop. Typ bijvoorbeeld **Meteen aan de slag met Azure DevOps Projects** of maak een andere wijziging.

      ![Code-bewerkingen](_img/azure-devops-project-aspnet-core/codechange.png)

5. Selecteer **Commit,** laat een reactie achter en selecteer **Opnieuw vastleggen.**

6. Ga in uw browser naar het Azure DevOps-projectdashboard.  Als het goed is, ziet u nu dat er een build wordt gemaakt. De aangebrachte wijzigingen worden automatisch gebouwd en geïmplementeerd via een CI/CD-pijplijn.

## <a name="examine-the-cicd-pipeline"></a>De CI/CD-pijplijn onderzoeken

In de vorige stap werd in Azure DevOps Projects automatisch een volledige CI/CD-pijplijn geconfigureerd. U kunt de pijplijn verkennen en zo nodig aanpassen. Ga als volgt te werk om vertrouwd te raken met de build- en release-pijplijnen van Azure DevOps.

1. Selecteer boven in het DevOps Projects-dashboard de optie **Build-pijplijnen**.  
Met deze koppeling worden een browsertabblad en de Azure DevOps build-pijplijn van Azure DevOps voor het nieuwe project geopend.

1. Selecteer de ellips (...).  Met deze actie wordt een menu geopend waarin u verschillende activiteiten starten, zoals het in de rij zetten van een nieuwe build, het onderbreken van een build en het bewerken van de buildpijplijn.

1. Selecteer **Bewerken**.

    ![Build-pipeline](_img/azure-devops-project-aspnet-core/builddef.png)

1. In dit deelvenster kunt u de verschillende taken voor uw build-pijplijn onderzoeken.  
 De build voert verschillende taken uit, zoals het ophalen van bronnen uit de Git-opslagplaats, het herstellen van afhankelijkheden en het publiceren van uitvoer die wordt gebruikt voor implementaties.

1. Selecteer bovenaan de build-pijplijn de naam van de build-pijplijn.

1. Wijzig de naam van de build-pijplijn in een meer beschrijvende naam. Selecteer **Opslaan en wachtrij** en selecteer vervolgens **Opslaan**.

1. Selecteer onder de naam van de build-pipeline de optie **Geschiedenis**.   
In het deelvenster **Geschiedenis** ziet u een audittrail van recente wijzigingen voor de build.  Azure Pipelines houdt alle wijzigingen in de build-pipeline bij en biedt de mogelijkheid om versies te vergelijken.

1. Selecteer **Triggers**.  
In DevOps Projects is automatisch een CI-trigger gemaakt en met elke doorvoering naar de opslagplaats wordt een nieuwe build gestart.  U kunt desgewenst kiezen of u vertakkingen van het CI-proces wilt opnemen of uitsluiten.

1. Selecteer **Retentie**.  
Afhankelijk van het scenario kunt u beleidsregels opgeven om een bepaald aantal builds te behouden of te verwijderen.

1. Selecteer **Build en release** en selecteer vervolgens **Releases**.  
In DevOps Projects wordt een release-pijplijn gemaakt om implementaties in Azure te beheren.

1.  Selecteer aan de linkerkant het beletselteken (...) naast de release-pijplijn en selecteer **Bewerken**.  
De release-pijplijn bevat een pijplijn die het releaseproces definieert.  

1. Onder **Artefacten** selecteert u **Neerzetten**.  De build-pijplijn die u in de vorige stappen hebt onderzocht, produceert de uitvoer die wordt gebruikt voor het artefact. 

1. Selecteer naast het pictogram **Neerzetten** de optie **Continue implementatietrigger**.  
Deze release-pijplijn heeft een ingeschakelde CD-trigger op basis waarvan een implementatie wordt uitgevoerd telkens wanneer een nieuw build-artefact beschikbaar is. U kunt de trigger eventueel uitschakelen zodat de implementaties handmatig moeten worden uitgevoerd.  

1. Selecteer aan de linkerkant **Taken**.   
De taken zijn de acties die tijdens het implementatieproces worden uitgevoerd. In dit voorbeeld is een taak gemaakt om te implementeren in Azure App Service.

1. Selecteer aan de rechterkant **Releases weergeven**. In deze weergave wordt een versiesgeschiedenis weergegeven.

1. Selecteer het beletselteken (...) naast een van de releases en selecteer vervolgens **Openen**.  
Er zijn verschillende menu's die u kunt verkennen, zoals een versieoverzicht, gekoppelde werkitems en tests.


1. Selecteer **Doorvoeringen**.   
In deze weergave worden de codedoorvoeringen weergegeven die zijn gekoppeld aan deze implementatie. 

1. Selecteer **Logboeken**.  
De logboeken bevatten nuttige informatie over het implementatieproces. Ze kunnen worden weergegeven tijdens en na de implementaties.


## <a name="clean-up-resources"></a>Resources opschonen

U kunt Azure App Service en gerelateerde resources die u hebt gemaakt, verwijderen wanneer u ze niet meer nodig hebt. Gebruik de functionaliteit **Verwijderen** op het DevOps Projects-dashboard.

## <a name="next-steps"></a>Volgende stappen

Zie deze zelfstudie voor meer informatie over het wijzigen van de build- en release-pipelines in overeenstemming met de behoeften van uw team:

> [!div class="nextstepaction"]
> [CD-proces aanpassen](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Video's

> [!VIDEO https://www.youtube.com/embed/itwqMf9aR0w]
