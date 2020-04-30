---
title: ASP.NET Core-Apps implementeren in azure Kubernetes service met Azure DevOps starter
description: Met Azure DevOps starter kunt u gemakkelijk aan de slag met Azure. Met DevOps starter kunt u in een paar snelle stappen uw ASP.NET Core-app implementeren met de Azure Kubernetes-service (AKS).
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 7e298afa7efa6035b24f689d1163308ac6b14bff
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233326"
---
# <a name="deploy-aspnet-core-apps-to-azure-kubernetes-service-with-azure-devops-starter"></a>ASP.NET Core-Apps implementeren in azure Kubernetes service met Azure DevOps starter

Azure DevOps starter biedt een vereenvoudigde ervaring waar u uw bestaande code en git opslag plaats kunt meenemen, of een voorbeeld toepassing moet kiezen voor het maken van een doorlopende integratie (CI) en continue levering (CD)-pijp lijn naar Azure. 

DevOps Starter:

* Er worden automatisch Azure-resources gemaakt, zoals Azure Kubernetes Service.
* In Azure DevOps wordt een release-pijplijn gemaakt en geconfigureerd om een build en release-pijplijn in te stellen voor CI/CD.
* Er wordt een Azure Application Insights-resource gemaakt voor de bewaking.
* [Azure Monitor voor containers](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) wordt ingeschakeld voor het bewaken van de prestaties voor de werkbelastingen van de container op het AKS-cluster

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * DevOps starter gebruiken om een ASP.NET Core-app te implementeren op AKS
> * Azure DevOps en een Azure-abonnement configureren 
> * Het AKS-cluster bestuderen
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * Wijzigingen doorvoeren in Git en automatisch implementeren naar Azure
> * Resources opschonen

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. U kunt er een gratis krijgen via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-starter-to-deploy-an-aspnet-core-app-to-aks"></a>DevOps starter gebruiken om een ASP.NET Core-app te implementeren op AKS

