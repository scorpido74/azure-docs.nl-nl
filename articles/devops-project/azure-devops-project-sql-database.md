---
title: 'Zelfstudie: uw ASP.NET-app en Azure SQL Database-code implementeren met behulp van Azure DevOps Projects'
description: Met DevOps Projects kunt u eenvoudig aan de slag met Azure. Met DevOps Projects kunt u in slechts enkele stappen een ASP.NET-app en Azure SQL Database-code implementeren.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 560b16a40d73d07df6c9f446c7196cd930e2274e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "71971490"
---
# <a name="tutorial-deploy-your-aspnet-app-and-azure-sql-database-code-by-using-azure-devops-projects"></a>Zelfstudie: uw ASP.NET-app en Azure SQL Database-code implementeren met behulp van Azure DevOps Projects

Azure DevOps Projects biedt een vereenvoudigde ervaring waar u uw bestaande code en Git-opslagplaats gebruikt of een voorbeeldtoepassing kiest voor het maken van een pijplijn voor CI (Continue integratie) en CD (Continue levering) naar Azure. 

In DevOps Projects gebeurt ook het volgende:
* Er worden automatisch Azure-resources gemaakt, zoals een Azure SQL-database.
* Er wordt een release-pijplijn gemaakt en geconfigureerd in Azure Pipelines die een build-pijplijn voor CI bevat.
* Er wordt een release-pijplijn voor CD ingesteld. 
* Er wordt een Azure Application Insights-resource gemaakt voor de bewaking.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Azure DevOps Projects gebruiken om uw ASP.NET-app en Azure SQL Database-code te implementeren
> * Azure DevOps en een Azure-abonnement configureren 
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * Wijzigingen doorvoeren in Azure Repos en automatisch implementeren naar Azure
> * Verbinding maken met de Azure SQL-database 
> * Resources opschonen

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. U kunt er een gratis krijgen via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-a-project-in-devops-projects-for-an-aspnet-app-and-an-azure-sql-database"></a>Een project in DevOps Projects maken voor een ASP.NET-app en een Azure SQL-database

In DevOps Projects wordt een CI/CD-pijplijn gemaakt in Azure Pipelines. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. Het DevOps Projects maakt ook Azure-resources, zoals een Azure SQL-database, in het Azure-abonnement van uw keuze.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **Een resource maken** in het linkerdeelvenster.

1. Typ in het zoekvak **DevOps Projects** en selecteer **Maken**.

    ![Het DevOps Projects-dashboard](_img/azure-devops-project-github/fullbrowser.png)

1. Selecteer **.NET** en vervolgens **Volgende**.

1. Selecteer bij **Een toepassingsframework kiezen** de optie **ASP.NET**.

1. Selecteer **Een database toevoegen** en selecteer **Volgende**.  
    Het toepassingsframework dat u in een vorige stap hebt gekozen, bepaalt welk type implementatiedoel hier beschikbaar is voor de Azure-service. 
    
1. Selecteer **Volgende**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps en een Azure-abonnement configureren

1. Maak een nieuwe Azure DevOps-organisatie of selecteer een bestaande organisatie. 

1. Voer een naam in voor het Azure DevOps-project. 

1. Selecteer uw Azure-abonnementsservices.  
    U kunt **Wijzigen** selecteren als u aanvullende configuratie-instellingen van Azure wilt bekijken en de gebruikersnaam in de sectie **Informatie over de aanmeldingsgegevens voor de databaseserver** wilt identificeren. Bewaar de gebruikersnaam voor toekomstige stappen in deze zelfstudie. Als u deze optionele stap uitvoert, sluit u het gebied met de Azure-configuratie voordat u **Gereed** selecteert.
 
1. Selecteer **Done**.  
    Na enkele minuten is het proces voltooid en wordt het dashboard van DevOps Projects geopend in de Azure-portal. U kunt ook rechtstreeks vanuit **Alle resources** in de Azure-portal naar het dashboard navigeren. Selecteer aan de rechterkant **Bladeren** om uw actieve toepassing weer te geven.
    
## <a name="examine-the-ci-pipeline"></a>De CI-pijplijn onderzoeken

In DevOps Projects wordt automatisch een volledige CI/CD-pijplijn geconfigureerd in Azure Repos. U kunt de pijplijn verkennen en aanpassen. Ga als volgt te werk om vertrouwd te raken met de Azure DevOps-build-pijplijn:

1. Selecteer boven aan het dashboard DevOps Projects de optie **Pijplijnen bouwen**.  
    Op een tabblad in de browser wordt de build-pijplijn voor het nieuwe project weergegeven.

1. Wijs het veld **Status** aan en selecteer het beletselteken (...).  
    Er wordt een menu met verschillende opties weergegeven, bijvoorbeeld om een nieuwe build in de wachtrij te plaatsen, een build te onderbreken of de build-pijplijn te bewerken.

1. Selecteer **Bewerken**.

1. In dit deelvenster kunt u de verschillende taken voor uw build-pijplijn onderzoeken.  
    De build voert verschillende taken uit, zoals het ophalen van bronnen uit de Git-opslagplaats, het herstellen van afhankelijkheden en het publiceren van uitvoer die wordt gebruikt voor implementaties.

