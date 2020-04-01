---
title: 'Zelfstudie: ASP.NET-apps implementeren voor Azure-functies met Azure DevOps-projecten'
description: Azure DevOps Projects zorgt ervoor dat u eenvoudig aan de slag kunt met Azure. Met DevOps Projects u uw ASP.NET-app in een paar snelle stappen implementeren in Azure-functies.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 06/20/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 4e7e9428af86f131632650f18d45e7dd48f4b5cb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "71971559"
---
# <a name="continuously-deploy-to-azure-functions-with-devops-projects"></a>Continu implementeren in Azure-functies met DevOps-projecten

Azure DevOps Projects biedt een vereenvoudigde ervaring waar u uw bestaande code en Git-opslagplaats gebruikt of een voorbeeldtoepassing kiest voor het maken van een pijplijn voor CI (Continue integratie) en CD (Continue levering) naar Azure.

In DevOps Projects gebeurt ook het volgende:

* Hiermee worden automatisch Azure-resources, zoals Azure-functies,

* Hiermee maakt en configureert u een releasepijplijn in Azure DevOps voor CI/CD

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>* DevOps-projecten gebruiken om een ASP.NET-app te implementeren in azure-functie
>* Azure DevOps en een Azure-abonnement configureren
>* De Azure-functie onderzoeken
>* De CI-pijplijn onderzoeken
>* De CD-pijplijn onderzoeken
>* Wijzigingen doorvoeren in Git en automatisch implementeren naar Azure
>* Resources opschonen

Momenteel zijn de ondersteunde runtimes voor functies **.NET** en **Node.js**. We gebruiken. NET-runtime voor deze zelfstudie om te implementeren naar Azure-functies. 

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Je er een gratis krijgen via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-projects-to-deploy-an-aspnet-app-to-azure-functions"></a>DevOps-projecten gebruiken om een ASP.NET-app te implementeren in Azure-functies

In DevOps Projects wordt een CI/CD-pijplijn gemaakt in Azure Pipelines. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. DevOps Projects maakt ook Azure-resources, zoals een IoTHub, in het Azure-abonnement van uw keuze.

