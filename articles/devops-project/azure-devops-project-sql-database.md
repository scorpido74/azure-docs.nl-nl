---
title: 'Zelf studie: uw ASP.NET-app en Azure SQL Database code implementeren met behulp van Azure DevOps starter'
description: DevOps Starter maakt het eenvoudig om aan de slag te gaan met Azure. Met DevOps starter kunt u in een paar snelle stappen uw ASP.NET-app en Azure SQL Database-code implementeren.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: e40eb9cc22cdc071381cc847b49a01d4d713653d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85318621"
---
# <a name="tutorial-deploy-your-aspnet-app-and-azure-sql-database-code-by-using-azure-devops-starter"></a>Zelf studie: uw ASP.NET-app en Azure SQL Database code implementeren met behulp van Azure DevOps starter

Azure DevOps starter biedt een vereenvoudigde ervaring waar u uw bestaande code en git opslag plaats kunt meenemen, of een voorbeeld toepassing moet kiezen voor het maken van een doorlopende integratie (CI) en continue levering (CD)-pijp lijn naar Azure. 

DevOps Starter:
* Maakt automatisch Azure-resources, zoals een data base in Azure SQL Database.
* Er wordt een release-pijplijn gemaakt en geconfigureerd in Azure Pipelines die een build-pijplijn voor CI bevat.
* Er wordt een release-pijplijn voor CD ingesteld. 
* Er wordt een Azure Application Insights-resource gemaakt voor de bewaking.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Azure DevOps starter gebruiken om uw ASP.NET-app en Azure SQL Database code te implementeren
> * Azure DevOps en een Azure-abonnement configureren 
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * Wijzigingen doorvoeren in Azure Repos en automatisch implementeren naar Azure
> * Verbinding maken met Azure SQL Database 
> * Resources opschonen

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. U kunt er een gratis krijgen via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-a-project-in-devops-projects-for-an-aspnet-app-and-azure-sql-database"></a>Een project maken in DevOps Projects voor een ASP.NET-app en Azure SQL Database

