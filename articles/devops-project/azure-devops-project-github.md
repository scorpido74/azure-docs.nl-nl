---
title: 'Zelfstudie: Een CI/CD-pijp lijn maken voor uw bestaande code met behulp van Azure DevOps Projects'
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
ms.date: 07/09/2018
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 2abe24ad65e1e8997b48a28b35ec0e65162022f2
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898015"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Zelfstudie: Een CI/CD-pijp lijn maken voor uw bestaande code met behulp van Azure DevOps Projects

Azure DevOps Projects biedt een vereenvoudigde ervaring waar u uw bestaande code en Git-opslagplaats gebruikt of een voorbeeldtoepassing kiest voor het maken van een CI- (Continue integratie) en CD-pijplijn (Continue levering) naar Azure.

U gaat het volgende doen:

> [!div class="checklist"]
> * DevOps Projects gebruiken om een CI/CD-pijplijn te maken
> * De toegang tot uw GitHub-opslagplaats configureren en een framework kiezen
> * Azure DevOps en een Azure-abonnement configureren 
> * Wijzigingen doorvoeren in GitHub en automatisch implementeren in Azure
> * De CI/CD-pijplijn van Azure Pipelines onderzoeken
> * Azure Application Insights-bewaking configureren
> * Resources opschonen

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. U kunt er een gratis krijgen via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Toegang tot een GitHub- of externe Git-opslagplaats met .NET, Java, PHP, Node, Python of statische webcode.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In Azure DevOps Projects wordt een CI/CD-pijplijn gemaakt in Azure Pipelines. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. Azure DevOps Projects maakt ook Azure-resources in het Azure-abonnement van uw keuze.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer in het linkerdeelvenster de optie **Nieuw**.

1. Typ in het zoekvak **DevOps Projects** en selecteer **Maken**.

    ![Het DevOps Projects-dashboard](_img/azure-devops-project-github/fullbrowser.png)

1. Selecteer **Neem uw eigen code mee** en selecteer **Volgende**.

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>De toegang tot uw GitHub-opslagplaats configureren en een framework kiezen

1. Selecteer **GitHub** of een externe Git-repo en selecteer vervolgens uw opslagplaats en de vertakking die uw toepassing bevat.

1. Selecteer uw webframework en selecteer **Volgende**.

    ![.NET Framework](_img/azure-devops-project-github/webframework.png)

    Het toepassingsframework dat u eerder hebt gekozen, bepaalt welk type implementatiedoel hier beschikbaar is voor de Azure-service. 
    
1. Selecteer de doelservice en selecteer **Volgende**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps en een Azure-abonnement configureren 

1. Maak een nieuwe Azure DevOps-organisatie of selecteer een bestaande organisatie.

    a. Voer in Azure DevOps een naam in voor het project. 
    
    b. Selecteer uw Azure-abonnement en locatie, voer een naam in voor de toepassing en selecteer **Gereed**.

    Na een paar minuten wordt het DevOps Projects-dashboard weergegeven in de Azure-portal. Er wordt een voorbeeldtoepassing in een opslagplaats in uw Azure DevOps-organisatie ingesteld, er wordt een build uitgevoerd en uw toepassing wordt geïmplementeerd in Azure. Dit dashboard biedt meer inzicht in uw GitHub-codeopslagplaats, de CI/CD-pijplijn en uw toepassing in Azure. 
    
1. Selecteer **Bladeren** om de actieve toepassing weer te geven.

    ![DevOps Projects-dashboardweergave](_img/azure-devops-project-github/dashboardnopreview.png) 
    
Azure DevOps Projects configureert automatisch een CI-build en een versietrigger. Uw code blijft in uw GitHub-opslagplaats of andere externe opslagplaats. 

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Wijzigingen doorvoeren in GitHub en automatisch implementeren in Azure 

U bent nu klaar om met een team samen te werken aan de app met behulp van een CI/CD-proces waarmee automatisch uw meest recente werk aan uw website wordt geïmplementeerd. Bij elke wijziging in de GitHub-opslagplaats wordt een build gestart in Azure DevOps, en met een CD-pijplijn wordt een implementatie uitgevoerd in Azure.

