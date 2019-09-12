---
title: 'Quickstart: Een CI/CD-pijp lijn maken voor Java-Azure DevOps Projects'
description: Met DevOps Projects kunt u eenvoudig aan de slag met Azure. Hiermee kunt u een web-app voor een Azure-service van uw keuze starten in slechts enkele stappen.
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
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
monikerRange: vsts
ms.openlocfilehash: d395855de6bb16841d54821c87a6af6d5d62c11c
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898058"
---
# <a name="quickstart-set-up-a-cicd-pipeline-for-a-java-app-with-azure-devops-projects"></a>Quickstart: Een CI/CD-pijp lijn instellen voor een Java-app met Azure DevOps Projects

In dit Quick Start-artikel gebruikt u Azure DevOps Projects vereenvoudigde ervaring voor het instellen van een doorlopende integratie (CI) en een continue levering (CD)-pijp lijn voor uw Java-app in azure-pijp lijnen. U kunt Azure DevOps Projects ook gebruiken om andere Azure-resources te maken.  

Als u geen Azure-abonnement hebt, kunt u er gratis een krijgen via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

  In DevOps Projects wordt een CI/CD-pijplijn gemaakt in Azure Pipelines. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. Met DevOps Projects worden ook Azure-resources gemaakt in het Azure-abonnement van uw keuze.

