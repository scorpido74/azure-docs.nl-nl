---
title: 'Zelfstudie: Implementeer ASP.NET-Apps om te Azure Functions met Azure DevOps Projects'
description: Azure DevOps Projects zorgt ervoor dat u eenvoudig aan de slag kunt met Azure. Met DevOps Projects kunt u in een paar snelle stappen uw ASP.NET-app implementeren in Azure Functions.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 06/20/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 4e7e9428af86f131632650f18d45e7dd48f4b5cb
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71971559"
---
# <a name="continuously-deploy-to-azure-functions-with-devops-projects"></a>Continu implementeren naar Azure Functions met DevOps Projects

Azure DevOps Projects biedt een vereenvoudigde ervaring waar u uw bestaande code en Git-opslagplaats gebruikt of een voorbeeldtoepassing kiest voor het maken van een CI- (Continue integratie) en CD-pijplijn (Continue levering) naar Azure.

In DevOps Projects gebeurt ook het volgende:

* Maakt automatisch Azure-resources, zoals Azure Functions

* Hiermee wordt een release pijplijn gemaakt en geconfigureerd in azure DevOps voor CI/CD

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>* DevOps Projects gebruiken om een ASP.NET-app te implementeren in azure function
>* Azure DevOps en een Azure-abonnement configureren
>* De Azure-functie controleren
>* De CI-pijplijn onderzoeken
>* De CD-pijplijn onderzoeken
>* Wijzigingen doorvoeren in Git en automatisch implementeren naar Azure
>* Resources opschonen

