---
title: 'Zelfstudie: ASP.NET Core-app implementeren naar Azure Kubernetes Service met Azure DevOps Projects'
description: Azure DevOps Projects zorgt ervoor dat u eenvoudig aan de slag kunt met Azure. Met Azure DevOps Projects kunt u een ASP.NET Core-app eenvoudig in slechts enkele stappen implementeren naar AKS (Azure Kubernetes Service).
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: b27d56d78296dc5500f97802f811a8923c4e87a8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "71969671"
---
# <a name="tutorial-deploy-aspnet-core-apps-to-azure-kubernetes-service-with-azure-devops-projects"></a>Zelfstudie: ASP.NET Core-app implementeren naar Azure Kubernetes Service met Azure DevOps Projects

Azure DevOps Projects biedt een vereenvoudigde ervaring waar u uw bestaande code en Git-opslagplaats gebruikt of een voorbeeldtoepassing kiest voor het maken van een pijplijn voor CI (Continue integratie) en CD (Continue levering) naar Azure. 

In DevOps Projects gebeurt ook het volgende:
* Er worden automatisch Azure-resources gemaakt, zoals Azure Kubernetes Service.
* In Azure DevOps wordt een release-pijplijn gemaakt en geconfigureerd om een build en release-pijplijn in te stellen voor CI/CD.
* Er wordt een Azure Application Insights-resource gemaakt voor de bewaking.
* [Azure Monitor voor containers](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) wordt ingeschakeld voor het bewaken van de prestaties voor de werkbelastingen van de container op het AKS-cluster

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Met behulp van DevOps Projects een ASP.NET Core-app implementeren naar AKS
> * Azure DevOps en een Azure-abonnement configureren 
> * Het AKS-cluster bestuderen
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * Wijzigingen doorvoeren in Git en automatisch implementeren naar Azure
> * Resources opschonen

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. U kunt er een gratis krijgen via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-projects-to-deploy-an-aspnet-core-app-to-aks"></a>Met behulp van DevOps Projects een ASP.NET Core-app implementeren naar AKS

In DevOps Projects wordt een CI/CD-pijplijn gemaakt in Azure Pipelines. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. DevOps Projects maakt ook Azure-resources, zoals een AKS-cluster, in het Azure-abonnement van uw keuze.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **Een resource maken** in het linkerdeelvenster.

1. Typ in het zoekvak **DevOps Projects** en selecteer **Maken**.

    ![Het DevOps Projects-dashboard](_img/azure-devops-project-github/fullbrowser.png)

1. Selecteer **.NET** en vervolgens **Volgende**.

1. Selecteer bij **Een toepassingsframework kiezen** de optie **ASP.NET Core**.

1. Selecteer **Kubernetes Service** en selecteer **Volgende**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps en een Azure-abonnement configureren

1. Maak een nieuwe Azure DevOps-organisatie of selecteer een bestaande organisatie. 

1. Voer een naam in voor het Azure DevOps-project. 

1. Selecteer uw Azure-abonnement.

1. Selecteer **Wijzigen** om aanvullende Azure-configuratie-instellingen te zien en het aantal knooppunten voor het AKS-cluster te identificeren.  
    In dit deelvenster vindt u verschillende opties voor het configureren van het type en de locatie van de Azure-services.
 
1. Verlaat het Azure-configuratiegebied en selecteer **Gereed**.  
    Na enkele minuten is het proces voltooid. Een voorbeeld-ASP.NET Core-app wordt in een Git-opslagplaats in uw Azure DevOps-organisatie ingesteld, een AKS-cluster wordt gemaakt, een CI/CD-pijplijn wordt uitgevoerd en uw app wordt geïmplementeerd naar Azure. 

    Nadat u dit alles hebt voltooid, wordt het dashboard van Azure DevOps Projects in de Azure-portal weergegeven. U kunt ook rechtstreeks vanuit **Alle resources** in de Azure-portal naar het dashboard van DevOps Projects gaan. 

    Dit dashboard biedt meer inzicht in uw Azure DevOps-codeopslagplaats, uw CI/CD-pijplijn en uw AKS-cluster. U kunt meer CI/CD-opties configureren in uw Azure DevOps-pijplijn. Selecteer aan de rechterkant **Bladeren** om uw actieve app weer te geven.

## <a name="examine-the-aks-cluster"></a>Het AKS-cluster bestuderen

