---
title: Taken voor de teamleider in het Team Data Science Process Team
description: Een gedetailleerde overzicht van de taken voor een teamlead in een Team Data Science Process team
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d099d7c233c3f4b5e65bfdb7d4b875a0e4098499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864278"
---
# <a name="tasks-for-the-team-lead-on-a-team-data-science-process-team"></a>Taken voor het team leiden in een Team Data Science Process team

In dit artikel worden de taken beschreven die een *teamlead* voltooit voor hun data science-team. Het doel van de teamleider is het opzetten van een samenwerkingsteamomgeving die standaardiseert op het [Team Data Science Process](overview.md) (TDSP). Het TDSP is ontworpen om samenwerking en teamlearning te verbeteren. 

De TDSP is een flexibele, iteratieve data science methodologie om efficiënt predictive analytics oplossingen en intelligente applicaties te leveren. Het proces destilleert de beste praktijken en structuren van Microsoft en de industrie.  Het doel is een succesvolle implementatie van data science initiatieven en het volledig realiseren van de voordelen van hun analytics programma's. Zie [Team Data Science Process rollen en taken](roles-tasks.md)voor een team dat zich standaard maakt voor een team dat zich op het TDSP standaardiseert.

Een teamlead beheert een team bestaande uit verschillende data scientists in de data science unit van een onderneming. Afhankelijk van de grootte en structuur van de data science-eenheid kunnen de [groepsmanager](group-manager-tasks.md) en de teamleider dezelfde persoon zijn of kunnen ze hun taken delegeren aan surrogaten. Maar de taken zelf veranderen niet. 

In het volgende diagram ziet u de werkstroom voor de taken die de teamlead voltooit om een teamomgeving in te stellen:

![Werkstroom voor taaktaken voor teamleiders](./media/team-lead-tasks/team-leads-1-creating-teams.png)

1. Maak een **teamproject** in de organisatie van de groep in Azure DevOps. 
  
1. Wijzig de naam van de standaardteamopslagplaats in **TeamUtilities**.
  
1. Maak een nieuwe **TeamTemplate-repository** in het teamproject. 
  
1. Importeer de inhoud van de **GroupUtilities-** en **GroupProjectTemplate-repositories** van de groep in de **TeamUtilities-** en **TeamTemplate-repositories.** 
  
1. Stel **beveiligingsbeheer** in door teamleden toe te voegen en hun machtigingen te configureren.
  
1. Maak indien nodig teamgegevens en analysebronnen:
   - Voeg teamspecifieke hulpprogramma's toe aan de **TeamUtilities-repository.** 
   - Maak **Azure-bestandsopslag** om gegevenselementen op te slaan die nuttig kunnen zijn voor het hele team. 
   - Monteer de Azure-bestandsopslag op de **Data Science Virtual Machine** (DSVM) van de teamleider en voeg er gegevenselementen aan toe.

De volgende zelfstudie loopt in detail door de stappen.

> [!NOTE] 
> In dit artikel wordt Azure DevOps en een DSVM gebruikt om een TDSP-teamomgeving in te stellen, omdat u TDSP bij Microsoft implementeren. Als uw team andere codehosting- of ontwikkelingsplatforms gebruikt, zijn de taken van de teamleider hetzelfde, maar de manier om deze te voltooien kan anders zijn.

## <a name="prerequisites"></a>Vereisten

In deze zelfstudie wordt ervan uitgegaan dat de volgende bronnen en machtigingen zijn ingesteld door uw [groepsmanager:](group-manager-tasks.md)

- De Azure **DevOps-organisatie** voor uw gegevenseenheid
- **GroupProjectTemplate-** en **GroupUtilities-repositories,** gevuld met de inhoud van de **ProjectTemplate-** en **Utilities-repositories** van het Microsoft TDSP-team
- Machtigingen voor uw organisatieaccount voor u om projecten en repositories voor uw team te maken

Als u repositories klonen en hun inhoud op uw lokale machine of DSVM wilt wijzigen, of Azure-bestandsopslag wilt instellen en deze wilt monteren op uw DSVM, hebt u het volgende nodig:

