---
title: 'Zelf studie: uw ASP.NET-app implementeren op Azure virtual machines met behulp van Azure DevOps starter'
description: DevOps Starter maakt het eenvoudig om aan de slag te gaan met Azure en om uw ASP.NET-app in een paar snelle stappen te implementeren op Azure virtual machines.
ms.author: mlearned
manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 80a590ff97cc6595f2da6d1e573820324a46c2d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82231497"
---
# <a name="tutorial-deploy-your-aspnet-app-to-azure-virtual-machines-by-using-azure-devops-starter"></a>Zelf studie: uw ASP.NET-app implementeren op Azure virtual machines met behulp van Azure DevOps starter

Azure DevOps starter biedt een vereenvoudigde ervaring waar u uw bestaande code en git opslag plaats kunt meenemen, of een voorbeeld toepassing moet kiezen voor het maken van een doorlopende integratie (CI) en continue levering (CD)-pijp lijn naar Azure. 

DevOps Starter:
* Er worden automatisch Azure-resources gemaakt, zoals een nieuwe Azure-VM (virtuele machine).
* Er wordt een release-pijplijn gemaakt en geconfigureerd in Azure DevOps die een build-pijplijn voor CI bevat.
* Er wordt een release-pijplijn voor CD ingesteld. 
* Er wordt een Azure Application Insights-resource gemaakt voor de bewaking.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * DevOps starter gebruiken om uw ASP.NET-app te implementeren
> * Azure DevOps en een Azure-abonnement configureren 
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * Wijzigingen doorvoeren in Azure Repos en automatisch implementeren naar Azure
> * Azure Application Insights-bewaking configureren
> * Resources opschonen

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. U kunt er een gratis krijgen via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-starter-to-deploy-your-aspnet-app"></a>DevOps starter gebruiken om uw ASP.NET-app te implementeren