Momenteel zijn de ondersteunde Runtimes voor-functies **.net** en **node. js**. We gebruiken. NET runtime voor deze zelf studie om te implementeren in Azure Functions. 

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. U kunt gratis profiteren van [Visual Studio dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-projects-to-deploy-an-aspnet-app-to-azure-functions"></a>DevOps Projects gebruiken om een ASP.NET-app te implementeren op Azure Functions

In DevOps Projects wordt een CI/CD-pijplijn gemaakt in Azure Pipelines. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. DevOps Projects maakt ook Azure-resources, zoals een IoTHub, in het Azure-abonnement van uw keuze.

1. Meld u aan bij [Azure Portal](https://portal.azure.com)

1. Selecteer **Een resource maken** in het linkerdeelvenster.

1. Typ **DevOps projects**in het zoekvak en klik vervolgens op **toevoegen**.

   ![DevOps Projects](_img/azure-devops-project-functions/devops-project.png)

1. Selecteer **.NET** en selecteer vervolgens **Volgende**. Onder **Kies een toepassings raamwerk**selecteert u **ASP.net** en klikt u op **volgende**.

1. Selecteer **functie-app** en selecteer vervolgens **volgende**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Azure DevOps en Azure-abonnement configureren

1. Voer een naam in voor uw Azure DevOps-project.

1. Maak een nieuwe Azure DevOps-organisatie of selecteer een bestaande organisatie.

1. Selecteer uw Azure-abonnement.

1. Als u aanvullende Azure-configuratie-instellingen wilt weer geven en de prijs categorie en de locatie wilt identificeren, klikt u op aanvullende instellingen. In dit deel venster worden verschillende opties weer gegeven voor het configureren van de prijs categorie en de locatie van Azure-Services.

1. Sluit het configuratie gebied van Azure af en selecteer vervolgens gereed.

1. Na enkele minuten is het proces voltooid. Een voor beeld van een ASP.NET-app is ingesteld in een Git-opslag plaats in uw Azure DevOps-organisatie, een functie-app en Application Insights wordt gemaakt, er een CI/CD-pijp lijn wordt uitgevoerd en uw app is geïmplementeerd in Azure.

   Nadat u dit alles hebt voltooid, wordt het dashboard van Azure DevOps Projects in de Azure-portal weergegeven. U kunt ook rechtstreeks vanuit **Alle resources** in de Azure-portal naar het dashboard van DevOps Projects gaan.

   Dit dash board biedt inzicht in uw Azure DevOps code-opslag plaats, uw CI/CD-pijp lijn en uw Azure-functie. U kunt meer CI/CD-opties configureren in uw Azure DevOps-pijplijn. Selecteer aan de rechter kant **functie-app** om weer te geven.

## <a name="examine-the-function-app"></a>De functie-app controleren

DevOps Projects de functie-app automatisch configureert, die u kunt verkennen en aanpassen. Ga als volgt te werk om te weten wat de functie-app is:

1. Ga naar het DevOps Projects-dashboard.

    ![DevOps Projects dash board](_img/azure-devops-project-functions/devops-projects-dashboard.png)

1. Selecteer aan de rechter kant de functie-app. Er wordt een deel venster geopend voor de functie-app. In deze weer gave kunt u verschillende acties uitvoeren, zoals het bewaken van bewerkingen, het zoeken naar Logboeken.

    ![Functie-app](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>De CI-pijplijn onderzoeken

DevOps Projects configureert automatisch een Azure-CI/CD-pijplijn in uw Azure DevOps-organisatie. U kunt de pijplijn verkennen en aanpassen. Ga als volgt te werk om vertrouwd te raken met de pijplijn:

1. Ga naar het DevOps Projects-dashboard.

1. Klik op de Hyper link onder **Build**. Op een tabblad in de browser wordt de build-pijplijn voor het nieuwe project weergegeven.

    ![Ontwikkelen](_img/azure-devops-project-functions/build.png)

1. Selecteer **Bewerken**. In dit deelvenster kunt u de verschillende taken voor uw build-pijplijn onderzoeken. De build voert diverse taken uit, zoals het ophalen van de bron code uit de Git-opslag plaats, het bouwen van de toepassing, het uitvoeren van eenheids tests en het publiceren van uitvoer die worden gebruikt voor implementaties.

1. Selecteer **Triggers**. In DevOps Projects wordt automatisch een CI-trigger gemaakt en met elke doorvoering naar de opslagplaats wordt een nieuwe build gestart. U kunt eventueel kiezen of u branches van het CI-proces wilt opnemen of uitsluiten.

1. Selecteer **Retentie**. Afhankelijk van het scenario kunt u beleidsregels opgeven om een bepaald aantal builds te behouden of te verwijderen.

1. Selecteer bovenaan de build-pijplijn de naam van de build-pijplijn.

1. Wijzig de naam van uw build-pijp lijn in iets beschrijvender en selecteer vervolgens **Opslaan** in de vervolg keuzelijst **Opslaan & wachtrij** .

1. Selecteer onder de naam van de build-pijplijn de optie **Geschiedenis**. In dit deelvenster ziet u een audittrail van recente wijzigingen voor de build. Azure DevOps houdt de wijzigingen bij die zijn aangebracht in de build-pijp lijn en stelt u in staat om versies te vergelijken.

## <a name="examine-the-cd-release-pipeline"></a>De CD-release-pijplijn onderzoeken

In DevOps Projects worden automatisch de benodigde stappen gemaakt en geconfigureerd om vanuit uw Azure DevOps-organisatie te implementeren naar uw Azure-abonnement. Deze stappen omvatten het configureren van een Azure-serviceverbinding om Azure DevOps te verifiëren bij uw Azure-abonnement. Er wordt ook automatisch ook een release-pijplijn gemaakt, die de CD levert aan Azure. Voor meer informatie over de release-pijplijn doet u het volgende:

1. Navigeer naar de **pijp lijnen | Releases**.

1. Klik op **Edit**.

1. Onder **Artefacten** selecteert u **Neerzetten**. Met de build-pijplijn die u in de vorige stappen hebt onderzocht, wordt de uitvoer geproduceerd die wordt gebruikt voor het artefact.

1. Selecteer **Continue implementatietrigger** rechts van het pictogram **Neerzetten**. Deze release-pijplijn heeft een ingeschakelde CD-trigger die een implementatie uitvoert telkens wanneer een nieuw build-artefact beschikbaar is. U kunt de trigger eventueel uitschakelen zodat de implementaties handmatig moeten worden uitgevoerd.

1. Selecteer aan de rechterkant **Versies weergeven** om een versiegeschiedenis weer te geven.

1. Klik op de release, waarmee de pijp lijn wordt weer gegeven. Klik op een wille keurige omgeving om de release **Samenvatting, door voeringen**, bijbehorende **werk items**te controleren.

1. Selecteer **Doorvoeringen**. In deze weergave worden de codedoorvoeringen weergegeven die zijn gekoppeld aan deze implementatie. Vergelijk versies om de doorvoerverschillen tussen implementaties weer te geven.

1. Selecteer **Logboeken weer geven**. De logboeken bevatten nuttige informatie over het implementatieproces. U kunt beide weergeven tijdens en na de implementaties.

## <a name="commit-code-changes-and-execute-cicd"></a>Codewijzigingen doorvoeren en CI/CD uitvoeren

> [!NOTE]
> Met de volgende procedure wordt de CI/CD-pijplijn getest door een eenvoudige tekstwijziging aan te brengen.

U bent nu klaar om samen te werken met een team in uw app met behulp van een CI/CD-proces waarmee automatisch uw nieuwste werk wordt geïmplementeerd in uw Azure-functie. Bij elke wijziging in de Git-opslagplaats wordt een build gestart in Azure DevOps, en met een CD-pijplijn wordt een implementatie uitgevoerd in Azure. Volg de procedure in deze sectie of gebruik een andere methode om wijzigingen in de opslagplaats door te voeren. U kunt bijvoorbeeld de Git-opslagplaats in uw favoriete hulpprogramma of IDE klonen en wijzigingen vervolgens naar deze opslagplaats pushen.

1. Selecteer in het menu van Azure DevOps de optie **opslag plaatsen | Bestanden**en ga vervolgens naar uw opslag plaats.

1. De opslag plaats bevat al code met de naam **SampleFunctionApp** op basis van de toepassings taal die u hebt gekozen tijdens het maken van het proces. Open het bestand **Application/SampleFunctionApp/Function1. cs** .

1. Selecteer **bewerken**en breng vervolgens een wijziging aan in **regel nummer 31** . U kunt deze bijvoorbeeld bijwerken naar **Hello. Welkom bij Azure Functions met behulp van DevOps Projects @ no__t-0

1. Selecteer in de rechterbovenhoek **Doorvoeren** en selecteer vervolgens nogmaals **Doorvoeren** om de wijziging te pushen.

1. Open het bestand **Application/SampleFunctionApp. test/Function1TestRunner. cs** . 

1. Selecteer **bewerken**en breng vervolgens een wijziging aan in **regel nummer 21**. U kunt deze bijvoorbeeld bijwerken naar **Hello. Welkom bij Azure Functions met behulp van Azure DevOps Projects @ no__t-0.

     Na een paar seconden wordt er in Azure DevOps een build gestart en wordt er een versie uitgevoerd om de wijzigingen te implementeren. Bewaak de buildstatus via het DevOps Projects-dashboard of in de browser met uw Azure DevOps-organisatie.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de gerelateerde resources die u hebt gemaakt, verwijderen wanneer u ze niet meer nodig hebt. Gebruik de functionaliteit **Verwijderen** op het DevOps Projects-dashboard.

## <a name="next-steps"></a>Volgende stappen

U kunt de build- en release-pijplijn desgewenst wijzigen in overeenstemming met de behoeften van uw team. U kunt dit CI/CD-patroon ook als een sjabloon voor uw andere pijplijnen gebruiken. In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * DevOps Projects gebruiken om een ASP.NET Core app te implementeren in azure function
> * Azure DevOps en een Azure-abonnement configureren 
> * De Azure-functie controleren
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * Wijzigingen doorvoeren in Git en automatisch implementeren naar Azure
> * Resources opschonen

