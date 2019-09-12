---
title: 'Quickstart: Een CI/CD-pijplijn voor Ruby on Rails maken met behulp van Azure DevOps Projects'
description: Azure DevOps Projects zorgt ervoor dat u eenvoudig aan de slag kunt met Azure. Hiermee kunt u een web-app in Ruby voor een Azure-service in slechts enkele stappen starten.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
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
ms.openlocfilehash: 5cb47cdd76d1de284c4dc6dbdfbfedd0095653ab
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899570"
---
# <a name="create-a-cicd-pipeline-for-ruby-on-rails-by-using-azure-devops-projects"></a>Een CI/CD-pijplijn voor Ruby on Rails maken met behulp van Azure DevOps Projects

Configureer continue integratie (CI) en continue levering (CD) voor uw Ruby on Rails-app met het behulp van Azure DevOps Projects. DevOps Projects vereenvoudigt de eerste configuratie van een build- en release-pijplijn van Azure DevOps.

Als u geen Azure-abonnement hebt, kunt u er gratis een krijgen via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Azure DevOps Projects maakt een CI/CD-pijplijn in Azure-opslagplaatsen. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. Met DevOps Projects worden ook Azure-resources gemaakt in het Azure-abonnement van uw keuze.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **Een resource maken** in het linkerdeelvenster.

1. Typ in het zoekvak **DevOps Projects** en selecteer **Maken**.

    ![Het DevOps Projects-dashboard](_img/azure-devops-project-github/fullbrowser.png)

## <a name="select-a-sample-app-and-azure-service"></a>Selecteer een voorbeeld-app en een Azure-service

1. Selecteer de **Ruby**-voorbeeld-app.

1. Selecteer het **Ruby on Rails**-toepassingsframework. Selecteer **Volgende** als u klaar bent.

1. **Web App on Linux** is het standaardimplementatiedoel.  
    Desgewenst kunt u ook **Web App for Containers** selecteren. Het toepassingsframework, dat u eerder hebt gekozen, bepaalt welk type implementatiedoel hier beschikbaar is voor de Azure-service. 
    
1. Selecteer de gewenste doelservice en selecteer **Volgende**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps en een Azure-abonnement configureren 

1. Maak gratis een nieuwe Azure DevOps-organisatie of kies een bestaande organisatie. 

1. Voer een naam in voor uw Azure DevOps-project. 

1. Selecteer uw Azure-abonnement en locatie, voer een naam in voor de app en selecteer **Gereed**.  
    Na enkele minuten wordt het DevOps Projects-dashboard weergegeven in de Azure-portal. Er wordt een voorbeeld-app ingesteld in een opslagplaats in uw Azure DevOps-organisatie, er wordt een build uitgevoerd en de app wordt geïmplementeerd in Azure. 
    
    Het dashboard biedt meer inzicht in uw codeopslagplaats, CI/CD-pijplijn en app in Azure. Selecteer aan de rechterkant **Bladeren** om de actieve app weer te geven.

    ![Dashboardweergave](_img/azure-devops-project-go/dashboardnopreview.png) 

## <a name="commit-your-code-changes-and-execute-the-cicd"></a>De codewijzigingen doorvoeren en de CI/CD uitvoeren

Met DevOps Projects wordt een Git-opslagplaats gemaakt in Azure Pipelines of in GitHub. Ga als volgt te werk om de opslagplaats weer te geven en codewijzigingen aan de brengen in de app:

1. Selecteer aan de linkerkant van het DevOps Projects-dashboard de koppeling voor uw master branch.  
    Met deze koppeling opent u een weergave in de zojuist gemaakte Git-opslagplaats.

1. Als u de kloon-URL van de opslagplaats wilt weergeven, selecteert u **Klonen** in de rechterbovenhoek.  
    U kunt de Git-opslagplaats klonen in uw favoriete IDE. In de volgende stappen kunt u de webbrowser gebruiken om codewijzigingen rechtstreeks aan te brengen en door te voeren in de master branch.

1. Ga aan de linkerkant naar het bestand *app/views/pages/home.html.erb* en selecteer **Bewerken**.

1. Breng een wijziging aan in het bestand. Wijzig bijvoorbeeld wat tekst binnen een van de div-tags.

1. Selecteer **Doorvoeren** en sla de wijzigingen op.

1. Ga in de browser naar het DevOps Projects-dashboard.  
    Er wordt nu een build uitgevoerd. De aangebrachte wijzigingen worden automatisch gebouwd en geïmplementeerd via een CI/CD-pijplijn.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>De CI/CD-pijplijn van Azure Pipelines onderzoeken