DevOps Starter maakt een CI/CD-pijp lijn in azure-pijp lijnen. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. Met DevOps Projects worden ook Azure-resources gemaakt, zoals virtuele machines, in het Azure-abonnement van uw keuze.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Typ **DevOps starter**in het zoekvak en selecteer. Klik op **toevoegen** om een nieuw item te maken.

    ![Het DevOps-starter dash board](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Selecteer **.NET** en vervolgens **Volgende**.

1. Selecteer onder **Een toepassingsframework kiezen** de optie **ASP.NET**. Selecteer vervolgens **Volgende**. Het toepassingsframework dat u in een vorige stap hebt gekozen, bepaalt welk type implementatiedoel hier beschikbaar is voor de Azure-service. 

1. Selecteer de virtuele machine en selecteer vervolgens **Volgende**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps en een Azure-abonnement configureren

1. Maak een nieuwe Azure DevOps-organisatie of selecteer een bestaande organisatie. 

1. Voer een naam in voor het Azure DevOps-project. 

1. Selecteer uw Azure-abonnementsservices. U kunt eventueel ook **Wijzigen** selecteren en vervolgens meer configuratiegegevens invoeren, zoals de locatie van de Azure-resources.
 
1. Voer een naam, gebruikersnaam en wachtwoord in voor de nieuwe virtuele Azure-machine, en selecteer vervolgens **Gereed**. Na enkele minuten is de virtuele Azure-machine klaar. Er wordt een voorbeeld-ASP.NET-toepassing ingesteld in een opslagplaats in uw Azure DevOps-organisatie, er worden een build en een release uitgevoerd, en de toepassing wordt geïmplementeerd op de zojuist gemaakte Azure-VM. 

   Nadat deze is voltooid, wordt het DevOps-start dashboard weer gegeven in de Azure Portal. U kunt ook rechtstreeks vanuit **Alle resources** in de Azure-portal naar het dashboard navigeren. 

   Het dashboard biedt inzicht in uw Azure DevOps-codeopslagplaats, uw CI/CD-pijplijn, en uw actieve toepassing in Azure.   

   ![Dashboardweergave](_img/azure-devops-project-vms/vm-starter-dashboard.png)

DevOps starter configureert automatisch een CI-activerings-en release-trigger waarmee code wijzigingen in uw opslag plaats worden geïmplementeerd. U kunt verder aanvullende opties configureren in Azure DevOps. Selecteer **Bladeren** om de actieve toepassing weer te geven.
    
## <a name="examine-the-ci-pipeline"></a>De CI-pijplijn onderzoeken
 
DevOps starter heeft automatisch een CI/CD-pijp lijn geconfigureerd in azure-pijp lijnen. U kunt de pijplijn verkennen en aanpassen. Ga als volgt te werk om vertrouwd te raken met de build-pijplijn:

1. Selecteer boven aan het DevOps-starter-dash board **Build pijp lijnen**. Op een tabblad in de browser wordt de build-pijplijn voor het nieuwe project weergegeven.

1. Wijs het veld **status** aan en selecteer vervolgens het weglatings teken (...). In een menu worden verschillende opties weer gegeven, zoals het in de wachtrij plaatsen van een nieuwe build, het onderbreken van een build en het bewerken van de build-pijp lijn.

1. Selecteer **Bewerken**.

1. In dit deelvenster kunt u de verschillende taken voor uw build-pijplijn onderzoeken. In de build worden verschillende taken uitgevoerd, zoals het ophalen van bronnen uit de Git-opslagplaats, het herstellen van afhankelijkheden, en het publiceren van uitvoergegevens die worden gebruikt voor implementaties.

1. Selecteer bovenaan de build-pijplijn de naam van de build-pijplijn.

1. Wijzig de naam van de build-pijplijn in een meer beschrijvende naam. Selecteer **Opslaan en wachtrij** en selecteer vervolgens **Opslaan**.

1. Selecteer onder de naam van de build-pipeline de optie **Geschiedenis**. In dit deelvenster ziet u een audittrail van recente wijzigingen voor de build. In Azure DevOps worden alle wijzigingen in de build-pijplijn bijgehouden en krijgt u de mogelijkheid om versies te vergelijken.

1. Selecteer **Triggers**. DevOps Starter maakt automatisch een CI-trigger en elke door voering aan de opslag plaats start een nieuwe build. Desgewenst kunt u kiezen of u vertakkingen van het CI-proces wilt opnemen of uitsluiten.

1. Selecteer **Retentie**. Afhankelijk van het scenario kunt u beleidsregels opgeven om een bepaald aantal builds te behouden of te verwijderen.

## <a name="examine-the-cd-pipeline"></a>De CD-pijplijn onderzoeken

DevOps Starter maakt en configureert automatisch de benodigde stappen voor het implementeren van uw Azure DevOps-organisatie naar uw Azure-abonnement. Deze stappen omvatten het configureren van een Azure-serviceverbinding om Azure DevOps te verifiëren bij uw Azure-abonnement. Er wordt ook automatisch een CD-pijplijn gemaakt die de CD aan de virtuele machine van Azure verstrekt. Voor meer informatie over de CD-pijplijn van Azure DevOps doet u het volgende:

1. Selecteer **Build en release** en selecteer vervolgens **Releases**.  DevOps Starter maakt een release pijplijn voor het beheren van implementaties naar Azure.

1. Selecteer het beletselteken (...) naast de release-pijplijn en selecteer **Bewerken**. De release-pijplijn bevat een *pijplijn* die het releaseproces definieert.

1. Onder **Artefacten** selecteert u **Neerzetten**. Met de build-pijplijn die u in de vorige stappen hebt onderzocht, wordt de uitvoer geproduceerd die wordt gebruikt voor het artefact. 

1. Selecteer naast het pictogram **Neerzetten** de optie **Continue implementatietrigger**. Deze release-pijplijn heeft een ingeschakelde CD-trigger op basis waarvan een implementatie wordt uitgevoerd telkens wanneer een nieuw build-artefact beschikbaar is. U kunt de trigger eventueel uitschakelen zodat de implementaties handmatig moeten worden uitgevoerd. 

1. Selecteer aan de linkerkant **Taken** en selecteer uw omgeving. Taken zijn de activiteiten die worden uitgevoerd tijdens het implementatieproces. Deze zijn gegroepeerd in fasen. Deze release-pijplijn bestaat uit twee fasen:

    - De eerste fase bevat een implementatietaak voor de Azure-resourcegroep waarmee twee acties worden uitgevoerd:
     
        - Configureren van de VM voor implementatie
        -   Toevoegen van de nieuwe VM in een Azure DevOps-implementatiegroep. De VM-implementatie groep in azure DevOps beheert logische groepen met implementatie doel machines
     
    - In de tweede fase wordt met behulp van een IIS-web-app-beheertaak een IIS-website gemaakt op de VM. Er wordt een tweede IIS-web-app-implementatietaak gemaakt om de site te implementeren.

1. Selecteer aan de rechterkant **Versies weergeven** om een versiegeschiedenis weer te geven.

1. Selecteer het beletselteken (...) naast een versie en selecteer **Openen**. U kunt verschillende menu's verkennen, zoals een versieoverzicht, gekoppelde werkitems en tests.

1. Selecteer **Doorvoeringen**. In deze weergave worden de codedoorvoeringen weergegeven die zijn gekoppeld aan deze implementatie. Vergelijk versies om de doorvoerverschillen tussen implementaties weer te geven.

1. Selecteer **Logboeken**. De logboeken bevatten nuttige informatie over het implementatieproces. U kunt beide weergeven tijdens en na de implementaties.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Wijzigingen doorvoeren in Azure Repos en automatisch implementeren naar Azure 

U bent nu klaar om met een team samen te werken aan de app met behulp van een CI/CD-proces waarmee automatisch uw meest recente werk op uw website wordt geïmplementeerd. Bij elke wijziging in de Git-opslagplaats wordt een build gestart in Azure DevOps, en met een CD-pijplijn wordt een implementatie uitgevoerd in Azure. Volg de procedure in deze sectie of gebruik een andere methode om wijzigingen in de opslagplaats door te voeren. Met de codewijzigingen wordt het CI/CD-proces gestart en worden de nieuwe wijzigingen automatisch geïmplementeerd op de IIS-website op de Azure-VM.

1. Selecteer in het linkerdeelvenster **Code** en ga naar uw opslagplaats.

1. Ga naar de map *Views\Home*, selecteer het beletselteken naast het bestand *Index.cshtml* en selecteer vervolgens **Bewerken**.

1. Breng een wijziging aan in het bestand, bijvoorbeeld door wat tekst toe te voegen in een van de div-tags. 

1. Selecteer in de rechterbovenhoek **Doorvoeren** en selecteer vervolgens nogmaals **Doorvoeren** om de wijziging te pushen. Na een paar seconden wordt er in Azure DevOps een build gestart en wordt er een versie uitgevoerd om de wijzigingen te implementeren. Bewaak de status van de build in het DevOps-start dashboard of in de browser met uw Azure DevOps-organisatie.

1. Wanneer de release is voltooid, vernieuwt u de toepassing om de wijzigingen te controleren.

## <a name="configure-azure-application-insights-monitoring"></a>Azure Application Insights-bewaking configureren

Met Azure Application Insights kunt u eenvoudig de prestaties en het gebruik van een webtoepassing controleren. DevOps starter configureert automatisch een Application Insights resource voor uw toepassing. U kunt verder diverse waarschuwingen en bewakingsopties configureren, indien nodig.

1. Ga in het Azure Portal naar het DevOps starter-dash board. 

1. Selecteer rechtsonder de koppeling **Application Insights** voor de app. Het deelvenster **Application Insights** wordt geopend. Deze weergave bevat controlegegevens over gebruik, prestaties en beschikbaarheid voor uw app.

   ![Het deelvenster Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Selecteer **Tijdsbereik** en selecteer vervolgens **Afgelopen uur**. Selecteer **Bijwerken** om de resultaten te filteren. U kunt nu alle activiteiten van de afgelopen 60 minuten bekijken. 
    
1. Selecteer **x** om het tijdsbereik af te sluiten.

1. Selecteer **Waarschuwingen** en selecteer vervolgens **Metrische waarschuwing toevoegen**. 

1. Voer een naam in voor de waarschuwing.

1. Verken in de vervolgkeuzelijst **Metrische gegevens** de verschillende metrische waarschuwingsgegevens. De standaardwaarschuwing is voor een **serverreactietijd langer dan 1 seconde**. U kunt eenvoudig tal van waarschuwingen configureren om de controlemogelijkheden van uw app te verbeteren.

1. Schakel het selectievakje **Eigenaars, bijdragers en lezers op de hoogte brengen via e-mail** in. U kunt eventueel aanvullende acties uitvoeren wanneer een waarschuwing wordt weergegeven, door een logische app van Azure uit te voeren.

1. Selecteer **OK** om de waarschuwing te maken. Na enkele ogenblikken wordt de waarschuwing als actief weergegeven op het dashboard. 

1. Sluit het gebied **Waarschuwingen** af en ga terug naar het deelvenster **Application Insights**.

1. Selecteer **Beschikbaarheid** en selecteer vervolgens **Test toevoegen**. 

1. Voer een testnaam in en selecteer vervolgens **Maken**. Hiermee maakt u eenvoudige ping-test om de beschikbaarheid van uw toepassing te controleren. Na een paar minuten zijn de resultaten beschikbaar en toont het Application Insights-dashboard een beschikbaarheidsstatus.

## <a name="clean-up-resources"></a>Resources opschonen

Tijdens het testen kunt u voorkomen dat de kosten oplopen door resources op te schonen. U kunt de virtuele Azure-machine en de gerelateerde resources die u in deze zelfstudie hebt gemaakt, verwijderen wanneer u ze niet meer nodig hebt. Als u dit wilt doen, gebruikt u de **verwijderings** functionaliteit op het DevOps starter-dash board. 

> [!IMPORTANT]
> Met de volgende procedure worden resources permanent verwijderd. De functie *verwijderen* vernietigt de gegevens die door het project in DevOps starter zijn gemaakt in zowel Azure als Azure DevOps, en u kunt deze niet ophalen. Gebruik deze procedure pas nadat u de prompts zorgvuldig hebt gelezen.

1. Ga in het Azure Portal naar het DevOps starter-dash board.
1. Selecteer in de rechterbovenhoek **Verwijderen**. 
1. Selecteer **Ja** bij de prompt om de resources *definitief te verwijderen*.

U kunt de build- en release-pijplijn desgewenst wijzigen in overeenstemming met de behoeften van uw team. U kunt dit CI/CD-patroon ook als een sjabloon voor uw andere pijplijnen gebruiken. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * DevOps starter gebruiken om uw ASP.NET-app te implementeren
> * Azure DevOps en een Azure-abonnement configureren 
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * Wijzigingen doorvoeren in Azure Repos en automatisch implementeren naar Azure
> * Azure Application Insights-bewaking configureren
> * Resources opschonen

Voor meer informatie over de CI/CD-pijplijn, zie:

> [!div class="nextstepaction"]
> [Een CD-pijplijn (continue implementatie) met meerdere fasen definiëren](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
