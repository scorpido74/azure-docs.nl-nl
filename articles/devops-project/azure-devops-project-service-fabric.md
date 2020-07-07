---
title: 'Zelf studie: uw ASP.NET Core-app implementeren in azure Service Fabric met behulp van Azure DevOps starter'
description: Met Azure DevOps starter kunt u gemakkelijk aan de slag met Azure. Met Azure DevOps Projects kunt u in slechts enkele stappen een ASP.NET Core-app implementeren naar Azure Service Fabric.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: ce35f3af38b98f43ebbd0d3f42136ab8358b19a5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82233717"
---
# <a name="tutorial-deploy-your-aspnet-core-app-to-azure-service-fabric-by-using-azure-devops-starter"></a>Zelf studie: uw ASP.NET Core-app implementeren in azure Service Fabric met behulp van Azure DevOps starter

Azure DevOps starter biedt een vereenvoudigde ervaring waar u uw bestaande code en git opslag plaats kunt meenemen, of een voorbeeld toepassing moet kiezen voor het maken van een doorlopende integratie (CI) en continue levering (CD)-pijp lijn naar Azure. 

DevOps Starter:

* Er worden automatisch Azure-resources gemaakt, zoals Azure Service Fabric.
* In Azure DevOps wordt een release-pijplijn gemaakt en geconfigureerd zodat een CI/CD-pijplijn wordt ingesteld.
* Er wordt een Azure Application Insights-resource gemaakt voor de bewaking.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gebruik DevOps starter om een ASP.NET Core-app te maken en deze te implementeren op Service Fabric
> * Azure DevOps en een Azure-abonnement configureren 
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * Wijzigingen doorvoeren in Git en automatisch implementeren in Azure
> * Resources opschonen

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. U kunt er een gratis krijgen via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-starter-to-create-an-aspnet-core-app-and-deploy-it-to-service-fabric"></a>Gebruik DevOps starter om een ASP.NET Core-app te maken en deze te implementeren op Service Fabric

DevOps Starter maakt een CI/CD-pijp lijn in azure-pijp lijnen. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. DevOps Starter maakt ook Azure-resources, zoals een Service Fabric cluster, in het Azure-abonnement van uw keuze.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Typ **DevOps starter**in het zoekvak en selecteer. Klik op **toevoegen** om een nieuw item te maken.

    ![Het DevOps-starter dash board](_img/azure-devops-starter-aks/search-devops-starter.png) 

1. Selecteer **.NET** en vervolgens **Volgende**.

1. Onder **Een toepassingsframework kiezen** selecteert u **ASP.NET Core**. Selecteer daarna **Volgende**.

1. Selecteer **Service Fabric-cluster** en vervolgens **Volgende**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps en een Azure-abonnement configureren

1. Maak een nieuwe Azure DevOps-organisatie of selecteer een bestaande organisatie. 

1. Voer een naam in voor het Azure DevOps-project. 

1. Selecteer uw Azure-abonnement.

1. Selecteer de koppeling **Wijziging** om aanvullende Azure-configuratie-instellingen weer te geven en de knooppuntgrootte van de virtuele machine en het besturingssysteem voor het Service Fabric-cluster te bepalen. In dit deelvenster vindt u verschillende opties voor het configureren van het type en de locatie van de Azure-services.
 
1. Verlaat het Azure-configuratiegebied en selecteer **Gereed**.  
    Na enkele minuten is het proces voltooid. Een voorbeeld-ASP.NET Core-app wordt in een Git-opslagplaats in uw Azure DevOps-organisatie ingesteld, een Service Fabric-cluster wordt gemaakt, een CI/CD-pijplijn wordt uitgevoerd en uw app wordt geïmplementeerd naar Azure. 

    Nadat dit is voltooid, wordt het DevOps-start dashboard weer gegeven in de Azure Portal. U kunt ook rechtstreeks naar het DevOps starter-dash board gaan vanuit **alle resources** in de Azure Portal. 

    Dit dashboard biedt meer inzicht in uw Azure DevOps-codeopslagplaats, uw CI/CD-pijplijn en uw Service Fabric-cluster. U kunt aanvullende opties voor uw CI/CD-pijplijn configureren in Azure-opslagplaatsen. Selecteer aan de rechterkant **Bladeren** om uw actieve app weer te geven.