1. Selecteer bovenaan de build-pijplijn de naam van de build-pijplijn.

1. Wijzig de naam van de build-pijplijn in een meer beschrijvende naam. Selecteer **Opslaan en wachtrij** en selecteer vervolgens **Opslaan**.

1. Selecteer onder de naam van de build-pipeline de optie **Geschiedenis**.  
    In dit deelvenster ziet u een audittrail van recente wijzigingen voor de build. Azure Pipelines houdt alle wijzigingen in de build-pijplijn bij en biedt de mogelijkheid om versies te vergelijken.

1. Selecteer **Triggers**.  
    In DevOps Projects wordt automatisch een CI-trigger gemaakt en met elke doorvoering naar de opslagplaats wordt een nieuwe build gestart. Desgewenst kunt u kiezen of u vertakkingen van het CI-proces wilt opnemen of uitsluiten.

1. Selecteer **Retentie**.  
    Afhankelijk van het scenario kunt u beleidsregels opgeven om een bepaald aantal builds te behouden of te verwijderen.

## <a name="examine-the-cd-pipeline"></a>De CD-pijplijn onderzoeken

In DevOps Projects worden automatisch de benodigde stappen gemaakt en geconfigureerd om vanuit uw Azure DevOps-organisatie te implementeren naar uw Azure-abonnement. Deze stappen omvatten het configureren van een Azure-serviceverbinding om Azure DevOps te verifiëren bij uw Azure-abonnement. Er wordt ook automatisch een CD-pijplijn gemaakt die de CD aan de virtuele machine van Azure verstrekt. Voor meer informatie over de CD-pijplijn van Azure DevOps doet u het volgende:

1. Selecteer **Build en release** en selecteer vervolgens **Releases**.  
    In DevOps Projects wordt een release-pijplijn gemaakt om implementaties in Azure te beheren.

1. Selecteer het beletselteken (...) naast de release-pijplijn en selecteer **Bewerken**.  
    De release-pijplijn bevat een *pijplijn* die het releaseproces definieert.

1. Onder **Artefacten** selecteert u **Neerzetten**.  
    Met de build-pijplijn die u in de vorige stappen hebt onderzocht, wordt de uitvoer geproduceerd die wordt gebruikt voor het artefact. 

1. Selecteer **Continue implementatietrigger** rechts van het pictogram **Neerzetten**.  
    Deze release-pijplijn heeft een ingeschakelde CD-trigger die een implementatie uitvoert telkens wanneer een nieuw build-artefact beschikbaar is. U kunt de trigger eventueel uitschakelen zodat de implementaties handmatig moeten worden uitgevoerd. 

    DevOps Projects stelt een willekeurig SQL-wachtwoord in en gebruikt dit voor de release-pijplijn.
    
1. Selecteer aan de linkerkant **Variabelen**. 

   > [!NOTE]
   > Voer de volgende stap alleen uit als u het SQL Server-wachtwoord hebt gewijzigd. Er is één wachtwoordvariabele.
  
1. Selecteer het hangslotpictogram naast het vak **Waarde**, voer het nieuwe wachtwoord in en selecteer **Opslaan**.

1. Selecteer aan de linkerkant **Taken** en selecteer uw omgeving.  
    Taken zijn de activiteiten die door het implementatieproces worden uitgevoerd. Ze zijn gegroepeerd in fasen. Deze release-pijplijn heeft één fase. Deze bevat een taak voor een *Azure App Service-implementatie* en een voor een *Azure SQL Database-implementatie*.

1. Selecteer de taak *Azure SQL uitvoeren* en controleer de verschillende eigenschappen die worden gebruikt voor de SQL-implementatie.  
    Onder **Implementatiepakket** ziet u dat de taak gebruikmaakt van een *SQL DACPAC-bestand*.

1. Selecteer aan de rechterkant **Versies weergeven** om een versiegeschiedenis weer te geven.

1. Selecteer het beletselteken (...) naast een versie en selecteer **Openen**.  
     U kunt verschillende menu's verkennen, zoals een versieoverzicht, gekoppelde werkitems en tests.

1. Selecteer **Doorvoeringen**.  
     In deze weergave worden de codedoorvoeringen weergegeven die zijn gekoppeld aan deze implementatie. Vergelijk versies om de doorvoerverschillen tussen implementaties weer te geven.

1. Selecteer **Logboeken**.  
     De logboeken bevatten nuttige informatie over het implementatieproces. U kunt beide weergeven tijdens en na de implementaties.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Wijzigingen doorvoeren in Azure Repos en automatisch implementeren naar Azure 

 > [!NOTE]
 > Met de volgende procedure wordt de CI/CD-pijplijn getest met een eenvoudige wijziging in de tekst. Als u het SQL-implementatieproces wilt testen, kunt u een SQL Server Schema-wijziging aanbrengen in de tabel.

