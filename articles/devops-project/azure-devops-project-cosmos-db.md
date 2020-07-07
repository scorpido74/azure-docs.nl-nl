---
title: 'Zelf studie: Node.js-Apps implementeren die worden aangedreven door Azure Cosmos DB met Azure DevOps starter'
description: Met Azure DevOps starter kunt u gemakkelijk aan de slag met Azure. Met DevOps starter kunt u in een paar snelle stappen uw Node.js-app implementeren die wordt aangedreven door Azure Cosmos DB naar Windows Web app.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 07579cf22738e195e3e4ae7a2aa18ffeb885bbe2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82233237"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-starter"></a>Node.js-Apps implementeren die worden aangedreven door Azure Cosmos DB met DevOps starter

Azure DevOps starter biedt een gestroomlijnde ervaring waar u een doorlopende integratie (CI) en een doorlopende implementatie (CD)-pijp lijn kunt maken naar Azure. U kunt dit doen met behulp van uw bestaande code en git-opslag plaats (opslag plaats) of door een voorbeeld toepassing te selecteren.

DevOps Starter:

* Maakt automatisch Azure-resources, zoals Azure Cosmos DB, Azure-toepassing inzichten, Azure App Service en App Service plannen

* Hiermee wordt een CI/CD-release pijplijn gemaakt en geconfigureerd in azure DevOps

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * DevOps starter gebruiken om een Node.js-app te implementeren die wordt aangedreven door Azure Cosmos DB
> * Azure DevOps en een Azure-abonnement configureren
> * Azure Cosmos DB onderzoeken
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * De wijzigingen door voeren in Git en deze automatisch implementeren in azure
> * De resources opschonen

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig. dit kunt u gratis doen met [Visual Studio dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) .

## <a name="use-devops-starter-to-deploy-nodejs-app"></a>DevOps starter gebruiken om Node.js-app te implementeren