DevOps Starter maakt een CI/CD-pijp lijn in azure-pijp lijnen. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. DevOps Starter maakt ook Azure-resources, zoals een AKS-cluster, in het Azure-abonnement van uw keuze.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Typ **DevOps starter**in het zoekvak en selecteer. Klik op **toevoegen** om een nieuw item te maken.

    ![Het DevOps-starter dash board](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Selecteer **.NET** en vervolgens **Volgende**.

1. Onder **Kies een toepassings raamwerk**selecteert u **ASP.net core** en selecteert u **volgende**.

1. Selecteer **Kubernetes Service** en selecteer **Volgende**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps en een Azure-abonnement configureren

1. Maak een nieuwe Azure DevOps-organisatie of selecteer een bestaande organisatie. 

1. Voer een naam in voor het Azure DevOps-project. 

1. Selecteer uw Azure-abonnement.

1. Selecteer **Wijzigen** om aanvullende Azure-configuratie-instellingen te zien en het aantal knooppunten voor het AKS-cluster te identificeren. In dit deelvenster vindt u verschillende opties voor het configureren van het type en de locatie van de Azure-services.
 
1. Verlaat het Azure-configuratiegebied en selecteer **Gereed**. Na enkele minuten is het proces voltooid. Een voorbeeld-ASP.NET Core-app wordt in een Git-opslagplaats in uw Azure DevOps-organisatie ingesteld, een AKS-cluster wordt gemaakt, een CI/CD-pijplijn wordt uitgevoerd en uw app wordt geïmplementeerd naar Azure. 

    Nadat dit is voltooid, wordt het Azure DevOps starter-dash board weer gegeven in de Azure Portal. U kunt ook rechtstreeks naar het DevOps starter-dash board gaan vanuit **alle resources** in de Azure Portal. 

    Dit dashboard biedt meer inzicht in uw Azure DevOps-codeopslagplaats, uw CI/CD-pijplijn en uw AKS-cluster. U kunt meer CI/CD-opties configureren in uw Azure DevOps-pijplijn. Selecteer aan de rechterkant **Bladeren** om uw actieve app weer te geven.

## <a name="examine-the-aks-cluster"></a>Het AKS-cluster bestuderen

DevOps starter configureert automatisch een AKS-cluster, dat u kunt verkennen en aanpassen. Ga als volgt te werk om vertrouwd te raken met het AKS-cluster:

1. Ga naar het DevOps-starter-dash board.

1. Selecteer de AKS-service aan de rechterkant. Er wordt een deelvenster voor het AKS-cluster geopend. In deze weergave kunt u verschillende acties uitvoeren, zoals de containerstatus bewaken, zoeken in logboeken en het Kubernetes-dashboard openen.

1. Selecteer aan de rechterkant **Kubernetes-dashboard weergeven**. Volg desgewenst de stappen om het Kubernetes-dashboard te openen.

## <a name="examine-the-ci-pipeline"></a>De CI-pijplijn onderzoeken

DevOps starter configureert automatisch een CI/CD-pijp lijn in uw Azure DevOps-organisatie. U kunt de pijplijn verkennen en aanpassen. Ga als volgt te werk om vertrouwd te raken met de pijplijn:

1. Ga naar het DevOps-starter-dash board.

1. Selecteer boven aan het DevOps-starter-dash board **Build pijp lijnen**.  Op een tabblad in de browser wordt de build-pijplijn voor het nieuwe project weergegeven.

1. Wijs het veld **status** aan en selecteer vervolgens het weglatings teken (...).  In een menu worden verschillende opties weer gegeven, zoals het in de wachtrij plaatsen van een nieuwe build, het onderbreken van een build en het bewerken van de build-pijp lijn.

1. Selecteer **bewerken**.

1. In dit deelvenster kunt u de verschillende taken voor uw build-pijplijn onderzoeken. In de build worden verschillende taken uitgevoerd, zoals het ophalen van bronnen uit de Git-opslagplaats, het herstellen van afhankelijkheden, en het publiceren van uitvoergegevens die worden gebruikt voor implementaties.

1. Selecteer bovenaan de build-pijplijn de naam van de build-pijplijn.

1. Wijzig de naam van de build-pijplijn in een meer beschrijvende naam. Selecteer **Opslaan en wachtrij** en selecteer vervolgens **Opslaan**.

1. Selecteer onder de naam van de build-pipeline de optie **Geschiedenis**. In dit deelvenster ziet u een audittrail van recente wijzigingen voor de build. In Azure DevOps worden alle wijzigingen in de build-pijplijn bijgehouden en krijgt u de mogelijkheid om versies te vergelijken.

1. Selecteer **Triggers**. DevOps Starter maakt automatisch een CI-trigger en elke door voering aan de opslag plaats start een nieuwe build. Desgewenst kunt u kiezen of u vertakkingen van het CI-proces wilt opnemen of uitsluiten.

1. Selecteer **Retentie**. Afhankelijk van het scenario kunt u beleidsregels opgeven om een bepaald aantal builds te behouden of te verwijderen.

## <a name="examine-the-cd-release-pipeline"></a>De CD-release-pijplijn onderzoeken

DevOps Starter maakt en configureert automatisch de benodigde stappen voor het implementeren van uw Azure DevOps-organisatie naar uw Azure-abonnement. Deze stappen omvatten het configureren van een Azure-serviceverbinding om Azure DevOps te verifiëren bij uw Azure-abonnement. Er wordt ook automatisch ook een release-pijplijn gemaakt, die de CD levert aan Azure. Voor meer informatie over de release-pijplijn doet u het volgende:

1. Selecteer **Build en release** en selecteer vervolgens **Releases**.  DevOps Starter maakt een release pijplijn voor het beheren van implementaties naar Azure.

1. Selecteer het beletselteken (...) naast de release-pijplijn en selecteer **Bewerken**. De release-pijplijn bevat een *pijplijn* die het releaseproces definieert.

1. Onder **Artefacten** selecteert u **Neerzetten**. Met de build-pijplijn die u in de vorige stappen hebt onderzocht, wordt de uitvoer geproduceerd die wordt gebruikt voor het artefact. 

1. Selecteer **Continue implementatietrigger** rechts van het pictogram **Neerzetten**. Deze release-pijplijn heeft een ingeschakelde CD-trigger die een implementatie uitvoert telkens wanneer een nieuw build-artefact beschikbaar is. U kunt de trigger eventueel uitschakelen zodat de implementaties handmatig moeten worden uitgevoerd. 

1. Selecteer aan de rechterkant **Versies weergeven** om een versiegeschiedenis weer te geven.

1. Selecteer het beletselteken (...) naast een versie en selecteer **Openen**. U kunt verschillende menu's verkennen, zoals een versieoverzicht, gekoppelde werkitems en tests.

1. Selecteer **Doorvoeringen**. In deze weergave worden de codedoorvoeringen weergegeven die zijn gekoppeld aan deze implementatie. Vergelijk versies om de doorvoerverschillen tussen implementaties weer te geven.

1. Selecteer **Logboeken**. De logboeken bevatten nuttige informatie over het implementatieproces. U kunt beide weergeven tijdens en na de implementaties.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Wijzigingen doorvoeren in Azure Repos en automatisch implementeren naar Azure 

 > [!NOTE]
 > Met de volgende procedure wordt de CI/CD-pijplijn getest door een eenvoudige tekstwijziging aan te brengen.

U bent nu klaar om met een team samen te werken aan de app met behulp van een CI/CD-proces waarmee automatisch uw meest recente werk op uw website wordt geïmplementeerd. Bij elke wijziging in de Git-opslagplaats wordt een build gestart in Azure DevOps, en met een CD-pijplijn wordt een implementatie uitgevoerd in Azure. Volg de procedure in deze sectie of gebruik een andere methode om wijzigingen in de opslagplaats door te voeren. U kunt bijvoorbeeld de Git-opslagplaats in uw favoriete hulpprogramma of IDE klonen en wijzigingen vervolgens naar deze opslagplaats pushen.

1. Selecteer in het menu van Azure DevOps **code** > **bestanden**en ga vervolgens naar uw opslag plaats.

1. Ga naar de map *Views\Home*, selecteer het beletselteken naast het bestand *Index.cshtml* en selecteer vervolgens **Bewerken**.

1. Breng een wijziging aan in het bestand, bijvoorbeeld door wat tekst toe te voegen in een van de div-tags. 

1. Selecteer in de rechterbovenhoek **Doorvoeren** en selecteer vervolgens nogmaals **Doorvoeren** om de wijziging te pushen. Na een paar seconden wordt er in Azure DevOps een build gestart en wordt er een versie uitgevoerd om de wijzigingen te implementeren. Bewaak de status van de build in het DevOps-start dashboard of in de browser met uw Azure DevOps-organisatie.

1. Nadat de versie is voltooid, vernieuwt u uw app om uw wijzigingen te controleren.

## <a name="clean-up-resources"></a>Resources opschonen

Tijdens het testen kunt u voorkomen dat de kosten oplopen door resources op te schonen. U kunt het AKS-cluster en de gerelateerde resources die u in deze zelfstudie hebt gemaakt, verwijderen wanneer u ze niet meer nodig hebt. Als u dit wilt doen, gebruikt u de **verwijderings** functionaliteit op het DevOps starter-dash board.

> [!IMPORTANT]
> Met de volgende procedure worden resources permanent verwijderd. De functie *verwijderen* vernietigt de gegevens die door het project in DevOps starter zijn gemaakt in zowel Azure als Azure DevOps, en u kunt deze niet ophalen. Gebruik deze procedure pas nadat u de prompts zorgvuldig hebt gelezen.

1. Ga in het Azure Portal naar het DevOps starter-dash board.
1. Selecteer in de rechterbovenhoek **Verwijderen**. 
1. Selecteer **Ja** bij de prompt om de resources *definitief te verwijderen*.

## <a name="next-steps"></a>Volgende stappen

U kunt de build- en release-pijplijn desgewenst wijzigen in overeenstemming met de behoeften van uw team. U kunt dit CI/CD-patroon ook als een sjabloon voor uw andere pijplijnen gebruiken. In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * DevOps starter gebruiken om een ASP.NET Core-app te implementeren op AKS
> * Azure DevOps en een Azure-abonnement configureren 
> * Het AKS-cluster bestuderen
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * Wijzigingen doorvoeren in Git en automatisch implementeren naar Azure
> * Resources opschonen

Zie voor meer informatie over het gebruik van het Kubernetes-dashboard:

> [!div class="nextstepaction"]
> [Het Kubernetes-dashboard gebruiken](https://docs.microsoft.com/azure/aks/kubernetes-dashboard)