DevOps Projects configureert automatisch een AKS-cluster, dat u kunt verkennen en aanpassen. Ga als volgt te werk om vertrouwd te raken met het AKS-cluster:

1. Ga naar het DevOps Projects-dashboard.

1. Selecteer de AKS-service aan de rechterkant.  
    Er wordt een deelvenster voor het AKS-cluster geopend. In deze weergave kunt u verschillende acties uitvoeren, zoals de containerstatus bewaken, zoeken in logboeken en het Kubernetes-dashboard openen.

1. Selecteer aan de rechterkant **Kubernetes-dashboard weergeven**.  
    Volg desgewenst de stappen om het Kubernetes-dashboard te openen.

## <a name="examine-the-ci-pipeline"></a>De CI-pijplijn onderzoeken

DevOps Projects configureert automatisch een Azure-CI/CD-pijplijn in uw Azure DevOps-organisatie. U kunt de pijplijn verkennen en aanpassen. Ga als volgt te werk om vertrouwd te raken met de pijplijn:

1. Ga naar het DevOps Projects-dashboard.

1. Selecteer boven in het DevOps Projects-dashboard de optie **Build-pijplijnen**.  
    Op een tabblad in de browser wordt de build-pijplijn voor het nieuwe project weergegeven.

1. Wijs het veld **Status** aan en selecteer het beletselteken (...).  
    Er wordt een menu met verschillende opties weergegeven, bijvoorbeeld om een nieuwe build in de wachtrij te plaatsen, een build te onderbreken of de build-pijplijn te bewerken.

1. Selecteer **Bewerken**.

1. In dit deelvenster kunt u de verschillende taken voor uw build-pijplijn onderzoeken.  
    In de build worden verschillende taken uitgevoerd, zoals het ophalen van bronnen uit de Git-opslagplaats, het herstellen van afhankelijkheden, en het publiceren van uitvoergegevens die worden gebruikt voor implementaties.

1. Selecteer bovenaan de build-pijplijn de naam van de build-pijplijn.

1. Wijzig de naam van de build-pijplijn in een meer beschrijvende naam. Selecteer **Opslaan en wachtrij** en selecteer vervolgens **Opslaan**.

1. Selecteer onder de naam van de build-pipeline de optie **Geschiedenis**.  
    In dit deelvenster ziet u een audittrail van recente wijzigingen voor de build. In Azure DevOps worden alle wijzigingen in de build-pijplijn bijgehouden en krijgt u de mogelijkheid om versies te vergelijken.

1. Selecteer **Triggers**.  
    In DevOps Projects wordt automatisch een CI-trigger gemaakt en met elke doorvoering naar de opslagplaats wordt een nieuwe build gestart. Desgewenst kunt u kiezen of u vertakkingen van het CI-proces wilt opnemen of uitsluiten.

1. Selecteer **Retentie**.  
    Afhankelijk van het scenario kunt u beleidsregels opgeven om een bepaald aantal builds te behouden of te verwijderen.

## <a name="examine-the-cd-release-pipeline"></a>De CD-release-pijplijn onderzoeken

In DevOps Projects worden automatisch de benodigde stappen gemaakt en geconfigureerd om vanuit uw Azure DevOps-organisatie te implementeren naar uw Azure-abonnement. Deze stappen omvatten het configureren van een Azure-serviceverbinding om Azure DevOps te verifiëren bij uw Azure-abonnement. Er wordt ook automatisch ook een release-pijplijn gemaakt, die de CD levert aan Azure. Voor meer informatie over de release-pijplijn doet u het volgende:

1. Selecteer **Build en release** en selecteer vervolgens **Releases**.  
    In DevOps Projects wordt een release-pijplijn gemaakt om implementaties in Azure te beheren.

1. Selecteer het beletselteken (...) naast de release-pijplijn en selecteer **Bewerken**.  
    De release-pijplijn bevat een *pijplijn* die het releaseproces definieert.

1. Onder **Artefacten** selecteert u **Neerzetten**.  
    Met de build-pijplijn die u in de vorige stappen hebt onderzocht, wordt de uitvoer geproduceerd die wordt gebruikt voor het artefact. 

1. Selecteer **Continue implementatietrigger** rechts van het pictogram **Neerzetten**.  
    Deze release-pijplijn heeft een ingeschakelde CD-trigger die een implementatie uitvoert telkens wanneer een nieuw build-artefact beschikbaar is. U kunt de trigger eventueel uitschakelen zodat de implementaties handmatig moeten worden uitgevoerd. 