1. Aanmelden bij de [Azure-portal](https://portal.azure.com)

1. Selecteer **Een resource maken** in het linkerdeelvenster.

1. Typ **DevOps-projecten**in het zoekvak en klik op **Toevoegen**.

   ![DevOps Projects](_img/azure-devops-project-functions/devops-project.png)

1. Selecteer **.NET** en vervolgens **Volgende**. Selecteer **onder Een toepassingskader kiezen** **ASP.NET** en klik op **Volgende**.

1. Selecteer **Functie-app** en selecteer **Volgende**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Azure DevOps en Azure-abonnement configureren

1. Voer een naam in voor het Azure DevOps-project.

1. Maak een nieuwe Azure DevOps-organisatie of selecteer een bestaande organisatie.

1. Selecteer uw Azure-abonnement.

1. Als u aanvullende Azure-configuratie-instellingen wilt weergeven en de prijscategorie en -locatie wilt identificeren, klikt u op Aanvullende instellingen. In dit deelvenster worden verschillende opties weergegeven voor het configureren van de prijscategorie en locatie van Azure-services.

1. Verlaat het Azure-configuratiegebied en selecteer Gereed.

1. Na enkele minuten is het proces voltooid. Er wordt een voorbeeld ASP.NET-app is ingesteld in een Git-repo in uw Azure DevOps-organisatie, een functie-app en toepassingsinzichten wordt gemaakt, een CI/CD-pijplijn wordt uitgevoerd en uw app wordt geïmplementeerd in Azure.

   Nadat u dit alles hebt voltooid, wordt het dashboard van Azure DevOps Projects in de Azure-portal weergegeven. U kunt ook rechtstreeks vanuit **Alle resources** in de Azure-portal naar het dashboard van DevOps Projects gaan.

   Dit dashboard biedt inzicht in uw Azure DevOps-coderepository, uw CI/CD-pijplijn en uw Azure-functie. U kunt meer CI/CD-opties configureren in uw Azure DevOps-pijplijn. Selecteer rechts **Functie-app** om weer te geven.

## <a name="examine-the-function-app"></a>De functie-app onderzoeken

DevOps Projects configureert automatisch de functie-app, die u verkennen en aanpassen. Ga als volgt te werk om de functie-app te leren kennen:

1. Ga naar het DevOps Projects-dashboard.

    ![Dashboard DevOps-projecten](_img/azure-devops-project-functions/devops-projects-dashboard.png)

1. Selecteer rechts de functie-app. Er wordt een deelvenster geopend voor de functie-app. Vanuit deze weergave u verschillende acties uitvoeren, zoals operations monitoring, zoeken logs.

    ![Functie-app](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>De CI-pijplijn onderzoeken

DevOps Projects configureert automatisch een Azure-CI/CD-pijplijn in uw Azure DevOps-organisatie. U kunt de pijplijn verkennen en aanpassen. Ga als volgt te werk om vertrouwd te raken met de pijplijn:

1. Ga naar het DevOps Projects-dashboard.

1. Klik op de hyperlink onder **Bouwen**. Op een tabblad in de browser wordt de build-pijplijn voor het nieuwe project weergegeven.

    ![Ontwikkelen](_img/azure-devops-project-functions/build.png)

1. Selecteer **Bewerken**. In dit deelvenster kunt u de verschillende taken voor uw build-pijplijn onderzoeken. De build voert verschillende taken uit, zoals het ophalen van broncode van de Git repo, het bouwen van de toepassing, het uitvoeren van eenheidstests en het publiceren van uitvoer die worden gebruikt voor implementaties.

1. Selecteer **Triggers**. In DevOps Projects wordt automatisch een CI-trigger gemaakt en met elke doorvoering naar de opslagplaats wordt een nieuwe build gestart. Desgewenst kunt u kiezen of u vertakkingen van het CI-proces wilt opnemen of uitsluiten.

1. Selecteer **Retentie**. Afhankelijk van het scenario kunt u beleidsregels opgeven om een bepaald aantal builds te behouden of te verwijderen.

1. Selecteer bovenaan de build-pijplijn de naam van de build-pijplijn.

1. Wijzig de naam van uw buildpijplijn in iets beschrijvender en selecteer **Opslaan** in de vervolgkeuzelijst **Opslaan & wachtrij.**

1. Selecteer onder de naam van de build-pipeline de optie **Geschiedenis**. In dit deelvenster ziet u een audittrail van recente wijzigingen voor de build. Azure DevOps houdt alle wijzigingen in de buildpijplijn bij en stelt u in staat versies te vergelijken.

## <a name="examine-the-cd-release-pipeline"></a>De CD-release-pijplijn onderzoeken

In DevOps Projects worden automatisch de benodigde stappen gemaakt en geconfigureerd om vanuit uw Azure DevOps-organisatie te implementeren naar uw Azure-abonnement. Deze stappen omvatten het configureren van een Azure-serviceverbinding om Azure DevOps te verifiëren bij uw Azure-abonnement. Er wordt ook automatisch ook een release-pijplijn gemaakt, die de CD levert aan Azure. Voor meer informatie over de release-pijplijn doet u het volgende:

1. Navigeren naar de **pijplijnen | Releases**.

1. Klik op **Edit**.

1. Onder **Artefacten** selecteert u **Neerzetten**. Met de build-pijplijn die u in de vorige stappen hebt onderzocht, wordt de uitvoer geproduceerd die wordt gebruikt voor het artefact.

1. Selecteer **Continue implementatietrigger** rechts van het pictogram **Neerzetten**. Deze release-pijplijn heeft een ingeschakelde CD-trigger die een implementatie uitvoert telkens wanneer een nieuw build-artefact beschikbaar is. U kunt de trigger eventueel uitschakelen zodat de implementaties handmatig moeten worden uitgevoerd.

1. Selecteer aan de rechterkant **Versies weergeven** om een versiegeschiedenis weer te geven.

1. Klik op de release, die de pijplijn weergeeft. Klik op een omgeving om het **releaseoverzicht, commits ,** bijbehorende **werkitems**te controleren.

1. Selecteer **Doorvoeringen**. In deze weergave worden de codedoorvoeringen weergegeven die zijn gekoppeld aan deze implementatie. Vergelijk versies om de doorvoerverschillen tussen implementaties weer te geven.

1. Selecteer **Logboeken weergeven**. De logboeken bevatten nuttige informatie over het implementatieproces. U kunt beide weergeven tijdens en na de implementaties.

## <a name="commit-code-changes-and-execute-cicd"></a>Codewijzigingen doorvoeren en CI/CD uitvoeren

> [!NOTE]
> Met de volgende procedure wordt de CI/CD-pijplijn getest door een eenvoudige tekstwijziging aan te brengen.

U bent nu klaar om samen te werken met een team in uw app door een CI/CD-proces te gebruiken dat uw nieuwste werk automatisch implementeert in uw Azure-functie. Bij elke wijziging in de Git-opslagplaats wordt een build gestart in Azure DevOps, en met een CD-pijplijn wordt een implementatie uitgevoerd in Azure. Volg de procedure in deze sectie of gebruik een andere methode om wijzigingen in de opslagplaats door te voeren. U kunt bijvoorbeeld de Git-opslagplaats in uw favoriete hulpprogramma of IDE klonen en wijzigingen vervolgens naar deze opslagplaats pushen.

1. Selecteer Repos | in het menu Azure DevOps ** Bestanden,** en ga dan naar je repo.

1. De repository bevat al code genaamd **SampleFunctionApp** op basis van de toepassingstaal die u hebt gekozen in het creatieproces. Open het bestand **Application/SampleFunctionApp/Function1.cs.**

1. Selecteer **Bewerken**en breng vervolgens een wijziging aan in **regelnummer 31** . U het bijvoorbeeld updaten naar **Hello there! Welkom bij Azure-functies met DevOps-projecten**

1. Selecteer in de rechterbovenhoek **Doorvoeren** en selecteer vervolgens nogmaals **Doorvoeren** om de wijziging te pushen.

1. Open het bestand **Application/SampleFunctionApp.Test/Function1TestRunner.cs.** 

1. Selecteer **Bewerken**en breng vervolgens een wijziging aan in **regelnummer 21**. U het bijvoorbeeld updaten naar **Hello there! Welkom bij Azure-functies met Azure DevOps-projecten**.

     Na een paar seconden wordt er in Azure DevOps een build gestart en wordt er een versie uitgevoerd om de wijzigingen te implementeren. Bewaak de buildstatus via het DevOps Projects-dashboard of in de browser met uw Azure DevOps-organisatie.

## <a name="clean-up-resources"></a>Resources opschonen

U de gerelateerde bronnen verwijderen die u hebt gemaakt wanneer u ze niet meer nodig hebt. Gebruik de functionaliteit **Verwijderen** op het DevOps Projects-dashboard.

## <a name="next-steps"></a>Volgende stappen

U kunt de build- en release-pijplijn desgewenst wijzigen in overeenstemming met de behoeften van uw team. U kunt dit CI/CD-patroon ook als een sjabloon voor uw andere pijplijnen gebruiken. In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * DevOps-projecten gebruiken om een ASP.NET Core-app te implementeren in azure-functie
> * Azure DevOps en een Azure-abonnement configureren 
> * De Azure-functie onderzoeken
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * Wijzigingen doorvoeren in Git en automatisch implementeren naar Azure
> * Resources opschonen