## <a name="examine-the-ci-pipeline"></a>De CI-pijplijn onderzoeken

DevOps starter configureert automatisch een CI/CD-pijp lijn in azure-pijp lijnen. U kunt de pijplijn verkennen en aanpassen. Ga als volgt te werk om vertrouwd te raken met de pijplijn:

1. Ga naar het DevOps-starter-dash board.

1. Selecteer boven aan het DevOps-starter-dash board **Build pijp lijnen**. Op een tabblad in de browser wordt de build-pijplijn voor het nieuwe project weergegeven.

1. Wijs het veld **status** aan en selecteer vervolgens het weglatings teken (...). In een menu worden verschillende opties weer gegeven, zoals het in de wachtrij plaatsen van een nieuwe build, het onderbreken van een build en het bewerken van de build-pijp lijn.

1. Selecteer **Bewerken**.

1. In dit deelvenster kunt u de verschillende taken voor uw build-pijplijn onderzoeken. In de build worden verschillende taken uitgevoerd, zoals het ophalen van bronnen uit de Git-opslagplaats, het herstellen van afhankelijkheden, en het publiceren van uitvoergegevens die worden gebruikt voor implementaties.

1. Selecteer bovenaan de build-pijplijn de naam van de build-pijplijn. 

1. Selecteer onder de naam van de build-pipeline de optie **Geschiedenis**. In dit deelvenster ziet u een audittrail van recente wijzigingen voor de build. In Azure DevOps worden alle wijzigingen in de build-pijplijn bijgehouden en krijgt u de mogelijkheid om versies te vergelijken.

1. Selecteer **Triggers**. DevOps Starter maakt automatisch een CI-trigger en elke door voering aan de opslag plaats start een nieuwe build. Desgewenst kunt u kiezen of u vertakkingen van het CI-proces wilt opnemen of uitsluiten.

1. Selecteer **Retentie**. Afhankelijk van het scenario kunt u beleidsregels opgeven om een bepaald aantal builds te behouden of te verwijderen.

## <a name="examine-the-cd-pipeline"></a>De CD-pijplijn onderzoeken

DevOps Starter maakt en configureert automatisch de benodigde stappen voor het implementeren van uw Azure DevOps-organisatie naar uw Azure-abonnement. Deze stappen omvatten het configureren van een Azure-serviceverbinding om Azure DevOps te verifiëren bij uw Azure-abonnement. Er wordt ook automatisch ook een release-pijplijn gemaakt, die de CD levert aan Azure. Voor meer informatie over de release-pijplijn doet u het volgende:

1. Selecteer **Build en release** en selecteer vervolgens **Releases**. DevOps Starter maakt een release pijplijn voor het beheren van implementaties naar Azure.

1. Selecteer het beletselteken (...) naast de release-pijplijn en selecteer **Bewerken**. De release-pijplijn bevat een *pijplijn* die het releaseproces definieert.

1. Onder **Artefacten** selecteert u **Neerzetten**. De build-pijplijn die u eerder hebt onderzocht, produceert de uitvoer die wordt gebruikt voor het artefact. 

1. Selecteer **Continue implementatietrigger** rechts van het pictogram **Neerzetten**. Deze release-pijplijn heeft een ingeschakelde CD-trigger die een implementatie uitvoert telkens wanneer een nieuw build-artefact beschikbaar is. U kunt de trigger eventueel uitschakelen zodat de implementaties handmatig moeten worden uitgevoerd. 

1. Selecteer aan de rechterkant **Versies weergeven** om een versiegeschiedenis weer te geven.

1. Selecteer het beletselteken (...) naast een versie en selecteer **Openen**. U kunt verschillende menu's verkennen, zoals een versieoverzicht, gekoppelde werkitems en tests.