DevOps Starter maakt een CI/CD-pijp lijn in azure-pijp lijnen. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. DevOps Starter maakt ook Azure-resources, zoals Azure SQL Database, in het Azure-abonnement van uw keuze.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Typ **DevOps starter**in het zoekvak en selecteer. Klik op **toevoegen** om een nieuw item te maken.

    ![Het DevOps-starter dash board](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Selecteer **.NET** en vervolgens **Volgende**.

1. Selecteer bij **Een toepassingsframework kiezen** de optie **ASP.NET**.

1. Selecteer **Een database toevoegen** en selecteer **Volgende**. Het toepassingsframework dat u in een vorige stap hebt gekozen, bepaalt welk type implementatiedoel hier beschikbaar is voor de Azure-service. 
    
1. Selecteer **Volgende**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps en een Azure-abonnement configureren

1. Maak een nieuwe Azure DevOps-organisatie of selecteer een bestaande organisatie. 

1. Voer een naam in voor het Azure DevOps-project. 

1. Selecteer uw Azure-abonnementsservices. U kunt **Wijzigen** selecteren als u aanvullende configuratie-instellingen van Azure wilt bekijken en de gebruikersnaam in de sectie **Informatie over de aanmeldingsgegevens voor de databaseserver** wilt identificeren. Bewaar de gebruikersnaam voor toekomstige stappen in deze zelfstudie. Als u deze optionele stap uitvoert, sluit u het gebied met de Azure-configuratie voordat u **Gereed** selecteert.
 
1. Selecteer **Voltooid**. Na een paar minuten wordt het proces voltooid en wordt het DevOps starter-dash board geopend in de Azure Portal. U kunt ook rechtstreeks vanuit **Alle resources** in de Azure-portal naar het dashboard navigeren. Selecteer aan de rechterkant **Bladeren** om uw actieve toepassing weer te geven.
    
## <a name="examine-the-ci-pipeline"></a>De CI-pijplijn onderzoeken

DevOps starter configureert automatisch een volledige CI/CD-pijp lijn in azure opslag plaatsen. U kunt de pijplijn verkennen en aanpassen. Ga als volgt te werk om vertrouwd te raken met de Azure DevOps-build-pijplijn:

1. Selecteer boven aan het DevOps-starter-dash board **Build pijp lijnen**. Op een tabblad in de browser wordt de build-pijplijn voor het nieuwe project weergegeven.

1. Wijs het veld **status** aan en selecteer vervolgens het weglatings teken (...). In een menu worden verschillende opties weer gegeven, zoals het in de wachtrij plaatsen van een nieuwe build, het onderbreken van een build en het bewerken van de build-pijp lijn.

1. Selecteer **Bewerken**.

1. In dit deelvenster kunt u de verschillende taken voor uw build-pijplijn onderzoeken. De build voert verschillende taken uit, zoals het ophalen van bronnen uit de Git-opslagplaats, het herstellen van afhankelijkheden en het publiceren van uitvoer die wordt gebruikt voor implementaties.

1. Selecteer bovenaan de build-pijplijn de naam van de build-pijplijn.

1. Wijzig de naam van de build-pijplijn in een meer beschrijvende naam. Selecteer **Opslaan en wachtrij** en selecteer vervolgens **Opslaan**.

1. Selecteer onder de naam van de build-pipeline de optie **Geschiedenis**. In dit deelvenster ziet u een audittrail van recente wijzigingen voor de build. Azure Pipelines houdt alle wijzigingen in de build-pijplijn bij en biedt de mogelijkheid om versies te vergelijken.

1. Selecteer **Triggers**. DevOps Starter maakt automatisch een CI-trigger en elke door voering aan de opslag plaats start een nieuwe build. Desgewenst kunt u kiezen of u vertakkingen van het CI-proces wilt opnemen of uitsluiten.

1. Selecteer **Retentie**. Afhankelijk van het scenario kunt u beleidsregels opgeven om een bepaald aantal builds te behouden of te verwijderen.

## <a name="examine-the-cd-pipeline"></a>De CD-pijplijn onderzoeken

DevOps Starter maakt en configureert automatisch de benodigde stappen voor het implementeren van uw Azure DevOps-organisatie naar uw Azure-abonnement. Deze stappen omvatten het configureren van een Azure-serviceverbinding om Azure DevOps te verifiëren bij uw Azure-abonnement. Er wordt ook automatisch een CD-pijplijn gemaakt die de CD aan de virtuele machine van Azure verstrekt. Voor meer informatie over de CD-pijplijn van Azure DevOps doet u het volgende:

1. Selecteer **Build en release** en selecteer vervolgens **Releases**. DevOps Starter maakt een release pijplijn voor het beheren van implementaties naar Azure.

1. Selecteer het beletselteken (...) naast de release-pijplijn en selecteer **Bewerken**. De release-pijplijn bevat een *pijplijn* die het releaseproces definieert.

1. Onder **Artefacten** selecteert u **Neerzetten**. Met de build-pijplijn die u in de vorige stappen hebt onderzocht, wordt de uitvoer geproduceerd die wordt gebruikt voor het artefact. 

1. Selecteer **Continue implementatietrigger** rechts van het pictogram **Neerzetten**. Deze release-pijplijn heeft een ingeschakelde CD-trigger die een implementatie uitvoert telkens wanneer een nieuw build-artefact beschikbaar is. U kunt de trigger eventueel uitschakelen zodat de implementaties handmatig moeten worden uitgevoerd. 

    DevOps starter stelt een wille keurig SQL-wacht woord in en gebruikt dit voor de release pijplijn.
    
1. Selecteer aan de linkerkant **Variabelen**. 

   > [!NOTE]
   > Voer de volgende stap alleen uit als u het SQL Server-wachtwoord hebt gewijzigd. Er is één wachtwoordvariabele.
  
1. Selecteer het hangslotpictogram naast het vak **Waarde**, voer het nieuwe wachtwoord in en selecteer **Opslaan**.

1. Selecteer aan de linkerkant **Taken** en selecteer uw omgeving. Taken zijn de activiteiten die door het implementatieproces worden uitgevoerd. Ze zijn gegroepeerd in fasen. Deze release-pijplijn heeft één fase. Deze bevat een taak voor een *Azure App Service-implementatie* en een voor een *Azure SQL Database-implementatie*.

1. Selecteer de taak *Azure SQL uitvoeren* en controleer de verschillende eigenschappen die worden gebruikt voor de SQL-implementatie. Onder **Implementatiepakket** ziet u dat de taak gebruikmaakt van een *SQL DACPAC-bestand*.

1. Selecteer aan de rechterkant **Versies weergeven** om een versiegeschiedenis weer te geven.

1. Selecteer het beletselteken (...) naast een versie en selecteer **Openen**. U kunt verschillende menu's verkennen, zoals een versieoverzicht, gekoppelde werkitems en tests.

1. Selecteer **Doorvoeringen**. In deze weergave worden de codedoorvoeringen weergegeven die zijn gekoppeld aan deze implementatie. Vergelijk versies om de doorvoerverschillen tussen implementaties weer te geven.

1. Selecteer **Logboeken**. De logboeken bevatten nuttige informatie over het implementatieproces. U kunt beide weergeven tijdens en na de implementaties.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Wijzigingen doorvoeren in Azure Repos en automatisch implementeren naar Azure 

 > [!NOTE]
 > Met de volgende procedure wordt de CI/CD-pijplijn getest met een eenvoudige wijziging in de tekst. Als u het SQL-implementatieproces wilt testen, kunt u een SQL Server Schema-wijziging aanbrengen in de tabel.

U bent nu klaar om met een team samen te werken aan de app met behulp van een CI/CD-proces waarmee automatisch uw meest recente werk op uw website wordt geïmplementeerd. Bij elke wijziging in de Git-opslagplaats wordt een build gestart in Azure DevOps, en met een CD-pijplijn wordt een implementatie uitgevoerd in Azure. Volg de procedure in deze sectie of gebruik een andere methode om wijzigingen in uw opslagplaats door te voeren. De codewijzigingen beginnen het CI/CD-proces en implementeren de wijzigingen automatisch in Azure.

1. Selecteer in het linkerdeelvenster **Code** en ga naar uw opslagplaats.

1. Ga naar de map *SampleWebApplication\Views\Home*, selecteer het beletselteken (...) naast het bestand *Index.cshtml* en kies **Bewerken**. 

1. Breng een wijziging aan in het bestand, bijvoorbeeld door wat tekst toe te voegen in een van de div-tags. 

1. Selecteer in de rechterbovenhoek **Doorvoeren** en selecteer vervolgens nogmaals **Doorvoeren** om de wijziging te pushen. Na een paar seconden wordt er in Azure DevOps een build gestart en wordt er een versie uitgevoerd om de wijzigingen te implementeren. Bewaak de status van de build in het DevOps-start dashboard of in de browser met uw Azure DevOps-organisatie.

1. Wanneer de release is voltooid, vernieuwt u de toepassing om de wijzigingen te controleren.

## <a name="connect-to-azure-sql-database"></a>Verbinding maken met Azure SQL Database

U hebt de juiste machtigingen nodig om verbinding te maken met Azure SQL Database.

1. Selecteer in het DevOps-start dashboard **SQL database** om naar de beheer pagina voor SQL database te gaan.
   
1. Selecteer **Serverfirewall instellen** en vervolgens **IP van client toevoegen**. 

1. Selecteer **Opslaan**. Het IP-adres van de client heeft nu toegang tot de SQL Server Azure-resource.

1. Ga terug naar het deelvenster **SQL Database**. 

1. Selecteer rechts de servernaam om naar de configuratiepagina voor **SQL Server** te navigeren.

1. Selecteer **Wachtwoord opnieuw instellen**, voer een wachtwoord in voor de aanmeldgegevens van de SQL Server-beheerder en selecteer **Opslaan**. Zorg dat u dit wachtwoord bewaart voor gebruik verderop in deze zelfstudie.

    U kunt nu desgewenst client hulpprogramma's zoals SQL Server Management Studio of Visual Studio gebruiken om verbinding te maken met SQL Server en Azure SQL Database. Gebruik de eigenschap **Servernaam** om verbinding te maken.

    Als u de gebruikersnaam van de database niet hebt gewijzigd tijdens de eerste configuratie van het project in DevOps Projects, is uw gebruikersnaam het lokale gedeelte van uw e-mailadres. Als uw e-mail adres bijvoorbeeld *janjansen \@ Microsoft.com*is, is uw gebruikers naam *JANJANSEN*.

   > [!NOTE]
   > Als u uw wacht woord voor de SQL-aanmelding wijzigt, moet u het wacht woord wijzigen in de variabele release pijplijn, zoals wordt beschreven in de sectie [de cd-pipeline bekijken](#examine-the-cd-pipeline) .

## <a name="clean-up-resources"></a>Resources opschonen

Tijdens het testen kunt u voorkomen dat de kosten oplopen door resources op te schonen. Wanneer u deze niet meer nodig hebt, kunt u Azure SQL Database en gerelateerde resources die u in deze zelf studie hebt gemaakt, verwijderen. Als u dit wilt doen, gebruikt u de **verwijderings** functionaliteit op het DevOps starter-dash board.

> [!IMPORTANT]
> Met de volgende procedure worden resources permanent verwijderd. De functie *verwijderen* vernietigt de gegevens die door het project in DevOps starter zijn gemaakt in zowel Azure als Azure DevOps, en u kunt deze niet ophalen. Gebruik deze procedure pas nadat u de prompts zorgvuldig hebt gelezen.

1. Ga in het Azure Portal naar het DevOps starter-dash board.
2. Selecteer in de rechterbovenhoek **Verwijderen**. 
3. Selecteer **Ja** bij de prompt om de resources *definitief te verwijderen*.

## <a name="next-steps"></a>Volgende stappen

U kunt de build- en release-pijplijn desgewenst wijzigen in overeenstemming met de behoeften van uw team. U kunt dit CI/CD-patroon ook als een sjabloon voor uw andere pijplijnen gebruiken. In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Azure DevOps starter gebruiken om uw ASP.NET-app en Azure SQL Database code te implementeren
> * Azure DevOps en een Azure-abonnement configureren 
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * Wijzigingen doorvoeren in Azure Repos en automatisch implementeren naar Azure
> * Verbinding maken met Azure SQL Database 
> * Resources opschonen

Voor meer informatie over de CI/CD-pijplijn, zie:

> [!div class="nextstepaction"]
> [Een CD-pijplijn (continue implementatie) met meerdere fasen definiëren](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Video's

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]
