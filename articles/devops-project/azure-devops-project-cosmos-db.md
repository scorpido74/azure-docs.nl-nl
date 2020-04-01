---
title: 'Zelfstudie: Node.js-apps implementeren die worden aangedreven door Azure Cosmos DB met Azure DevOps-projecten'
description: Azure DevOps Projects zorgt ervoor dat u eenvoudig aan de slag kunt met Azure. Met DevOps Projects u uw Node.js-app die wordt aangedreven door Azure Cosmos DB in een paar korte stappen implementeren in Windows Web App.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/11/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 229b4b9f53ea3866dce1169645f6d6da20827271
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73888894"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-projects"></a>Node.js-apps implementeren die worden aangedreven door Azure Cosmos DB met DevOps-projecten

Azure DevOps Projects biedt een gestroomlijnde ervaring waarbij u een permanente integratie (CI) en cd-pijplijn (continuous deployment) naar Azure maken. Dit doe je door je bestaande code en Git repository (repo) te gebruiken of door een voorbeeldtoepassing te selecteren.

In DevOps Projects gebeurt ook het volgende:

* Hiermee worden automatisch Azure-resources gemaakt, zoals Azure Cosmos DB, Azure Application Insights, Azure App Service en App Service-abonnementen

* Een CI/CD-releasepijplijn maken en configureren in Azure DevOps

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * DevOps-projecten gebruiken om een Node.js-app te implementeren die wordt aangedreven door Azure Cosmos DB
> * Azure DevOps en een Azure-abonnement configureren
> * Azure Cosmos DB onderzoeken
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * De wijzigingen vastleggen in Git en deze automatisch implementeren in Azure
> * De resources opschonen

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig, dat u gratis krijgen via [Visual Studio Dev Essentials.](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-projects-to-deploy-nodejs-app"></a>DevOps-projecten gebruiken om de App Node.js te implementeren

In DevOps Projects wordt een CI/CD-pijplijn gemaakt in Azure Pipelines. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. DevOps Projects maakt ook Azure-resources, zoals Azure Cosmos DB, Application Insights, App Service en App Service-abonnementen, in het Azure-abonnement van uw keuze.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **Een resource maken** in het linkerdeelvenster.

1. Voer in het zoekvak **DevOps-projecten**in en selecteer **Toevoegen**.

   ![Deelvenster DevOps-projecten](_img/azure-devops-project-cosmos-db/devops-project.png)

1. Selecteer **Node.js** als runtime en selecteer **Volgende**. Selecteer **Express.js**onder **Een toepassingskader kiezen**.

1. Schakel de sectie **Een database toevoegen** voor Cosmos **DB**in en selecteer **Volgende**.

    ![Een database toevoegen](_img/azure-devops-project-cosmos-db/add-database.png)

    Azure DevOps Projects ondersteunt verschillende toepassingsframeworks, zoals **Express.js**, **Sample Node.js-app**en **Sail.js**. In deze zelfstudie gebruiken we **Express.js**.

1. Selecteer een Azure-service om de toepassing te implementeren en selecteer **Volgende**. Uw opties zijn Windows Web App, Azure Kubernetes Service en Azure Web App voor containers. In deze zelfstudie gebruiken we **Windows Web App**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Azure DevOps en Azure-abonnement configureren

1. Voer een naam in voor het Azure DevOps-project.

1. Maak een nieuwe Azure DevOps-organisatie of selecteer een bestaande organisatie.

1. Selecteer uw Azure-abonnement.

1. Als u aanvullende Azure-configuratie-instellingen wilt weergeven of de prijscategorie en -locatie wilt identificeren, selecteert u **Aanvullende instellingen**. In dit deelvenster worden verschillende opties weergegeven voor het configureren van de prijscategorie en locatie van Azure-services.

1. Verlaat het Azure-configuratiegebied en selecteer **Gereed**.

1. Het proces eindigt na een paar minuten. Een voorbeeld-Node.js-app is ingesteld in een Git-repo in uw Azure DevOps-organisatie. Vervolgens worden Azure Cosmos DB, App Service, App Service-plan en Application Insights-bronnen gemaakt, evenals een CI/CD-pijplijn. Uw app wordt vervolgens geïmplementeerd in Azure.

   Nadat al deze processen zijn voltooid, wordt het Azure DevOps Project-dashboard weergegeven in de Azure-portal. U kunt ook rechtstreeks vanuit **Alle resources** in de Azure-portal naar het dashboard van DevOps Projects gaan.

   Dit dashboard biedt inzicht in uw Azure DevOps-coderepository, uw CI/CD-pijplijn en uw Azure Cosmos DB-database. U kunt meer CI/CD-opties configureren in uw Azure DevOps-pijplijn. Selecteer **Azure Cosmos DB** aan de rechterkant van het dashboard om deze opties weer te geven.

## <a name="examine-azure-cosmos-db"></a>Azure Cosmos DB onderzoeken

DevOps Projects configureert automatisch Azure Cosmos DB, die u verkennen en aanpassen. Ga als volgt te werk om vertrouwd te raken met Azure Cosmos DB:

1. Ga naar het DevOps Projects-dashboard.

    ![Dashboard DevOps Projects](_img/azure-devops-project-cosmos-db/devops-project-dashboard.png)

1. Selecteer azure cosmos DB aan de rechterkant. Er wordt een deelvenster geopend voor Azure Cosmos DB. Vanuit deze weergave u verschillende acties uitvoeren, zoals het bewaken van bewerkingen en het zoeken naar logboeken.

    ![Azure Cosmos DB-deelvenster](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>De CI-pijplijn onderzoeken

DevOps Projects configureert automatisch een Azure-CI/CD-pijplijn in uw Azure DevOps-organisatie. U kunt de pijplijn verkennen en aanpassen. Ga als volgt te werk om vertrouwd te raken met de pijplijn:

1. Ga naar het DevOps Projects-dashboard.

1. Selecteer de hyperlink onder **Bouwen**. Op een tabblad in de browser wordt de build-pijplijn voor het nieuwe project weergegeven.

    ![Deelvenster Bouwen](_img/azure-devops-project-cosmos-db/build.png)

1. Selecteer **Bewerken**. In dit deelvenster kunt u de verschillende taken voor uw build-pijplijn onderzoeken. De build voert verschillende taken uit, zoals het ophalen van broncode van de Git repo, het bouwen van de toepassing, het uitvoeren van eenheidstests en het publiceren van uitvoer die worden gebruikt voor implementaties.

1. Selecteer **Triggers**. In DevOps Projects wordt automatisch een CI-trigger gemaakt en met elke doorvoering naar de opslagplaats wordt een nieuwe build gestart. U ervoor kiezen om branches op te nemen of uit te sluiten van het CI-proces.

1. Selecteer **Retentie**. Afhankelijk van het scenario kunt u beleidsregels opgeven om een bepaald aantal builds te behouden of te verwijderen.

1. Selecteer bovenaan de build-pijplijn de naam van de build-pijplijn.

1. Wijzig de naam van uw buildpijplijn in iets beschrijvender en selecteer **Opslaan** in de vervolgkeuzelijst **Opslaan & wachtrij.**

1. Selecteer onder de naam van de build-pipeline de optie **Geschiedenis**. In dit deelvenster ziet u een audittrail van recente wijzigingen voor de build. In Azure DevOps worden alle wijzigingen in de build-pijplijn bijgehouden en krijgt u de mogelijkheid om versies te vergelijken.

## <a name="examine-the-cd-release-pipeline"></a>De CD-release-pijplijn onderzoeken

In DevOps Projects worden automatisch de benodigde stappen gemaakt en geconfigureerd om vanuit uw Azure DevOps-organisatie te implementeren naar uw Azure-abonnement. Deze stappen omvatten het configureren van een Azure-serviceverbinding om Azure DevOps te verifiëren bij uw Azure-abonnement. Er wordt ook automatisch ook een release-pijplijn gemaakt, die de CD levert aan Azure. Voor meer informatie over de release-pijplijn doet u het volgende:

1. Ga naar **Pijplijnen** en selecteer **Releases**.

1. Selecteer **Bewerken**.

1. Onder **Artefacten** selecteert u **Neerzetten**. Met de build-pijplijn die u in de vorige stappen hebt onderzocht, wordt de uitvoer geproduceerd die wordt gebruikt voor het artefact.

1. Selecteer rechts van het pictogram **Drop** de optie **Continue implementatietrigger**. Deze releasepijplijn heeft een continue implementatietrigger ingeschakeld, die een implementatie uitvoert elke keer dat er een nieuw buildartefact beschikbaar is. U de trigger uitschakelen zodat uw implementaties handmatig worden uitgevoerd.

1. Selecteer rechts de sectie **Releases weergeven** om een geschiedenis van releases weer te geven.

1. Selecteer de release, waarin de pijplijn wordt weergegeven. Selecteer een omgeving om het releaseoverzicht, commits of bijbehorende werkitems te controleren.

1. Selecteer **Doorvoeringen**. In deze weergave worden de codedoorvoeringen weergegeven die zijn gekoppeld aan deze implementatie. Vergelijk versies om de doorvoerverschillen tussen implementaties weer te geven.

1. Selecteer **Logboeken weergeven**. De logboeken bevatten nuttige informatie over het implementatieproces. U kunt beide weergeven tijdens en na de implementaties.

## <a name="commit-code-changes-and-execute-the-cicd-pipeline"></a>Codewijzigingen vastleggen en de CI/CD-pijplijn uitvoeren

> [!NOTE]
> Met de volgende procedure wordt de CI/CD-pijplijn getest door een eenvoudige tekstwijziging aan te brengen.

U bent nu klaar om samen te werken met een team in uw app door een CI/CD-proces te gebruiken waarmee uw nieuwste werk wordt geïmplementeerd in uw app-service. Bij elke wijziging in de Git-opslagplaats wordt een build gestart in Azure DevOps, en met een CD-pijplijn wordt een implementatie uitgevoerd in Azure. Volg de procedure in deze sectie of gebruik een andere methode om wijzigingen in de opslagplaats door te voeren. U kunt bijvoorbeeld de Git-opslagplaats in uw favoriete hulpprogramma of IDE klonen en wijzigingen vervolgens naar deze opslagplaats pushen.

1. Selecteer **Repo's** en vervolgens **Bestanden**in het menu Azure DevOps . Ga dan naar je repo.

1. De repo bevat al code op basis van de toepassingstaal die u hebt gekozen in het creatieproces. Open het bestand **Toepassing/weergaven/index.pug.**

1. Selecteer **Bewerken**en breng vervolgens een wijziging aan in **regelnummer 15**. U het bijvoorbeeld wijzigen in 'Mijn eerste implementatie in Azure App Service die wordt aangedreven door Azure Cosmos DB'.

1. **Selecteer**Commit in de rechterbovenhoek en selecteer vervolgens **Opnieuw vastleggen** om je wissel te duwen.

     Na een paar seconden start een build in Azure DevOps en wordt een release uitgevoerd om de wijzigingen te implementeren. Bewaak de buildstatus via het DevOps Projects-dashboard of in de browser met uw Azure DevOps-organisatie.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de gerelateerde bronnen die u hebt gemaakt wanneer u ze niet meer nodig hebt. Gebruik de functionaliteit **Verwijderen** op het DevOps Projects-dashboard.

## <a name="next-steps"></a>Volgende stappen

U kunt deze build- en release-pipelines desgewenst wijzigen in overeenstemming met de behoeften van uw team. U kunt dit CI/CD-patroon ook als een sjabloon voor uw andere pijplijnen gebruiken. In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * DevOps-projecten gebruiken om een Node.js-app te implementeren die wordt aangedreven door Azure Cosmos DB
> * Azure DevOps en een Azure-abonnement configureren 
> * Azure Cosmos DB onderzoeken
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * Wijzigingen doorvoeren in Git en automatisch implementeren naar Azure
> * Resources opschonen

Zie [Uw cd-pijplijn (continuous deployment) definiëren](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=azure-devops&viewFallbackFrom=vsts) voor meer informatie en volgende stappen.


