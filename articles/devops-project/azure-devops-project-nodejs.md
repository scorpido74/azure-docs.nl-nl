---
title: Een CI/CD-pijp lijn maken voor node. js met Azure DevOps Projects
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
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
monikerRange: vsts
ms.openlocfilehash: 35eebeaa393ff75ada11752aaf9f195efddfa12b
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049809"
---
#  <a name="quickstart-create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-projects"></a>Snelstartgids: een CI/CD-pijp lijn maken in azure-pijp lijnen voor node. js met Azure DevOps Projects

In deze Quick Start gebruikt u de vereenvoudigde Azure DevOps Projects-ervaring voor het instellen van een pipeline voor continue integratie (CI) en continue levering (CD) voor uw node. js-app in azure-pijp lijnen. U kunt Azure DevOps Projects gebruiken voor het instellen van alles wat u nodig hebt voor het ontwikkelen, implementeren en bewaken van uw app. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Een [Azure DevOps](https://azure.microsoft.com/services/devops/) -account en-organisatie.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In DevOps Projects wordt een CI/CD-pijplijn gemaakt in Azure Pipelines. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. Met DevOps Projects worden ook Azure-resources gemaakt in het Azure-abonnement van uw keuze.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en selecteer in het linkerdeel venster **een resource maken**. 

   ![Een Azure-resource maken in Azure Portal](_img/azure-devops-project-nodejs/create-azure-resource.png)

1. Zoek en selecteer **DevOps projects**en selecteer vervolgens **maken**.

## <a name="select-a-sample-application-and-azure-service"></a>Een voorbeeldtoepassing en Azure-service selecteren

1. Selecteer de Node.js-voorbeeldtoepassing.  
    De Node.js-voorbeelden omvatten een keuze uit verschillende toepassingsframeworks.

1. Het standaardvoorbeeldframework is Express.js. Laat de standaardinstelling ongewijzigd en selecteer **Volgende**.  
    **Web App on Windows** is het standaardimplementatiedoel.  Het toepassingsframework, dat u eerder hebt gekozen, bepaalt welk type implementatiedoel hier beschikbaar is voor de Azure-service.  

2. Laat de standaardservice ongewijzigd en selecteer **Volgende**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps en een Azure-abonnement configureren 

1. Maak een nieuwe Azure DevOps-organisatie of kies een bestaande organisatie. 
   
   1. Voer een naam in voor het project.
      
   1. Selecteer uw Azure-abonnement en locatie, voer een naam in voor uw toepassing en selecteer **Gereed**.  
      Na enkele minuten wordt het DevOps Projects-dashboard weergegeven in de Azure-portal. Er wordt een voorbeeldtoepassing ingesteld in een opslagplaats in uw Azure DevOps-organisatie, er wordt een build uitgevoerd en de toepassing wordt geïmplementeerd in Azure. Dit dashboard biedt meer inzicht in uw codeopslagplaats, CI/CD-pijplijn en toepassing in Azure.
   
1. Selecteer **Bladeren** om de actieve toepassing weer te geven.
   
   ![Dashboard weergave van CI/CD-pijp lijn](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 

In DevOps Projects worden automatisch een CI-build en een releasetrigger geconfigureerd.  U bent nu klaar om met een team samen te werken aan uw Node.js-app met een CI/CD-proces dat automatisch uw meest recente werk op uw website implementeert.

## <a name="commit-code-changes-and-execute-cicd"></a>Codewijzigingen doorvoeren en CI/CD uitvoeren

Met DevOps Projects wordt een Git-opslagplaats gemaakt in Azure Repos of in GitHub. Voer de volgende stappen uit om de opslagplaats weer te geven en codewijzigingen aan te brengen in de toepassing.

1. Selecteer aan de linkerkant van het DevOps Projects-dashboard de koppeling voor uw master branch.  
Met deze koppeling opent u een weergave in de zojuist gemaakte Git-opslagplaats.

1. Als u de kloon-URL van de opslagplaats wilt weergeven, selecteert u **Klonen** in de rechterbovenhoek van de browser.   
    U kunt uw Git-opslagplaats klonen in uw favoriete IDE. In de volgende stappen kunt u de webbrowser gebruiken om codewijzigingen rechtstreeks aan te brengen en door te voeren in de master branch.

1. Ga aan de linkerkant van de browser naar het bestand **views/index.pug**.

1. Selecteer **Bewerken** en breng vervolgens een wijziging aan in de h2-kop.  
    Voer bijvoorbeeld meteen aan de **slag met Azure DevOps projects** of breng een andere wijziging aan.

1. Selecteer **Doorvoeren** en sla de wijzigingen op.

1. Ga in de browser naar het DevOps Projects-dashboard.   
Als het goed is, ziet u nu dat er een build wordt gemaakt. De zojuist aangebrachte wijzigingen worden automatisch gebouwd en geïmplementeerd via een CI/CD-pijplijn.

## <a name="examine-the-azure-cicd-pipeline"></a>De Azure-CI/CD-pijplijn onderzoeken

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
Met de release-pijplijn wordt het releaseproces gedefinieerd.

12. Onder **Artefacten** selecteert u **Neerzetten**.  
    De build-pijplijn die u in de vorige stappen hebt onderzocht, produceert de uitvoer die wordt gebruikt voor het artefact. 

1. Selecteer naast het pictogram **Neerzetten** de optie **Continue implementatietrigger**.  
Deze release-pijplijn heeft een ingeschakelde CD-trigger op basis waarvan een implementatie wordt uitgevoerd telkens wanneer een nieuw build-artefact beschikbaar is. U kunt de trigger eventueel uitschakelen zodat de implementaties handmatig moeten worden uitgevoerd. 


1. Selecteer aan de linkerkant **Taken**.   
De taken zijn de acties die tijdens het implementatieproces worden uitgevoerd. In dit voorbeeld is een taak gemaakt om te implementeren in Azure App Service.


1. Selecteer aan de rechterkant **Releases weergeven**.  
In deze weergave wordt een versiesgeschiedenis weergegeven.

1. Selecteer het beletselteken naast een van de releases en selecteer vervolgens **Openen**.  
Er zijn verschillende menu's die u kunt verkennen, zoals een versieoverzicht, gekoppelde werkitems en tests.

1. Selecteer **Doorvoeringen**.   
In deze weergave worden codedoorvoeringen getoond die zijn gekoppeld aan de specifieke implementatie.

1. Selecteer **Logboeken**.  
De logboeken bevatten nuttige informatie over het implementatieproces. Ze kunnen worden weergegeven tijdens en na de implementaties.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt Azure App Service en gerelateerde resources die u hebt gemaakt, verwijderen wanneer u ze niet meer nodig hebt. Gebruik de functionaliteit **Verwijderen** op het DevOps Projects-dashboard.


## <a name="next-steps"></a>Volgende stappen

De build en pijplijnen zijn automatisch gemaakt toen u het CI/CD-proces configureerde. U kunt deze build- en release-pipelines desgewenst wijzigen in overeenstemming met de behoeften van uw team. Voor meer informatie over de CI/CD-pijplijn raadpleegt u:

> [!div class="nextstepaction"]
> [CD-proces aanpassen](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Video's

> [!VIDEO https://www.youtube.com/embed/3etwjubReJs]