1. Meld u aan bij de [Microsoft Azure Portal](https://portal.azure.com).

1. Selecteer in het linkerdeelvenster de optie **Een resource maken**, en zoek vervolgens naar **DevOps Projects**.  

2. Selecteer **Maken**.

    ![Configuratie voor continue levering starten](_img/azure-devops-project-java/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Een voorbeeldtoepassing en Azure-service selecteren

1. Selecteer de Java-voorbeeldtoepassing.  
De Java-voorbeelden omvatten een keuze uit verschillende toepassingsframeworks.

1. Het standaardvoorbeeldframework is Spring. Laat de standaardinstelling ongewijzigd en selecteer **Volgende**.  Web App for Containers is het standaardimplementatiedoel. Het toepassingsframework, dat u eerder hebt gekozen, bepaalt welk type implementatiedoel hier beschikbaar is voor de Azure-service. 

2. Laat de standaardservice ongewijzigd en selecteer **Volgende**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps en een Azure-abonnement configureren 

1. Maak een nieuwe Azure DevOps-organisatie of kies een bestaande organisatie. 

    a. Kies een naam voor het project. 

    b. Selecteer uw Azure-abonnement en locatie, kies een naam voor de toepassing en selecteer vervolgens **Gereed**.  
    Na enkele minuten wordt het DevOps Projects-dashboard weergegeven in de Azure-portal. Er wordt een voorbeeldtoepassing ingesteld in een opslagplaats in uw Azure DevOps-organisatie, er wordt een build uitgevoerd en de toepassing wordt geïmplementeerd in Azure. Dit dashboard biedt meer inzicht in uw codeopslagplaats, CI/CD-pijplijn en toepassing in Azure.
    

2. Selecteer **Bladeren** om de actieve toepassing weer te geven.

    ![Dashboardweergave](_img/azure-devops-project-java/dashboardnopreview.png) 
    
   In DevOps Projects worden automatisch een CI-build en een releasetrigger geconfigureerd.  U bent nu klaar om samen met een team te werken aan uw Java-app met een CI/CD-proces dat automatisch uw meest recente werk in uw website implementeert.

## <a name="commit-code-changes-and-execute-cicd"></a>Codewijzigingen doorvoeren en CI/CD uitvoeren

Met DevOps Projects wordt een Git-opslagplaats gemaakt in Azure Repos of in GitHub. Ga als volgt te werk om de opslagplaats weer te geven en codewijzigingen aan de brengen in de toepassing:

1. Selecteer aan de linkerkant van het DevOps Projects-dashboard de koppeling voor uw master branch.  
Met deze koppeling opent u een weergave in de zojuist gemaakte Git-opslagplaats.

1. Als u de kloon-URL van de opslagplaats wilt weergeven, selecteert u **Klonen** in de rechterbovenhoek van de browser.   
    U kunt uw Git-opslagplaats klonen in uw favoriete IDE. In de volgende stappen kunt u de webbrowser gebruiken om codewijzigingen rechtstreeks aan te brengen en door te voeren in de master branch.

1. Ga aan de linkerkant van de browser naar het bestand **src/main/webapp/index.html**.

1. Selecteer **Bewerken** en breng een wijziging aan in de tekst.
    Wijzig bijvoorbeeld een stuk tekst voor een van de div-tags.

1. Selecteer **Doorvoeren** en sla de wijzigingen op.

1. Ga in de browser naar het DevOps Projects-dashboard.   
Als het goed is, ziet u nu dat er een build wordt gemaakt. De zojuist aangebrachte wijzigingen worden automatisch gebouwd en geïmplementeerd via een CI/CD-pijplijn.

## <a name="examine-the-cicd-pipeline"></a>De CI/CD-pijplijn onderzoeken

 In de vorige stap werd in DevOps Projects automatisch een volledige CI/CD-pijplijn geconfigureerd. U kunt de pijplijn verkennen en zo nodig aanpassen. Voer de volgende stappen uit om vertrouwd te raken met de build- en release-pijplijnen.

1. Selecteer boven in het DevOps Projects-dashboard de optie **Build-pijplijnen**.  
Met deze koppeling worden een browsertabblad en de build-pijplijn voor het nieuwe project geopend.

1. Wijs het veld **Status** aan en selecteer het beletselteken (...).  
    Met deze actie wordt een menu geopend waarin u diverse acties kunt starten, zoals een nieuwe build in de wachtrij plaatsen, een build onderbreken en de build-pijplijn bewerken.

1. Selecteer **Bewerken**.

1. In dit deelvenster kunt u de verschillende taken voor uw build-pijplijn onderzoeken.  
In de build worden verschillende taken uitgevoerd, zoals het ophalen van bronnen uit de Git-opslagplaats, het herstellen van afhankelijkheden, en het publiceren van uitvoergegevens die worden gebruikt voor implementaties.

1. Selecteer bovenaan de build-pijplijn de naam van de build-pijplijn.

1. Wijzig de naam van de build-pijplijn in een gebruiksvriendelijkere naam. Selecteer **Opslaan en wachtrij** en selecteer **Opslaan**.

1. Selecteer onder de naam van de build-pijplijn de optie **Geschiedenis**.   
In het deelvenster **Geschiedenis** ziet u een audittrail van recente wijzigingen voor de build.  Azure Pipelines houdt alle wijzigingen in de build-pijplijn bij en biedt de mogelijkheid om versies te vergelijken.

1. Selecteer **Triggers**.   
 In DevOps Projects is automatisch een CI-trigger gemaakt en met elke doorvoering naar de opslagplaats wordt een nieuwe build gestart.  U kunt desgewenst kiezen of u vertakkingen van het CI-proces wilt opnemen of uitsluiten.

1. Selecteer **Retentie**.   
Afhankelijk van het scenario kunt u beleidsregels opgeven om een bepaald aantal builds te behouden of te verwijderen.

1. Selecteer **Build en release** en selecteer vervolgens **Releases**.  
 In DevOps Projects wordt een release-pijplijn gemaakt om implementaties in Azure te beheren.

1. Selecteer aan de linkerkant het beletselteken (...) naast de release-pijplijn en selecteer **Bewerken**.  
De release-pijplijn bevat een pijplijn die het releaseproces definieert.  
    
12. Onder **Artefacten** selecteert u **Neerzetten**.  
Met de build-pijplijn die u in de vorige stappen hebt onderzocht, wordt de uitvoer geproduceerd die wordt gebruikt voor het artefact. 

1. Selecteer naast het pictogram **Neerzetten** de optie **Continue implementatietrigger**.  
Deze release-pijplijn heeft een ingeschakelde CD-trigger op basis waarvan een implementatie wordt uitgevoerd telkens wanneer een nieuw build-artefact beschikbaar is. U kunt de trigger eventueel uitschakelen zodat de implementaties handmatig moeten worden uitgevoerd. 

1. Selecteer aan de linkerkant **Taken**.   
De taken zijn de acties die tijdens het implementatieproces worden uitgevoerd. In dit voorbeeld is een taak gemaakt om te implementeren in Azure App Service.

1. Selecteer aan de rechterkant **Releases weergeven**.  
In deze weergave wordt een versiesgeschiedenis weergegeven.

1. Selecteer het beletselteken (...) naast een van de releases en selecteer vervolgens **Openen**.  
U kunt verschillende menu's verkennen, zoals een releaseoverzicht, gekoppelde werkitems en tests.

1. Selecteer **Doorvoeringen**.   
In deze weergave worden de codedoorvoeringen weergegeven die zijn gekoppeld aan deze implementatie. 

1. Selecteer **Logboeken**.  
De logboeken bevatten nuttige informatie over het implementatieproces. Ze kunnen worden weergegeven tijdens en na de implementaties.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt Azure App Service en andere gerelateerde resources verwijderen wanneer u ze niet meer nodig hebt. Gebruik de functionaliteit **Verwijderen** op het DevOps Projects-dashboard.

## <a name="next-steps"></a>Volgende stappen

De build en pijplijnen zijn automatisch gemaakt toen u het CI/CD-proces configureerde. U kunt deze build- en release-pipelines desgewenst wijzigen in overeenstemming met de behoeften van uw team. Voor meer informatie over de CI/CD-pijplijn raadpleegt u:

> [!div class="nextstepaction"]
> [CD-proces aanpassen](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