1. Breng een wijziging aan in uw toepassing en voer deze door in uw GitHub-opslagplaats.  
    Na enkele ogenblikken wordt een build in Azure Pipelines gestart. U kunt de buildstatus bewaken via het DevOps Projects-dashboard of in de browser met uw Azure DevOps-organisatie.

1. Wanneer de build is voltooid, vernieuwt u de toepassing om de wijzigingen te controleren.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>De CI/CD-pijplijn van Azure Pipelines onderzoeken

In Azure DevOps Projects wordt automatisch een CI/CD-pijplijn geconfigureerd in Azure Pipelines. U kunt de pijplijn verkennen en zo nodig aanpassen. Ga als volgt te werk om vertrouwd te raken met de build- en release-pijplijnen:

1. Selecteer boven in het DevOps Projects-dashboard de optie **Build-pijplijnen**.  
    Op een tabblad in de browser wordt de build-pijplijn voor het nieuwe project weergegeven.

1. Wijs het veld **Status** aan en selecteer het beletselteken (...).  
    Er wordt een menu met verschillende opties weergegeven, bijvoorbeeld om een nieuwe build in de wachtrij te plaatsen, een build te onderbreken of de build-pijplijn te bewerken.

1. Selecteer **Bewerken**.

1. In dit deelvenster kunt u de verschillende taken voor uw build-pijplijn onderzoeken.  
    In de build worden verschillende taken uitgevoerd, zoals het ophalen van bronnen uit de Git-opslagplaats, het herstellen van afhankelijkheden, en het publiceren van uitvoergegevens die worden gebruikt voor implementaties.

1. Selecteer boven aan de build-pijplijn de naam van de build-pijplijn.

1. Wijzig de naam van de build-pijplijn in een gebruiksvriendelijkere naam. Selecteer **Opslaan en wachtrij** en selecteer **Opslaan**.

1. Selecteer onder de naam van de build-pijplijn de optie **Geschiedenis**.  
    U ziet een audittrail van recente wijzigingen voor de build. In Azure DevOps worden alle wijzigingen in de build-pijplijn bijgehouden en krijgt u de mogelijkheid om versies te vergelijken.

1. Selecteer **Triggers**.  
    In Azure DevOps Projects wordt automatisch een CI-trigger gemaakt en met elke doorvoering naar de opslagplaats wordt een nieuwe build gestart. U kunt eventueel kiezen of u branches van het CI-proces wilt opnemen of uitsluiten.

1. Selecteer **Retentie**.  
        Afhankelijk van het scenario kunt u beleidsregels opgeven om een bepaald aantal builds te behouden of te verwijderen.

1. Selecteer **Build en release** en selecteer vervolgens **Releases**.  
    In Azure DevOps Projects wordt een release-pijplijn gemaakt om implementaties in Azure te beheren.

1. Selecteer het beletselteken (...) naast uw release-pijplijn en selecteer **Bewerken**.  
    De release-pijplijn bevat een *pijplijn* die het releaseproces definieert. 
    
1. Onder **Artefacten** selecteert u **Neerzetten**.  
    Met de build-pijplijn die u in de vorige stappen hebt onderzocht, wordt de uitvoer geproduceerd die wordt gebruikt voor het artefact. 

1. Selecteer naast het pictogram **Neerzetten** de optie **Continue implementatietrigger**.  
    Deze release-pipeline heeft een ingeschakelde CD-trigger die een implementatie uitvoert telkens wanneer er een nieuw build-artefact beschikbaar is. U kunt de trigger eventueel uitschakelen zodat de implementaties handmatig moeten worden uitgevoerd. 

1. Selecteer aan de linkerkant **Taken**.  
    Taken zijn de acties die in het implementatieproces worden uitgevoerd. In dit voorbeeld is een taak gemaakt om te implementeren naar de Azure App-service.

1. Selecteer aan de rechterkant **Versies weergeven** om een versiegeschiedenis weer te geven.

1. Selecteer het beletselteken (...) naast een versie en selecteer **Openen**.  
    U kunt verschillende menu's verkennen, zoals een releaseoverzicht, gekoppelde werkitems en tests.

1. Selecteer **Doorvoeringen**.  
    In deze weergave worden de codedoorvoeringen weergegeven die zijn gekoppeld aan deze implementatie. 