- Een Azure-abonnement.
- Git geïnstalleerd op uw machine. Als u een DSVM gebruikt, is Git vooraf geïnstalleerd. Anders zie de [bijlage platforms en gereedschappen](platforms-and-tools.md#appendix).
- Als u een DSVM wilt gebruiken, heeft de Windows- of Linux DSVM in Azure gemaakt en geconfigureerd. Zie de Data Science [Virtual Machine Documentation](/azure/machine-learning/data-science-virtual-machine/)voor meer informatie en instructies.
- Voor een Windows DSVM is [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) geïnstalleerd op uw machine. Schuif *in* het README.md-bestand omlaag naar de sectie **Downloaden en installeren** en selecteer het nieuwste **installatieprogramma.** Download de *.exe* installer van de installatiepagina en voer deze uit. 
- Voor een Linux DSVM is een SSH-openbare sleutel ingesteld op uw DSVM en toegevoegd in Azure DevOps. Zie de sectie **SSH public key maken** in de [bijlage platforms en hulpmiddelen](platforms-and-tools.md#appendix)voor meer informatie en instructies. 

## <a name="create-a-team-project-and-repositories"></a>Een teamproject en repositories maken

In deze sectie maakt u de volgende resources in de Azure DevOps-organisatie van uw groep:

- Het **MyTeam-project** in Azure DevOps
- De **TeamTemplate-opslagplaats**
- De **TeamUtilities** repository

De namen die zijn opgegeven voor de repositories en mappen in deze zelfstudie gaan ervan uit dat u een apart project wilt opzetten voor uw eigen team binnen uw grotere data science-organisatie. De hele groep kan er echter voor kiezen om te werken onder één project dat is gemaakt door de groepsbeheerder of de organisatiebeheerder. Vervolgens maken alle data science-teams repositories in het kader van dit ene project. Dit scenario kan geldig zijn voor:
- Een kleine data science-groep die niet meerdere data science-teams heeft. 
- Een grotere data science groep met meerdere data science teams die toch de samenwerking tussen teams wil optimaliseren met activiteiten zoals groepsniveau sprintplanning. 

Als teams ervoor kiezen om hun teamspecifieke repositories onder één groepsproject te hebben, moeten de teamleads de repositories maken met namen als * \<TeamName>Template* en * \<TeamName>Utilities*. Bijvoorbeeld: *TeamATemplate* en *TeamAUtilities*. 

In ieder geval moeten teamleads hun teamleden laten weten welke sjabloon- en hulpprogramma's moeten worden ingesteld en gekloond. Projectleads moeten de [projectleadtaken volgen voor een data science-team](project-lead-tasks.md) om projectrepositories te maken, of het nu gaat om afzonderlijke projecten of één project. 

### <a name="create-the-myteam-project"></a>Het MyTeam-project maken

Ga als lid van het project om een apart project voor uw team te maken:

1. Ga in uw webbrowser naar de startpagina van de Azure DevOps-organisatie van uw groep op *\//\<URL-https: servernaam\<>/ organisatienaam>* en selecteer Nieuw **project**. 
   
   ![Nieuw project selecteren](./media/team-lead-tasks/team-leads-2-create-new-team.png)
   
1. Voer in het dialoogvenster **Project maken** uw teamnaam in, zoals *MyTeam,* onder **Projectnaam**en selecteer **Geavanceerd**. 
   
1. Selecteer **onder Versiebeheer** **Git**en selecteer onder **Werkitemproces**de optie **Flexibel**. Selecteer vervolgens **Maken**. 
   
   ![Project maken](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)
   
De pagina **Teamprojectoverzicht** wordt geopend met pagina-URL *\//\<https: servernaam>/\<organisatienaam>/\<teamnaam>*.

### <a name="rename-the-myteam-default-repository-to-teamutilities"></a>De naam van de standaardopslagplaats van MyTeam wijzigen in TeamUtilities

1. Selecteer **MyTeam** **Repos** **Summary** **.** 
   
   ![Repo's selecteren](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)
   
1. Selecteer **MyTeam** op de myteamrepo-pagina de **MyTeam-opslagplaats** boven aan de pagina en selecteer **vervolgens Opslagplaatsen beheren** in de vervolgkeuzelijst. 
   
   ![Repositories beheren selecteren](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)
1. Selecteer op de pagina **Projectinstellingen** de **...** naast de **MyTeam-opslagplaats** en selecteer **Vervolgens De naam van de opslagplaats wijzigen**. 
   
   ![De naam van de opslagplaats wijzigen](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)
   
1. Voer *teamutiliteit en*selecteer Vervolgens Naam wijzigen in de pop-up **Van de MyTeam-repository** en selecteer **Vervolgens Naam wijzigen**. 

### <a name="create-the-teamtemplate-repository"></a>De TeamTemplate-opslagplaats maken

1. Selecteer op de pagina **Projectinstellingen** de optie **Nieuwe opslagplaats.** 
   
   ![Nieuwe opslagplaats selecteren](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)
   
   Of selecteer **Repo's** in de linkernavigatie van de pagina **Overzicht** **van het MyTeam-project,** selecteer een opslagplaats boven aan de pagina en selecteer Vervolgens Nieuwe **opslagplaats** in de vervolgkeuzelijst.
   
1. Controleer in het dialoogvenster **Een nieuwe opslagplaats maken** of **Git** is geselecteerd onder **Type**. Voer *TeamTemplate* in onder **de naam Repository**en selecteer Vervolgens **Maken**.
   
   ![Repository maken](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)
   
1. Controleer of u de twee repositories **TeamUtilities** en **TeamTemplate** op uw pagina met projectinstellingen zien. 
   
   ![Twee teamrepositories](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)

### <a name="import-the-contents-of-the-group-common-repositories"></a>De inhoud van de algemene groepsopslagplaatsen importeren

Ga als lid van het team om uw teamrepositories te vullen met de inhoud van de algemene groepsopslagplaatsen die zijn ingesteld door uw groepsmanager:

1. Selecteer **Repos** op de linkernavigatie op uw startpagina **van MyTeam-project.** Als u een bericht ontvangt dat de **MyTeam-sjabloon** niet is gevonden, selecteert u de koppeling in **Anders, navigeert u naar de standaardteamsjabloon-opslagplaats.** 
   
   De **standaardTeamTemplate-opslagplaats** wordt geopend. 
   
1. **Selecteer**Importeren op de pagina **TeamTemplate is leeg** . 
   
   ![Selecteer Importeren](./media/team-lead-tasks/import-repo.png)
   
1. Selecteer **Git** als **brontype**in het dialoogvenster **Een Git-opslagplaats importeren** en voer de URL in voor de algemene sjabloonopslagplaats van uw groep onder **Kloon-URL**. De URL is *\//\<https:\<servernaam>/organisatienaam>/_git/\<repository naam>*. Bijvoorbeeld: *https:\//dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupProjectTemplate*. 
   
1. Selecteer **Importeren**. De inhoud van de groepssjabloonopslagplaats wordt geïmporteerd in de opslagplaats voor teamsjablonen. 
   
   ![Groep gemeenschappelijke sjabloonopslagplaats importeren](./media/team-lead-tasks/import-repo-2.png)
   
1. Ga boven aan de **Repos-pagina** van uw project naar beneden en selecteer de **TeamUtilities-opslagplaats.**
   
1. Herhaal het importproces om de inhoud van de gemeenschappelijke hulpprogramma's van uw groep te importeren, bijvoorbeeld *GroupUtilities,* in uw **TeamUtilities-opslagplaats.** 
   
Elk van uw twee teamrepositories bevat nu de bestanden uit de bijbehorende groepsgemeenschappelijke repository. 

### <a name="customize-the-contents-of-the-team-repositories"></a>De inhoud van de teamrepositories aanpassen

Als je de inhoud van je teamrepositories wilt aanpassen aan de specifieke behoeften van je team, kun je dat nu doen. U bestanden wijzigen, de directorystructuur wijzigen of bestanden en mappen toevoegen.

Ga als het gaat om het wijzigen, uploaden of maken van bestanden of mappen rechtstreeks in Azure DevOps:

1. Selecteer **Repos**op de pagina **Overzicht** **van het MyTeam-project** . 
   
1. Selecteer boven aan de pagina de opslagplaats die u wilt aanpassen.

1. Navigeer in de repo-mapstructuur naar de map of het bestand dat u wilt wijzigen. 
   
   - Als u nieuwe mappen of bestanden wilt maken, selecteert u de pijl naast **Nieuw**. 
     
     ![Nieuw bestand maken](./media/team-lead-tasks/new-file.png)
     
   - Als u bestanden wilt uploaden, selecteert u **Bestand(en uploaden)**. 
     
     ![Bestanden uploaden](./media/team-lead-tasks/upload-files.png)
     
   - Als u bestaande bestanden wilt bewerken, navigeert u naar het bestand en selecteert u **Bewerken**. 
     
     ![Een bestand bewerken](./media/team-lead-tasks/edit-file.png)
     
1. Nadat u bestanden hebt toegevoegd of bewerkt, selecteert u **Vastleggen**.
   
   ![Wijzigingen vastleggen](./media/team-lead-tasks/commit.png)

Om met repositories op je lokale machine of DSVM te werken, kopieer of *kloon* je de repositories eerst naar je lokale machine en verbind en push je je wijzigingen naar de shared team repositories, 

Ga als het gaat om opslagplaatsen te klonen:

1. Selecteer op de pagina **Overzicht van** **het MyTeam-project** **Repos**en selecteer boven aan de pagina de opslagplaats die u wilt klonen.
   
1. Selecteer **kloon** rechtsboven op de repo-pagina.
   
1. Selecteer in het dialoogvenster **Kloonopslagplaats** onder **Opdrachtregel** **HTTPS** voor een HTTP-verbinding of **SSH** voor een SSH-verbinding en kopieer de kloon-URL naar het klembord.
   
   ![URL van kloon kopiëren](./media/team-lead-tasks/clone.png)
   
1. Maak op uw lokale machine de volgende mappen:
   
   - Voor Windows: **C:\GitRepos\MyTeam**
   - Voor Linux, **$home/GitRepos/MyTeam** 
   
1. Wijzigen in de map die u hebt gemaakt.
   
1. Voer in Git Bash `git clone <clone URL>`de \<opdracht uit, waarbij de URL van de kloon> de URL is die u hebt gekopieerd uit het dialoogvenster **Kloon.**
   
   Gebruik bijvoorbeeld een van de volgende opdrachten om de **TeamUtilities-repository** te klonen naar de *MyTeam-map* op uw lokale machine. 
   
   **HTTPS-verbinding:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH-verbinding:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```

Nadat je de gewenste wijzigingen hebt aangebracht in de lokale kloon van je repository, moet je de wijzigingen in de gedeelde teamrepositories vastleggen en pushen. 

Voer de volgende Git Bash-opdrachten uit van uw lokale **GitRepos\MyTeam\TeamTemplate** of **GitRepos\MyTeam\TeamUtilities-map.**

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Als dit de eerste keer is dat u zich verbindt aan een Git-opslagplaats, moet `git commit` u mogelijk globale parameters *user.name* en *user.email* configureren voordat u de opdracht uitvoert. Voer de volgende twee opdrachten uit:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Als u zich verbindt aan verschillende Git-repositories, gebruikt u voor alle andere bestanden dezelfde naam en hetzelfde e-mailadres. Het gebruik van dezelfde naam en e-mailadres is handig bij het bouwen van Power BI-dashboards om uw Git-activiteiten in meerdere opslagplaatsen bij te houden.

## <a name="add-team-members-and-configure-permissions"></a>Teamleden toevoegen en machtigingen configureren

Ga als lid naar het team:

1. Selecteer in Azure DevOps op de startpagina van **het MyTeam-project** **Project project instellingen** op de linkernavigatie. 
   
1. Selecteer in de navigatie **Projectinstellingen** links **teams**en selecteer vervolgens op de pagina **Teams** het **Team MijnTeam**. 
   
   ![Teams configureren](./media/team-lead-tasks/teams.png)
   
1. Selecteer op de pagina **Teamprofiel** de optie **Toevoegen**.
   
   ![Toevoegen aan MyTeam Team](./media/team-lead-tasks/add-to-team.png)
   
1. Zoek **in** het dialoogvenster Gebruikers en groepen toevoegen naar en selecteer leden die u aan de groep wilt toevoegen en selecteer **wijzigingen opslaan**. 
   
   ![Gebruikers en groepen toevoegen](./media/team-lead-tasks/add-users.png)
   

Machtigingen voor teamleden configureren:

1. Selecteer **Machtigingen**in de navigatie **projectinstellingen** links . 
   
1. Selecteer **op** de pagina Machtigingen de groep waaraan u leden wilt toevoegen. 
   
1. Selecteer op de pagina voor die groep **Leden**en selecteer **Vervolgens Toevoegen**. 
   
1. Zoek **in** de pop-up Leden uitnodigen naar en selecteer leden die aan de groep willen toevoegen en selecteer **Vervolgens Opslaan**. 
   
   ![Machtigingen verlenen aan leden](./media/team-lead-tasks/grant-permissions.png)

## <a name="create-team-data-and-analytics-resources"></a>Teamgegevens en analysebronnen maken

Deze stap is optioneel, maar het delen van gegevens en analysebronnen met uw hele team heeft prestatie- en kostenvoordelen. Teamleden kunnen hun projecten uitvoeren op basis van de gedeelde resources, besparen op budgetten en efficiënter samenwerken. U Azure-bestandsopslag maken en deze op uw DSVM monteren om te delen met teamleden. 

Zie [Platforms en hulpprogramma's](platforms-and-tools.md)voor informatie over het delen van andere bronnen met uw team, zoals Azure HDInsight Spark-clusters. Dat onderwerp biedt richtlijnen vanuit een data science perspectief op het selecteren van bronnen die geschikt zijn voor uw behoeften, en links naar productpagina's en andere relevante en nuttige tutorials.

>[!NOTE]
> Zorg ervoor dat uw Azure-brongroep, opslagaccount en DSVM allemaal worden gehost in dezelfde Azure-regio om te voorkomen dat gegevens worden verzonden over datacenters die traag en duur kunnen zijn. 

### <a name="create-azure-file-storage"></a>Azure-bestandsopslag maken

1. Voer het volgende script uit om Azure-bestandsopslag te maken voor gegevenselementen die nuttig zijn voor uw hele team. Het script vraagt u om uw Azure-abonnementsgegevens, dus heb die klaar om in te voeren. 

   - Voer het script uit op een Windows-machine via de powershell-opdrachtprompt:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
     .\CreateFileShare.ps1
     ```
     
   - Voer het script uit vanaf de Linux-schil op een Linux-machine:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
     bash CreateFileShare.sh
     ```
   
1. Meld u aan bij uw Microsoft Azure-account wanneer u daarom wordt gevraagd en selecteer het abonnement dat u wilt gebruiken.
   
1. Selecteer het opslagaccount dat u wilt gebruiken of maak een nieuw account onder uw geselecteerde abonnement. U kleine tekens, getallen en koppeltekens gebruiken voor de naam Azure-bestandsopslag.
   
1. Als u de opslag wilt zo eenvoudig maken, drukt u op Enter of Enter *Y* om de Azure-bestandsopslaggegevens op te slaan in een tekstbestand in uw huidige map. U dit tekstbestand inchecken in uw **TeamTemplate-opslagplaats,** idealiter onder **Documenten\DataDictionaries,** zodat alle projecten in uw team er toegang toe hebben. U hebt ook de bestandsgegevens nodig om uw Azure-bestandsopslag in de volgende sectie aan uw Azure DSVM te monteren. 
   
### <a name="mount-azure-file-storage-on-your-local-machine-or-dsvm"></a>Azure-bestandsopslag monteren op uw lokale machine of DSVM

1. Als u uw Azure-bestandsopslag wilt monteren op uw lokale machine of DSVM, gebruikt u het volgende script.
   
   - Voer het script uit op een Windows-machine via de powershell-opdrachtprompt:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
     .\AttachFileShare.ps1
     ```
     
   - Voer het script uit vanaf de Linux-schil op een Linux-machine:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
     bash AttachFileShare.sh
     ```
   
1. Druk op Enter of Enter *Y* om door te gaan als u in de vorige stap een Azure-bestandsopslaggegevensbestand hebt opgeslagen. Voer het volledige pad en de naam in van het bestand dat u hebt gemaakt. 
   
   Als u geen Azure-bestandsopslaggegevensbestand hebt, voert u *n*in en volgt u de instructies om uw abonnement, Azure-opslagaccount en Azure-bestandsopslaggegevens in te voeren.
   
1. Voer de naam in van een lokaal of TDSP-station om de bestandsshare op te monteren. Op het scherm wordt een lijst met bestaande stationsnamen weergegeven. Geef een stationnaam op die nog niet bestaat.
   
1. Controleer of het nieuwe station en de opslag op uw machine zijn gemonteerd.

## <a name="next-steps"></a>Volgende stappen

Hier vindt u links naar gedetailleerde beschrijvingen van de andere rollen en taken die zijn gedefinieerd door het Team Data Science-proces:

- [Group Manager taken voor een data science team](group-manager-tasks.md)
- [Projectleadtaken voor een data science-team](project-lead-tasks.md)
- [Project individuele bijdragertaken voor een data science-team](project-ic-tasks.md)