1. Selecteer aan de rechterkant **Versies weergeven** om een versiegeschiedenis weer te geven.

1. Selecteer het beletselteken (...) naast een versie en selecteer **Openen**.  
    U kunt verschillende menu's verkennen, zoals een versieoverzicht, gekoppelde werkitems en tests.

1. Selecteer **Doorvoeringen**.  
    In deze weergave worden de codedoorvoeringen weergegeven die zijn gekoppeld aan deze implementatie. Vergelijk versies om de doorvoerverschillen tussen implementaties weer te geven.

1. Selecteer **Logboeken**.  
    De logboeken bevatten nuttige informatie over het implementatieproces. U kunt beide weergeven tijdens en na de implementaties.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Wijzigingen doorvoeren in Azure Repos en automatisch implementeren naar Azure 

 > [!NOTE]
 > Met de volgende procedure wordt de CI/CD-pijplijn getest door een eenvoudige tekstwijziging aan te brengen.

U bent nu klaar om met een team samen te werken aan de app met behulp van een CI/CD-proces waarmee automatisch uw meest recente werk op uw website wordt geïmplementeerd. Bij elke wijziging in de Git-opslagplaats wordt een build gestart in Azure DevOps, en met een CD-pijplijn wordt een implementatie uitgevoerd in Azure. Volg de procedure in deze sectie of gebruik een andere methode om wijzigingen in de opslagplaats door te voeren. U kunt bijvoorbeeld de Git-opslagplaats in uw favoriete hulpprogramma of IDE klonen en wijzigingen vervolgens naar deze opslagplaats pushen.

1. Selecteer > **codebestanden**in het menu **Code**Azure DevOps en ga vervolgens naar uw repo.

1. Ga naar de map *Views\Home*, selecteer het beletselteken naast het bestand *Index.cshtml* en selecteer vervolgens **Bewerken**.

1. Breng een wijziging aan in het bestand, bijvoorbeeld door wat tekst toe te voegen in een van de div-tags. 

1. Selecteer in de rechterbovenhoek **Doorvoeren** en selecteer vervolgens nogmaals **Doorvoeren** om de wijziging te pushen.  
    Na een paar seconden wordt er in Azure DevOps een build gestart en wordt er een versie uitgevoerd om de wijzigingen te implementeren. Bewaak de buildstatus via het DevOps Projects-dashboard of in de browser met uw Azure DevOps-organisatie.

1. Nadat de versie is voltooid, vernieuwt u uw app om uw wijzigingen te controleren.

## <a name="clean-up-resources"></a>Resources opschonen

Tijdens het testen kunt u voorkomen dat de kosten oplopen door resources op te schonen. U kunt het AKS-cluster en de gerelateerde resources die u in deze zelfstudie hebt gemaakt, verwijderen wanneer u ze niet meer nodig hebt. Hiertoe gebruikt u de functionaliteit **Verwijderen** op het dashboard van DevOps Projects.

> [!IMPORTANT]
> Met de volgende procedure worden resources permanent verwijderd. Met de functionaliteit *Verwijderen* verwijdert u de gegevens die door het project in DevOps Projects zijn gemaakt in zowel Azure als Azure DevOps. Als ze zijn verwijderd, kunt u ze niet meer terughalen. Gebruik deze procedure pas nadat u de prompts zorgvuldig hebt gelezen.

1. Ga in de Azure-portal naar het dashboard van DevOps Projects.
2. Selecteer in de rechterbovenhoek **Verwijderen**. 
3. Selecteer **Ja** bij de prompt om de resources *definitief te verwijderen*.

## <a name="next-steps"></a>Volgende stappen

U kunt de build- en release-pijplijn desgewenst wijzigen in overeenstemming met de behoeften van uw team. U kunt dit CI/CD-patroon ook als een sjabloon voor uw andere pijplijnen gebruiken. In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Met behulp van DevOps Projects een ASP.NET Core-app implementeren naar AKS
> * Azure DevOps en een Azure-abonnement configureren 
> * Het AKS-cluster bestuderen
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * Wijzigingen doorvoeren in Git en automatisch implementeren naar Azure
> * Resources opschonen

Zie voor meer informatie over het gebruik van het Kubernetes-dashboard:

> [!div class="nextstepaction"]
> [Het Kubernetes-dashboard gebruiken](https://docs.microsoft.com/azure/aks/kubernetes-dashboard)
