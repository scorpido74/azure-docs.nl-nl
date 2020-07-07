---
title: 'Snelstartgids: een CI/CD-pijp lijn maken voor python met Azure DevOps starter'
description: DevOps Starter maakt het eenvoudig om aan de slag te gaan met Azure. Hiermee kunt u een web-app voor een Azure-service van uw keuze starten in slechts enkele stappen.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: mlearned
ms.custom: mvc, tracking-python
ms.openlocfilehash: e148d50af39e69750c3024d98abc833e40654705
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84558743"
---
# <a name="create-a-cicd-pipeline-for-python-with-azure-devops-starter"></a>Een CI/CD-pijp lijn maken voor python met Azure DevOps starter

In deze Quick Start gebruikt u de vereenvoudigde Azure DevOps starter-ervaring voor het instellen van een doorlopende integratie (CI) en een continue levering (CD)-pijp lijn voor uw python-app in azure-pijp lijnen. U kunt Azure DevOps starter gebruiken voor het instellen van alles wat u nodig hebt voor het ontwikkelen, implementeren en bewaken van uw app. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 
- Een [Azure DevOps](https://azure.microsoft.com/services/devops/) -account en-organisatie.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

DevOps Starter maakt een CI/CD-pijp lijn in azure-pijp lijnen. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. DevOps Starter maakt ook Azure-resources in het Azure-abonnement van uw keuze.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 

1. Typ **DevOps starter**in het zoekvak en selecteer. Klik op **toevoegen** om een nieuw item te maken.

    ![Het DevOps-starter dash board](_img/azure-devops-starter-aks/search-devops-starter.png) 

## <a name="select-a-sample-application-and-azure-service"></a>Een voorbeeldtoepassing en Azure-service selecteren

1. Selecteer de Python-voorbeeldtoepassing. De Python-voorbeelden omvatten een keuze uit verschillende toepassingsframeworks.

1. Het standaardvoorbeeldframework is Django. Laat de standaardinstelling ongewijzigd en selecteer vervolgens **Volgende**. Web App for Containers is het standaardimplementatiedoel. Het toepassingsframework dat u eerder hebt gekozen, bepaalt welk type implementatiedoel hier beschikbaar is voor de Azure-service. 

3. Laat de standaardservice ongewijzigd en selecteer vervolgens **Volgende**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps en een Azure-abonnement configureren 

1. Maak een nieuwe Azure DevOps-organisatie of kies een bestaande organisatie. 

    1. Voer in Azure DevOps een naam in voor het project.  

    1. Selecteer uw Azure-abonnement en locatie, voer een naam in voor de toepassing en selecteer **Gereed**.  
    
     Na enkele minuten wordt het start dashboard weer gegeven in de Azure Portal. Er wordt een voorbeeldtoepassing ingesteld in een opslagplaats in uw Azure DevOps-organisatie, er wordt een build uitgevoerd en de toepassing wordt geïmplementeerd in Azure. Dit dashboard biedt meer inzicht in uw codeopslagplaats, CI/CD-pijplijn en toepassing in Azure.  
    
2. Selecteer **Bladeren** om de actieve toepassing weer te geven.

    ![Dashboardweergave](_img/azure-devops-project-python/dashboardnopreview.png) 
    
   In DevOps Projects worden automatisch een CI-build en een releasetrigger geconfigureerd. U bent nu klaar om met een team samen te werken aan een Python-app met behulp van een CI/CD-proces waarmee automatisch uw meest recente werk op uw website wordt geïmplementeerd.

## <a name="commit-code-changes-and-execute-cicd"></a>Codewijzigingen doorvoeren en CI/CD uitvoeren

DevOps Starter maakt een Git-opslag plaats in azure opslag plaatsen of GitHub. Ga als volgt te werk om de opslagplaats weer te geven en codewijzigingen aan de brengen in de toepassing: 

1. Selecteer aan de linkerkant van het DevOps starter-dash board de koppeling voor uw hoofd vertakking. Met deze koppeling opent u een weergave in de zojuist gemaakte Git-opslagplaats.

1. Als u de kloon-URL van de opslagplaats wilt weergeven, selecteert u **Klonen** in de rechterbovenhoek van de browser. U kunt uw Git-opslagplaats klonen in uw favoriete IDE. In de volgende stappen kunt u de webbrowser gebruiken om codewijzigingen rechtstreeks aan te brengen en door te voeren in de master branch.

1. Ga aan de linkerkant naar het bestand **app/templates/app/index.html**.

1. Selecteer **Bewerken** en breng een wijziging aan in de tekst. Wijzig bijvoorbeeld een stuk tekst voor een van de div-tags.

1. Selecteer **Doorvoeren** en sla vervolgens de wijzigingen op.

1. Ga in uw browser naar het DevOps-starter-dash board. Als het goed is, ziet u nu dat er een build wordt gemaakt. De zojuist aangebrachte wijzigingen worden automatisch gebouwd en geïmplementeerd via een CI/CD-pijplijn.

## <a name="examine-the-cicd-pipeline"></a>De CI/CD-pijplijn onderzoeken

In de vorige stap heeft DevOps starter automatisch een volledige CI/CD-pijp lijn geconfigureerd. U kunt de pijplijn verkennen en zo nodig aanpassen. Ga als volgt te werk om vertrouwd te raken met de build- en release-pijplijnen:

1. Selecteer boven aan het DevOps-starter-dash board **Build pijp lijnen**. Op een tabblad in de browser wordt de build-pijplijn voor het nieuwe project weergegeven.

1. Wijs het veld **status** aan en selecteer vervolgens het **weglatings teken** (...). In een menu worden verschillende opties weer gegeven, zoals het in de wachtrij plaatsen van een nieuwe build, het onderbreken van een build en het bewerken van de build-pijp lijn.

1. Selecteer **Bewerken**.

1. In dit deelvenster kunt u de verschillende taken voor uw build-pijplijn onderzoeken. In de build worden verschillende taken uitgevoerd, zoals het ophalen van bronnen uit de Git-opslagplaats, het herstellen van afhankelijkheden, en het publiceren van uitvoergegevens die worden gebruikt voor implementaties.

1. Selecteer bovenaan de build-pijplijn de naam van de build-pijplijn.

1. Wijzig de naam van de build-pijplijn in een meer beschrijvende naam. Selecteer **Opslaan en wachtrij** en selecteer vervolgens **Opslaan**.

1. Selecteer onder de naam van de build-pipeline de optie **Geschiedenis**. U ziet een audittrail van recente wijzigingen voor de build. In Azure DevOps worden alle wijzigingen in de build-pijplijn bijgehouden en krijgt u de mogelijkheid om versies te vergelijken.

1. Selecteer **Triggers**. DevOps Starter maakt automatisch een CI-trigger en elke door voering aan de opslag plaats start een nieuwe build. U kunt desgewenst kiezen of u vertakkingen van het CI-proces wilt opnemen of uitsluiten.

1. Selecteer **Retentie**. Afhankelijk van het scenario kunt u beleidsregels opgeven om een bepaald aantal builds te behouden of te verwijderen.

1. Selecteer **Build en release** en kies vervolgens **Releases**.   
 In DevOps Projects wordt een release-pijplijn gemaakt om implementaties in Azure te beheren.

1. Selecteer het beletselteken (...) naast de release-pijplijn en selecteer **Bewerken**. Met de release-pijplijn wordt het releaseproces gedefinieerd.  
        
12. Onder **Artefacten** selecteert u **Neerzetten**. Met de build-pijplijn die u in de vorige stappen hebt onderzocht, wordt de uitvoer geproduceerd die wordt gebruikt voor het artefact. 

1. Selecteer naast het pictogram **Neerzetten** de optie **Continue implementatietrigger**. Deze release-pijplijn heeft een ingeschakelde CD-trigger op basis waarvan een implementatie wordt uitgevoerd telkens wanneer een nieuw build-artefact beschikbaar is. U kunt de trigger eventueel uitschakelen zodat de implementaties handmatig moeten worden uitgevoerd. 

1. Selecteer aan de linkerkant **Taken**. De taken zijn de acties die tijdens het implementatieproces worden uitgevoerd. In dit voorbeeld is een taak gemaakt om te implementeren in Azure App Service.

1. Selecteer aan de rechterkant **Releases weergeven** om een releasegeschiedenis weer te geven.  
        
1. Selecteer het beletselteken (...) naast een van de releases en selecteer vervolgens **Openen**. Er zijn verschillende menu's die u in deze weergave kunt verkennen, zoals een releaseoverzicht, gekoppelde werkitems en tests.

1. Selecteer **Doorvoeringen**. In deze weergave worden de codedoorvoeringen getoond die zijn gekoppeld aan de specifieke implementatie. 

1. Selecteer **Logboeken**. De logboeken bevatten nuttige informatie over het implementatieproces. U kunt beide weergeven tijdens en na de implementaties.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt Azure App Service en gerelateerde resources verwijderen wanneer u ze niet meer nodig hebt. Gebruik de **verwijderings** functionaliteit op het DevOps-starter-dash board.

## <a name="next-steps"></a>Volgende stappen

De build en pijplijnen zijn automatisch gemaakt toen u het CI/CD-proces configureerde. U kunt deze build- en release-pipelines desgewenst wijzigen in overeenstemming met de behoeften van uw team. Voor meer informatie over de CI/CD-pijplijn, zie:

> [!div class="nextstepaction"]
> [CD-proces aanpassen](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