1. Selecteer **Doorvoeringen**. In deze weergave worden de codedoorvoeringen weergegeven die zijn gekoppeld aan deze implementatie. Vergelijk versies om de doorvoerverschillen tussen implementaties weer te geven.

1. Selecteer **Logboeken**. De logboeken bevatten nuttige informatie over het implementatieproces. U kunt beide weergeven tijdens en na de implementaties.

## <a name="commit-changes-to-git-and-automatically-deploy-them-to-azure"></a>Wijzigingen doorvoeren in Git en automatisch implementeren naar Azure 

 > [!NOTE]
 > Met de volgende procedure wordt de CI/CD-pijplijn getest door een eenvoudige tekstwijziging aan te brengen.

U bent nu klaar om met een team samen te werken aan de app met behulp van een CI/CD-proces waarmee automatisch uw meest recente werk op uw website wordt geïmplementeerd. Bij elke wijziging van de Git-opslagplaats wordt er een build gestart, en worden uw wijzigingen naar Azure geïmplementeerd via een versie. Volg de procedure in deze sectie of gebruik een andere methode om wijzigingen in de opslagplaats door te voeren. U kunt bijvoorbeeld de Git-opslagplaats in uw favoriete hulpprogramma of IDE klonen en wijzigingen vervolgens naar deze opslagplaats pushen.

1. Selecteer in het menu van Azure DevOps **code**  >  **bestanden**en ga vervolgens naar uw opslag plaats.

1. Ga naar de map *Views\Home*, selecteer het beletselteken naast het bestand *Index.cshtml* en selecteer vervolgens **Bewerken**.

1. Breng een wijziging aan in het bestand, bijvoorbeeld door wat tekst toe te voegen in een van de div-tags. 

1. Selecteer in de rechterbovenhoek **Doorvoeren** en selecteer vervolgens nogmaals **Doorvoeren** om de wijziging te pushen.  
    Na enkele seconden wordt er een build gestart, waarna er een versie wordt uitgevoerd om de wijzigingen te implementeren. U kunt de samenstel status bewaken in het DevOps-starter dash board of in de browser met Azure DevOps real-time logboek registratie.

1. Nadat de versie is voltooid, vernieuwt u uw app om uw wijzigingen te controleren.

## <a name="clean-up-resources"></a>Resources opschonen

Tijdens het testen kunt u voorkomen dat de kosten oplopen door resources op te schonen. U kunt het Azure Service Fabric-cluster en de gerelateerde resources die u in deze zelfstudie hebt gemaakt, verwijderen wanneer u ze niet meer nodig hebt. Als u dit wilt doen, gebruikt u de **verwijderings** functionaliteit op het DevOps starter-dash board.

> [!IMPORTANT]
> Met de volgende procedure worden resources permanent verwijderd. De functie *verwijderen* vernietigt de gegevens die door het project in DevOps starter zijn gemaakt in zowel Azure als Azure DevOps, en u kunt deze niet ophalen. Gebruik deze procedure pas nadat u de prompts zorgvuldig hebt gelezen.

1. Ga in het Azure Portal naar het DevOps starter-dash board.
1. Selecteer in de rechterbovenhoek **Verwijderen**. 
1. Selecteer **Ja** bij de prompt om de resources *definitief te verwijderen*.

## <a name="next-steps"></a>Volgende stappen

U kunt de Azure CI/CD-pijplijn desgewenst wijzigen in overeenstemming met de behoeften van uw team. U kunt dit CI/CD-patroon ook als een sjabloon voor uw andere pijplijnen gebruiken. In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Gebruik DevOps starter om een ASP.NET Core-app te maken en deze te implementeren op Service Fabric
> * Azure DevOps en een Azure-abonnement configureren 
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * Wijzigingen doorvoeren in Git en automatisch implementeren naar Azure
> * Resources opschonen

Zie voor meer informatie over Service Fabric en microservices:

> [!div class="nextstepaction"]
> [Een microservices-benadering gebruiken voor het compileren van toepassingen](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