Azure DevOps Projects configureert automatisch een volledige Azure-CI/CD-pijplijn in uw Azure DevOps-organisatie. U kunt de pijplijn verkennen en zo nodig aanpassen. Ga als volgt te werk om vertrouwd te raken met de build- en release-pijplijnen van Azure DevOps:

1. Ga naar het DevOps Projects-dashboard.

1. Selecteer **Pijplijnen bouwen** bovenaan.  
    Op een tabblad in de browser wordt de build-pijplijn voor het nieuwe project weergegeven.

1. Wijs het veld **Status** aan en selecteer het beletselteken (...).  
    Er wordt een menu met verschillende opties weergegeven, bijvoorbeeld om een nieuwe build in de wachtrij te plaatsen, een build te onderbreken of de build-pijplijn te bewerken.

1. Selecteer **Bewerken**.

1. In dit deelvenster kunt u de verschillende taken voor uw build-pijplijn onderzoeken.  
    In de build worden verschillende taken uitgevoerd, zoals het ophalen van bronnen uit de Git-opslagplaats, het herstellen van afhankelijkheden, en het publiceren van uitvoergegevens die worden gebruikt voor implementaties.

1. Selecteer boven aan de build-pijplijn de naam van de build-pijplijn.

1. Wijzig de naam van de build-pijplijn in een gebruiksvriendelijkere naam. Selecteer **Opslaan en wachtrij** en selecteer **Opslaan**.

1. Selecteer onder de naam van de build-pijplijn de optie **Geschiedenis**.  
    In dit deelvenster ziet u een audittrail van recente wijzigingen voor de build. In Azure DevOps worden alle wijzigingen in de build-pijplijn bijgehouden en krijgt u de mogelijkheid om versies te vergelijken.

1. Selecteer **Triggers**.  
    In DevOps Projects wordt automatisch een CI-trigger gemaakt en met elke doorvoering naar de opslagplaats wordt een nieuwe build gestart. U kunt eventueel kiezen of u branches van het CI-proces wilt opnemen of uitsluiten.

1. Selecteer **Retentie**.  
    Afhankelijk van het scenario kunt u beleidsregels opgeven om een bepaald aantal builds te behouden of te verwijderen.

1. Selecteer **Build en release** en selecteer vervolgens **Releases**.  
    In DevOps Projects wordt een release-pijplijn gemaakt om implementaties in Azure te beheren.

1. Selecteer het beletselteken (...) naast uw release-pijplijn en selecteer **Bewerken**.  
    De release-pijplijn bevat een *pijplijn* die het releaseproces definieert.

1. Onder **Artefacten** selecteert u **Neerzetten**.  
    De build-pijplijn die u eerder hebt onderzocht, produceert de uitvoer die wordt gebruikt voor het artefact. 

1. Selecteer **Continue implementatietrigger** rechts van het pictogram **Neerzetten**.  
    Deze release-pijplijn heeft een ingeschakelde CD-trigger die een implementatie uitvoert telkens wanneer een nieuw build-artefact beschikbaar is. U kunt de trigger eventueel uitschakelen zodat de implementaties handmatig moeten worden uitgevoerd. 

1. Selecteer aan de linkerkant **Taken**.  
    Taken zijn de acties die tijdens het implementatieproces worden uitgevoerd. In dit voorbeeld is een taak gemaakt om te implementeren in Azure App Service.

1. Selecteer aan de rechterkant **Versies weergeven** om een versiegeschiedenis weer te geven.

1. Selecteer het beletselteken (...) naast een versie en selecteer **Openen**.  
    U kunt verschillende menu's verkennen, zoals een versieoverzicht, gekoppelde werkitems en tests.

1. Selecteer **Doorvoeringen**.  
    In deze weergave worden de codedoorvoeringen weergegeven die zijn gekoppeld aan deze implementatie. 

1. Selecteer **Logboeken**.  
    De logboeken bevatten nuttige informatie over het implementatieproces. U kunt beide weergeven tijdens en na de implementaties.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt het Azure App Service-exemplaar en de gerelateerde resources die u in deze quickstart hebt gemaakt, verwijderen wanneer u ze niet meer nodig hebt. Gebruik hiervoor de functionaliteit **Verwijderen** op het DevOps Projects-dashboard.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het wijzigen van de build- en release-pijplijnen in overeenstemming met de behoeften van uw team, raadpleegt u:

> [!div class="nextstepaction"]
> [Een CD-pijplijn (continue implementatie) met meerdere fasen definiëren](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