U bent nu klaar om met een team samen te werken aan de app met behulp van een CI/CD-proces waarmee automatisch uw meest recente werk op uw website wordt geïmplementeerd. Bij elke wijziging in de Git-opslagplaats wordt een build gestart in Azure DevOps, en met een CD-pijplijn wordt een implementatie uitgevoerd in Azure. Volg de procedure in deze sectie of gebruik een andere methode om wijzigingen in uw opslagplaats door te voeren. De codewijzigingen beginnen het CI/CD-proces en implementeren de wijzigingen automatisch in Azure.

1. Selecteer in het linkerdeelvenster **Code** en ga naar uw opslagplaats.

1. Ga naar de map *SampleWebApplication\Views\Home*, selecteer het beletselteken (...) naast het bestand *Index.cshtml* en kies **Bewerken**. 

1. Breng een wijziging aan in het bestand, bijvoorbeeld door wat tekst toe te voegen in een van de div-tags. 

1. Selecteer in de rechterbovenhoek **Doorvoeren** en selecteer vervolgens nogmaals **Doorvoeren** om de wijziging te pushen.  
    Na een paar seconden wordt er in Azure DevOps een build gestart en wordt er een versie uitgevoerd om de wijzigingen te implementeren. Bewaak de buildstatus op het DevOps Projects-dashboard of in de browser met uw Azure DevOps-organisatie.

1. Wanneer de release is voltooid, vernieuwt u de toepassing om de wijzigingen te controleren.

## <a name="connect-to-the-azure-sql-database"></a>Verbinding maken met de Azure SQL-database

U hebt de juiste machtigingen nodig om verbinding te kunnen maken met de Azure SQL-database.

1. Selecteer **SQL Database** in het dashboard van DevOps Projects om naar de beheerpagina voor de SQL-database te gaan.
   
1. Selecteer **Serverfirewall instellen** en vervolgens **IP van client toevoegen**. 

1. Selecteer **Opslaan**.  
    Het IP-adres van de client heeft nu toegang tot de SQL Server Azure-resource.

1. Ga terug naar het deelvenster **SQL Database**. 

1. Selecteer rechts de servernaam om naar de configuratiepagina voor **SQL Server** te navigeren.

1. Selecteer **Wachtwoord opnieuw instellen**, voer een wachtwoord in voor de aanmeldgegevens van de SQL Server-beheerder en selecteer **Opslaan**.  
    Zorg dat u dit wachtwoord bewaart voor gebruik verderop in deze zelfstudie.

    U kunt nu desgewenst gebruikmaken van clienthulpprogramma's zoals SQL Server Management Studio of Visual Studio om verbinding te maken met SQL Server en de Azure SQL-database. Gebruik de eigenschap **Servernaam** om verbinding te maken.

    Als u de gebruikersnaam van de database niet hebt gewijzigd tijdens de eerste configuratie van het project in DevOps Projects, is uw gebruikersnaam het lokale gedeelte van uw e-mailadres. Als uw e-mailadres bijvoorbeeld *johndoe-microsoft.com\@* is, is uw gebruikersnaam *johndoe.*

   > [!NOTE]
   > Als u uw wachtwoord voor de SQL-aanmelding wijzigt, moet u het wachtwoord in de variabele van de release-pijplijn wijzigen zoals staat beschreven in de sectie De CD-pijplijn onderzoeken.

## <a name="clean-up-resources"></a>Resources opschonen

Tijdens het testen kunt u voorkomen dat de kosten oplopen door resources op te schonen. U kunt de Azure SQL-database en de gerelateerde resources die u in deze zelfstudie hebt gemaakt, verwijderen wanneer u ze niet meer nodig hebt. Hiertoe gebruikt u de functionaliteit **Verwijderen** op het dashboard van DevOps Projects.

> [!IMPORTANT]
> Met de volgende procedure worden resources permanent verwijderd. Met de functionaliteit *Verwijderen* verwijdert u de gegevens die door het project in DevOps Projects zijn gemaakt in zowel Azure als Azure DevOps. Als ze zijn verwijderd, kunt u ze niet meer terughalen. Gebruik deze procedure pas nadat u de prompts zorgvuldig hebt gelezen.

1. Ga in de Azure-portal naar het dashboard van DevOps Projects.
2. Selecteer in de rechterbovenhoek **Verwijderen**. 
3. Selecteer **Ja** bij de prompt om de resources *definitief te verwijderen*.

## <a name="next-steps"></a>Volgende stappen

U kunt de build- en release-pijplijn desgewenst wijzigen in overeenstemming met de behoeften van uw team. U kunt dit CI/CD-patroon ook als een sjabloon voor uw andere pijplijnen gebruiken. In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Azure DevOps Projects gebruiken om uw ASP.NET-app en Azure SQL Database-code te implementeren
> * Azure DevOps en een Azure-abonnement configureren 
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * Wijzigingen doorvoeren in Azure Repos en automatisch implementeren naar Azure
> * Verbinding maken met de Azure SQL-database 
> * Resources opschonen

Voor meer informatie over de CI/CD-pijplijn, zie:

> [!div class="nextstepaction"]
> [Een CD-pijplijn (continue implementatie) met meerdere fasen definiëren](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Video's

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]