DevOps Starter maakt een CI/CD-pijp lijn in azure-pijp lijnen. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. DevOps Starter maakt ook Azure-resources, zoals Azure Cosmos DB, Application Insights, App Service en App Service plannen, in het Azure-abonnement van uw keuze.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Typ **DevOps starter**in het zoekvak en selecteer. Klik op **toevoegen** om een nieuw item te maken.

    ![Het DevOps-starter dash board](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Selecteer **Node.js** als runtime en selecteer **volgende**. Onder **Kies een toepassings raamwerk**selecteert u **Express.js**.

1. Schakel de sectie **een Data Base** voor **Cosmos DB**toevoegen in en selecteer **volgende**.

    ![Een Data Base toevoegen](_img/azure-devops-project-cosmos-db/add-database.png)

    Azure DevOps Starter ondersteunt verschillende toepassings raamwerken, zoals **Express.js**, **voorbeeld Node.js app**en **Sail.js**. In deze zelf studie gebruiken we **Express.js**.

1. Selecteer een Azure-service om de toepassing te implementeren en selecteer vervolgens **volgende**. Uw opties zijn onder andere Windows Web app, Azure Kubernetes service en Azure Web App for Containers. In deze zelf studie gebruiken we **Windows Web app**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Azure DevOps en Azure-abonnement configureren

1. Voer een naam in voor het Azure DevOps-project.

1. Maak een nieuwe Azure DevOps-organisatie of selecteer een bestaande organisatie.

1. Selecteer uw Azure-abonnement.

1. Als u aanvullende Azure-configuratie-instellingen wilt weer geven of de prijs categorie en locatie wilt identificeren, selecteert u **aanvullende instellingen**. Dit deel venster toont diverse opties voor het configureren van de prijs categorie en de locatie van Azure-Services.

1. Verlaat het Azure-configuratiegebied en selecteer **Gereed**.

1. Het proces is na een paar minuten voltooid. Een voor beeld Node.js-app is ingesteld in een Git-opslag plaats in uw Azure DevOps-organisatie. Vervolgens worden Azure Cosmos DB, App Service, App Service plan en Application Insights resources gemaakt, evenals een CI/CD-pijp lijn. Uw app wordt vervolgens geïmplementeerd naar Azure.

   Wanneer al deze processen zijn voltooid, wordt het Azure DevOps starter-dash board weer gegeven in de Azure Portal. U kunt ook rechtstreeks naar het DevOps starter-dash board gaan vanuit **alle resources** in de Azure Portal.

   Dit dash board biedt inzicht in uw Azure DevOps code-opslag plaats, uw CI/CD-pijp lijn en uw Azure Cosmos DB-Data Base. U kunt meer CI/CD-opties configureren in uw Azure DevOps-pijplijn. Selecteer aan de rechter kant van het dash board **Azure Cosmos DB** om deze opties weer te geven.

## <a name="examine-azure-cosmos-db"></a>Azure Cosmos DB onderzoeken

DevOps starter configureert automatisch Azure Cosmos DB, dat u kunt verkennen en aanpassen. Ga als volgt te werk om vertrouwd te raken met Azure Cosmos DB:

1. Ga naar het DevOps-starter-dash board.

    ![DevOps Projects dash board](_img/azure-devops-project-cosmos-db/devops-starter-dashboard.png)

1. Selecteer aan de rechter kant Azure Cosmos DB. Er wordt een deel venster geopend voor Azure Cosmos DB. In deze weer gave kunt u verschillende acties uitvoeren, zoals het bewaken van bewerkingen en het zoeken naar Logboeken.

    ![Azure Cosmos DB deel venster](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>De CI-pijplijn onderzoeken

DevOps starter configureert automatisch een CI/CD-pijp lijn in uw Azure DevOps-organisatie. U kunt de pijplijn verkennen en aanpassen. Ga als volgt te werk om vertrouwd te raken met de pijplijn:

1. Ga naar het DevOps-starter-dash board.

1. Selecteer de Hyper link onder **Build**. Op een tabblad in de browser wordt de build-pijplijn voor het nieuwe project weergegeven.

    ![Deel venster maken](_img/azure-devops-project-cosmos-db/build.png)

1. Selecteer **Bewerken**. In dit deelvenster kunt u de verschillende taken voor uw build-pijplijn onderzoeken. De build voert diverse taken uit, zoals het ophalen van de bron code uit de Git-opslag plaats, het bouwen van de toepassing, het uitvoeren van eenheids tests en het publiceren van uitvoer die worden gebruikt voor implementaties.

1. Selecteer **Triggers**. DevOps Starter maakt automatisch een CI-trigger en elke door voering aan de opslag plaats start een nieuwe build. U kunt ervoor kiezen om vertakkingen op te nemen of uit te sluiten van het CI-proces.

1. Selecteer **Retentie**. Afhankelijk van het scenario kunt u beleidsregels opgeven om een bepaald aantal builds te behouden of te verwijderen.

1. Selecteer bovenaan de build-pijplijn de naam van de build-pijplijn.

1. Wijzig de naam van uw build-pijp lijn in iets beschrijvender en selecteer vervolgens **Opslaan** in de vervolg keuzelijst **Opslaan & wachtrij** .

1. Selecteer onder de naam van de build-pipeline de optie **Geschiedenis**. In dit deelvenster ziet u een audittrail van recente wijzigingen voor de build. In Azure DevOps worden alle wijzigingen in de build-pijplijn bijgehouden en krijgt u de mogelijkheid om versies te vergelijken.

## <a name="examine-the-cd-release-pipeline"></a>De CD-release-pijplijn onderzoeken

DevOps Starter maakt en configureert automatisch de benodigde stappen voor het implementeren van uw Azure DevOps-organisatie naar uw Azure-abonnement. Deze stappen omvatten het configureren van een Azure-serviceverbinding om Azure DevOps te verifiëren bij uw Azure-abonnement. Er wordt ook automatisch ook een release-pijplijn gemaakt, die de CD levert aan Azure. Voor meer informatie over de release-pijplijn doet u het volgende:

1. Ga naar **pijp lijnen** en selecteer **releases**.

1. Selecteer **Bewerken**.

1. Onder **Artefacten** selecteert u **Neerzetten**. Met de build-pijplijn die u in de vorige stappen hebt onderzocht, wordt de uitvoer geproduceerd die wordt gebruikt voor het artefact.

1. Selecteer de **trigger voor continue implementatie**rechts van het pictogram voor **neerzetten** . Deze release pijplijn heeft voortdurende implementatie geactiveerd, waardoor een implementatie wordt uitgevoerd wanneer een nieuw build-artefact beschikbaar is. U kunt de trigger uitschakelen zodat uw implementaties hand matig worden uitgevoerd.

1. Selecteer aan de rechter kant de sectie **weergave releases** om een geschiedenis van releases weer te geven.

1. Selecteer de release, waarmee de pijp lijn wordt weer gegeven. Selecteer een wille keurige omgeving om de release samenvatting, door voeringen of gekoppelde werk items te controleren.

1. Selecteer **Doorvoeringen**. In deze weergave worden de codedoorvoeringen weergegeven die zijn gekoppeld aan deze implementatie. Vergelijk versies om de doorvoerverschillen tussen implementaties weer te geven.

1. Selecteer **Logboeken weer geven**. De logboeken bevatten nuttige informatie over het implementatieproces. U kunt beide weergeven tijdens en na de implementaties.

## <a name="commit-code-changes-and-execute-the-cicd-pipeline"></a>Code wijzigingen door voeren en de CI/CD-pijp lijn uitvoeren

> [!NOTE]
> Met de volgende procedure wordt de CI/CD-pijplijn getest door een eenvoudige tekstwijziging aan te brengen.

U bent nu klaar om samen te werken met een team in uw app door een CI/CD-proces te gebruiken waarmee u uw laatste werk op uw App Service implementeert. Bij elke wijziging in de Git-opslagplaats wordt een build gestart in Azure DevOps, en met een CD-pijplijn wordt een implementatie uitgevoerd in Azure. Volg de procedure in deze sectie of gebruik een andere methode om wijzigingen in de opslagplaats door te voeren. U kunt bijvoorbeeld de Git-opslagplaats in uw favoriete hulpprogramma of IDE klonen en wijzigingen vervolgens naar deze opslagplaats pushen.

1. Selecteer in het menu van Azure DevOps **opslag plaatsen** en vervolgens **bestanden**. Ga vervolgens naar uw opslag plaats.

1. De opslag plaats bevat al code op basis van de toepassings taal die u hebt gekozen tijdens het maken van het proces. Open het bestand **Application/views/index. Pug** .

1. Selecteer **bewerken**en breng vervolgens een wijziging aan in **regel nummer 15**. U kunt deze bijvoorbeeld wijzigen in ' mijn eerste implementatie naar Azure App Service die wordt ingeschakeld door Azure Cosmos DB '.

1. Selecteer **door voeren**in de rechter bovenhoek en selecteer vervolgens **door voeren** opnieuw om uw wijziging te pushen.

     Na een paar seconden wordt een build gestart in azure DevOps en wordt een release uitgevoerd om de wijzigingen te implementeren. Bewaak de status van de build in het DevOps-start dashboard of in de browser met uw Azure DevOps-organisatie.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de gerelateerde resources die u hebt gemaakt wanneer u deze niet meer nodig hebt. Gebruik de **verwijderings** functionaliteit op het DevOps-starter-dash board.

## <a name="next-steps"></a>Volgende stappen

U kunt deze build- en release-pipelines desgewenst wijzigen in overeenstemming met de behoeften van uw team. U kunt dit CI/CD-patroon ook als een sjabloon voor uw andere pijplijnen gebruiken. In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * DevOps starter gebruiken om een Node.js-app te implementeren die wordt aangedreven door Azure Cosmos DB
> * Azure DevOps en een Azure-abonnement configureren 
> * Azure Cosmos DB onderzoeken
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * Wijzigingen doorvoeren in Git en automatisch implementeren naar Azure
> * Resources opschonen

Zie [uw cd-pipeline (multi-fase continue implementatie) definiëren](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=azure-devops&viewFallbackFrom=vsts) voor meer informatie en de volgende stappen.