1. Selecteer **Logboeken**.  
    De logboeken bevatten nuttige informatie over het implementatieproces. U kunt beide weergeven tijdens en na de implementaties.

## <a name="configure-azure-application-insights-monitoring"></a>Azure Application Insights-bewaking configureren

Met Azure Application Insights kunt u eenvoudig de prestaties en het gebruik van een webtoepassing controleren. Met Azure DevOps Projects wordt automatisch een Application Insights-resource voor de toepassing geconfigureerd. U kunt verder diverse waarschuwingen en bewakingsopties configureren, indien nodig.

1. Ga in de Azure-portal naar het dashboard van DevOps Projects. 

1. Selecteer rechtsonder de koppeling **Application Insights** voor de app.  
    Het deelvenster **Application Insights** wordt geopend. Deze weergave bevat controlegegevens over gebruik, prestaties en beschikbaarheid voor uw app.

    ![Het deelvenster Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Selecteer **Tijdsbereik** en selecteer vervolgens **Afgelopen uur**. Selecteer **Bijwerken** om de resultaten te filteren.  
    U kunt nu alle activiteiten van de afgelopen 60 minuten bekijken. Selecteer **x** om het tijdsbereik af te sluiten.

1. Selecteer **Waarschuwingen** en selecteer vervolgens **Metrische waarschuwing toevoegen**. 

1. Voer een naam in voor de waarschuwing.

1. Selecteer in de vervolgkeuzelijst **Bronwijziging op** uw **App Service-resource**. <!-- Please confirm whether this should be "Source Alter on" or "Source Alert on" -->

1. Verken in de vervolgkeuzelijst **Metrische gegevens** de verschillende metrische waarschuwingsgegevens.  
    De standaardwaarschuwing is voor een **serverreactietijd langer dan 1 seconde**. U kunt eenvoudig tal van waarschuwingen configureren om de controlemogelijkheden van uw app te verbeteren.

1. Schakel het selectievakje **Eigenaars, bijdragers en lezers op de hoogte brengen via e-mail** in.  
    U kunt eventueel aanvullende acties uitvoeren wanneer een waarschuwing wordt weergegeven, door een logische app van Azure uit te voeren.

1. Selecteer **OK** om de waarschuwing te maken.  
    Na enkele ogenblikken wordt de waarschuwing als actief weergegeven op het dashboard.
    
1. Sluit het gebied **Waarschuwingen** af en ga terug naar het deelvenster **Application Insights**.

1. Selecteer **Beschikbaarheid** en selecteer vervolgens **Test toevoegen**. 

1. Voer een testnaam in en selecteer vervolgens **Maken**.  
    Hiermee maakt u eenvoudige ping-test om de beschikbaarheid van uw toepassing te controleren. Na een paar minuten zijn de resultaten beschikbaar en toont het Application Insights-dashboard een beschikbaarheidsstatus.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de Azure-app-service en de gerelateerde resources die u in deze zelfstudie hebt gemaakt, verwijderen wanneer u ze niet meer nodig hebt. Gebruik hiervoor de functionaliteit **Verwijderen** op het DevOps Projects-dashboard.

## <a name="next-steps"></a>Volgende stappen

Toen u het CI/CD-proces in deze quickstart configureerde, zijn er automatisch een build- en release-pijplijn in Azure DevOps Projects gemaakt. U kunt deze build- en release-pipelines desgewenst wijzigen in overeenstemming met de behoeften van uw team. U hebt geleerd hoe u:

> [!div class="checklist"]
> * DevOps Projects gebruiken om een CI/CD-pijplijn te maken
> * De toegang tot uw GitHub-opslagplaats configureren en een framework kiezen
> * Azure DevOps en een Azure-abonnement configureren 
> * Wijzigingen doorvoeren in GitHub en automatisch implementeren in Azure
> * De CI/CD-pijplijn van Azure Pipelines onderzoeken
> * Azure Application Insights-bewaking configureren
> * Resources opschonen

Voor meer informatie over de CI/CD-pijplijn raadpleegt u:

> [!div class="nextstepaction"]
> [Een CD-pijplijn (continue implementatie) met meerdere fasen definiëren](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
