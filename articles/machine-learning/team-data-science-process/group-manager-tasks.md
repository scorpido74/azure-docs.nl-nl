---
title: Team Data Science Process groepsmanager taken
description: Volg deze gedetailleerde overzicht van de taken die een groepsmanager voltooit op een data science team project.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ec7f4242e5046e90fdf0eb8c6c0579f402e4f55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721350"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Team Data Science Process groepsmanager taken

In dit artikel worden de taken beschreven die een *groepsmanager* voltooit voor een data science-organisatie. De groepsmanager beheert de volledige data science-eenheid in een onderneming. Een data science-eenheid kan meerdere teams hebben, die elk werken aan veel data science-projecten in verschillende bedrijfsverticalen. Het doel van de groepsmanager is het opzetten van een samenwerkingsgroepomgeving die standaardiseert op het [Team Data Science Process](overview.md) (TDSP). Zie [Team Data Science Process rollen en taken](roles-tasks.md)voor een overzicht van alle personeelsrollen en bijbehorende taken die worden uitgevoerd door een data science-team dat standaard is op het TDSP.

In het volgende diagram worden de zes hoofdgroepsbeheertaken weergegeven. Groepsmanagers kunnen hun taken delegeren aan surrogaten, maar de taken die aan de rol zijn gekoppeld, veranderen niet.

![Groepsmanagertaken](./media/group-manager-tasks/tdsp-group-manager.png)

1. Stel een **Azure DevOps-organisatie** in voor de groep.
2. Maak het standaard **GroupCommon-project** in de Azure DevOps-organisatie.
3. Maak de **GroupProjectTemplate-opslagplaats** in Azure Repos.
4. Maak de **GroupUtilities-opslagplaats** in Azure Repos.
5. Importeer de inhoud van de **ProjectTemplate-** en **Utilities-repositories** van het Microsoft TDSP-team in de algemene opslagplaatsen van de groep.
6. Stel **lidmaatschapen** en **machtigingen** in voor teamleden om toegang te krijgen tot de groep.

De volgende zelfstudie loopt in detail door de stappen. 

> [!NOTE] 
> In dit artikel wordt Azure DevOps gebruikt om een TDSP-groepsomgeving in te stellen, omdat u TDSP bij Microsoft implementeren. Als uw groep andere codehosting- of ontwikkelingsplatforms gebruikt, zijn de taken van de Groepsmanager hetzelfde, maar de manier om deze te voltooien kan anders zijn.

## <a name="create-an-organization-and-project-in-azure-devops"></a>Een organisatie en project maken in Azure DevOps

1. Ga naar [visualstudio.microsoft.com,](https://visualstudio.microsoft.com)selecteer **Aanmelden** rechtsboven en meld u aan bij uw Microsoft-account. 
   
   ![Aanmelden bij uw Microsoft-account](./media/group-manager-tasks/signinvs.png)
   
   Als u geen Microsoft-account hebt, selecteert u **Nu aanmelden,** maakt u een Microsoft-account en meldt u zich aan met dit account. Als uw organisatie een Visual Studio-abonnement heeft, meldt u zich aan met de referenties voor dat abonnement.
   
1. Nadat u zich hebt aangemeld, rechtsboven op de azure devOps-pagina, selecteert u **Nieuwe organisatie maken**.
   
   ![Nieuwe organisatie maken](./media/group-manager-tasks/create-organization.png)
   
1. Als u wordt gevraagd akkoord te gaan met de servicevoorwaarden, de privacyverklaring en de gedragscode, selecteert u **Doorgaan**.
   
1. Geef in het aanmeldingsdialoogvenster een naam aan uw Azure DevOps-organisatie en accepteer de toewijzing van het hostgebied, of laat een andere regio vallen en selecteren. Selecteer vervolgens **Doorgaan**. 

1. Voer *GroupCommon*in en selecteer Vervolgens Project **maken**onder **Een project maken om aan de slag te**gaan . 
   
   ![Project maken](./media/group-manager-tasks/create-project.png)

De pagina **Groepsalgemeen** **projectoverzicht** wordt geopend. De pagina-URL is *\//\<https:\<servernaam>/ organisatienaam>/GroupCommon*.

![Pagina Projectoverzicht](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>De algemene groepsopslagplaatsen instellen

Azure Repos host de volgende typen opslagplaatsen voor uw groep:

- **Gemeenschappelijke repositories voor groepen**: Repositories voor algemene doeleinden die meerdere teams binnen een data science-eenheid kunnen gebruiken voor veel data science-projecten. 
- **Team repositories**: Repositories voor specifieke teams binnen een data science unit. Deze repositories zijn specifiek voor de behoeften van een team en kunnen worden gebruikt voor meerdere projecten binnen dat team, maar zijn niet algemeen genoeg om te worden gebruikt in meerdere teams binnen een data science-eenheid.
- **Projectrepositories**: Repositories voor specifieke projecten. Dergelijke repositories zijn mogelijk niet algemeen genoeg voor meerdere projecten binnen een team of voor andere teams in een data science-eenheid.

Als u de algemene groepsopslagplaatsen in uw project wilt instellen, gaat u als: 
- De naam van de **standaardgroep GroupCommon** wijzigen in **GroupProjectTemplate**
- Een nieuwe **GroupUtilities-opslagplaats** maken

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>De naam van de standaardprojectopslagplaats wijzigen in GroupProjectTemplate

De naam van de **standaardgroep groupcommon-projectrepository** wijzigen in **GroupProjectTemplate:**

1. Selecteer **Repo's**op de pagina **Groepsalgemeen** **projectoverzicht** . Met deze actie gaat u naar de **standaardgroupcommon-opslagplaats** van het GroupCommon-project, dat momenteel leeg is.
   
1. Laat boven aan de pagina de pijl naast **GroupCommon** vallen en selecteer **Opslagplaatsen beheren**.
   
   ![Repositories beheren](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. Selecteer op de pagina **Projectinstellingen** de **...** naast **GroupCommon**en selecteer **Vervolgens De naam van de opslagplaats wijzigen**. 
   
   ![Selecteer... en selecteer vervolgens De naam van de opslagplaats wijzigen](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. Voer in de pop-up *GroepProjectSjabloon*wijzigen in de pop-up **GroupCommon-repository** en selecteer **Vervolgens Naam wijzigen**. 
   
   ![Naamvan de opslagplaats wijzigen](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>De GroupUtilities-opslagplaats maken

Ga als u de **GroupUtilities-opslagplaats** maken:

1. Selecteer **Repo's**op de pagina **Groepsalgemeen** **projectoverzicht** . 
   
1. Laat boven aan de pagina de pijl naast **GroupProjectTemplate** vallen en selecteer **Nieuwe opslagplaats**.
   
   ![Nieuwe opslagplaats selecteren](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. Selecteer **Git** als **type**in het dialoogvenster **Een nieuwe opslagplaats maken,** voer *GroupUtilities* in als **de naam Repository**en selecteer **Vervolgens Maken**.
   
   ![GroupUtilities-opslagplaats maken](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. Selecteer op de pagina **Projectinstellingen** de optie **Repositories** onder **Repos** in de linkernavigatie om de twee groepsopslagplaatsen te bekijken: **GroupProjectTemplate** en **GroupUtilities**.
   
   ![Twee groepsopslagplaatsen](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>De Microsoft TDSP-teamrepositories importeren

In dit deel van de zelfstudie importeert u de inhoud van de **ProjectTemplate-** en **Utilities-repositories** die door het Microsoft TDSP-team worden beheerd, in uw **GroupProjectTemplate-** en **GroupUtilities-repositories.** 

Ga als lid van het TDSP-team:

1. Selecteer **Repo's** in de linkernavigatie op de startpagina **van GroupCommon-project.** De standaard **GroupProjectTemplate** repo wordt geopend. 
   
1. **Selecteer**Importeren op de pagina **GroupProjectTemplate is leeg** . 
   
   ![Selecteer Importeren](./media/group-manager-tasks/import-repo.png)
   
1. Selecteer **Git** als **brontype**en voer https in in het dialoogvenster **Een Git-opslagplaats** importeren en voer *https in:\//github.com/Azure/Azure-TDSP-ProjectTemplate.git* voor de **URL van de kloon**. Selecteer vervolgens **Importeren**. De inhoud van de Microsoft TDSP-team ProjectTemplate-opslagplaats wordt geïmporteerd in uw GroupProjectTemplate-opslagplaats. 
   
   ![Microsoft TDSP-teamrepository importeren](./media/group-manager-tasks/import-repo-2.png)
   
1. Ga boven aan de pagina **Repos** naar beneden en selecteer de **GroupUtilities-opslagplaats.**
   
1. Herhaal het importproces om de inhoud van de Microsoft TDSP-team **utilities** repository, *\/https: /github.com/Azure/Azure-TDSP-Utilities.git*, te importeren in uw **GroupUtilities** repository. 
   
Elk van uw twee groepsrepositories bevat nu alle bestanden, behalve die in de *.git-map,* uit de bijbehorende opslagplaats van het Microsoft TDSP-team. 

## <a name="customize-the-contents-of-the-group-repositories"></a>De inhoud van de groepsopslagplaatsen aanpassen

Als u de inhoud van uw groepsopslagplaatsen wilt aanpassen aan de specifieke behoeften van uw groep, u dat nu doen. U de bestanden wijzigen, de directorystructuur wijzigen of bestanden toevoegen die uw groep heeft ontwikkeld of die nuttig zijn voor uw groep.

### <a name="make-changes-in-azure-repos"></a>Wijzigingen aanbrengen in Azure Repos

Ga als u de inhoud van de opslagplaats aanpassen:

1. Selecteer **Repo's**op de pagina **Groepsalgemeen** **projectoverzicht** . 
   
1. Selecteer boven aan de pagina de opslagplaats die u wilt aanpassen.

1. Navigeer in de repo-mapstructuur naar de map of het bestand dat u wilt wijzigen. 
   
   - Als u nieuwe mappen of bestanden wilt maken, selecteert u de pijl naast **Nieuw**. 
     
     ![Nieuw bestand maken](./media/group-manager-tasks/new-file.png)
     
   - Als u bestanden wilt uploaden, selecteert u **Bestand(en uploaden)**. 
     
     ![Bestanden uploaden](./media/group-manager-tasks/upload-files.png)
     
   - Als u bestaande bestanden wilt bewerken, navigeert u naar het bestand en selecteert u **Bewerken**. 
     
     ![Een bestand bewerken](./media/group-manager-tasks/edit-file.png)
     
1. Nadat u bestanden hebt toegevoegd of bewerkt, selecteert u **Vastleggen**.
   
   ![Wijzigingen vastleggen](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>Wijzigingen aanbrengen met uw lokale machine of DSVM

Als u wijzigingen wilt aanbrengen met uw lokale machine of DSVM en de wijzigingen wilt doordrukken naar de groepsopslagplaatsen, moet u ervoor zorgen dat u de vereisten hebt voor het werken met Git en DSVMs:

- Een Azure-abonnement als u een DSVM wilt maken.
- Git geïnstalleerd op uw machine. Als u een DSVM gebruikt, is Git vooraf geïnstalleerd. Anders zie de [bijlage platforms en gereedschappen](platforms-and-tools.md#appendix).
- Als u een DSVM wilt gebruiken, heeft de Windows- of Linux DSVM in Azure gemaakt en geconfigureerd. Zie de Data Science [Virtual Machine Documentation](/azure/machine-learning/data-science-virtual-machine/)voor meer informatie en instructies.
- Voor een Windows DSVM is [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) geïnstalleerd op uw machine. Schuif *in* het README.md-bestand omlaag naar de sectie **Downloaden en installeren** en selecteer het nieuwste **installatieprogramma.** Download de *.exe* installer van de installatiepagina en voer deze uit. 
- Voor een Linux DSVM is een SSH-openbare sleutel ingesteld op uw DSVM en toegevoegd in Azure DevOps. Zie de sectie **SSH public key maken** in de [bijlage platforms en hulpmiddelen](platforms-and-tools.md#appendix)voor meer informatie en instructies. 

Kopieer of *kloon* eerst de opslagplaats naar uw lokale machine. 
   
1. Selecteer op de pagina **Groepsalgemeen** **projectoverzicht** de optie **Repo's**en selecteer boven aan de pagina de opslagplaats die u wilt klonen.
   
1. Selecteer **kloon** rechtsboven op de repo-pagina.
   
1. Selecteer **in** het dialoogvenster **Kloonopslagplaats** de optie HTTPS voor een HTTP-verbinding of **SSH** voor een SSH-verbinding en kopieer de kloon-URL onder **opdrachtregel** naar het klembord.
   
   ![Kloon repo](./media/group-manager-tasks/clone.png)
   
1. Maak op uw lokale machine de volgende mappen:
   
   - Voor Windows: **C:\GitRepos\GroupCommon**
   - Voor Linux, **$/GitRepos/GroupCommon** op uw home directory 
   
1. Wijzigen in de map die u hebt gemaakt.
   
1. Voer in Git Bash de opdracht uit`git clone <clone URL>.`
   
   Een van de volgende opdrachten kloont bijvoorbeeld de **GroupUtilities-opslagplaats** naar de map *GroupCommon* op uw lokale machine. 
   
   **HTTPS-verbinding:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **SSH-verbinding:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

Nadat u de gewenste wijzigingen hebt aangebracht in de lokale kloon van uw opslagplaats, u de wijzigingen in de gemeenschappelijke opslagplaatsen van de gedeelde groep pushen. 

Voer de volgende Git Bash-opdrachten uit uw lokale **GroupProjectTemplate-** of **GroupUtilities-map.**

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

## <a name="add-group-members-and-configure-permissions"></a>Groepsleden toevoegen en machtigingen configureren

Als u leden aan de groep wilt toevoegen:

1. Selecteer in Azure DevOps op de startpagina **van GroupCommon** project **project projectinstellingen** aan de linkerkant. 
   
1. Selecteer in de navigatie **Projectinstellingen** links **de**optie Teams en selecteer vervolgens op de pagina **Teams** het **Groepsgewoon team**. 
   
   ![Teams configureren](./media/group-manager-tasks/teams.png)
   
1. Selecteer op de pagina **Teamprofiel** de optie **Toevoegen**.
   
   ![Toevoegen aan GroupCommon-team](./media/group-manager-tasks/add-to-team.png)
   
1. Zoek **in** het dialoogvenster Gebruikers en groepen toevoegen naar en selecteer leden die u aan de groep wilt toevoegen en selecteer **wijzigingen opslaan**. 
   
   ![Gebruikers en groepen toevoegen](./media/group-manager-tasks/add-users.png)
   

Machtigingen voor leden configureren:

1. Selecteer **Machtigingen**in de navigatie **projectinstellingen** links . 
   
1. Selecteer **op** de pagina Machtigingen de groep waaraan u leden wilt toevoegen. 
   
1. Selecteer op de pagina voor die groep **Leden**en selecteer **Vervolgens Toevoegen**. 
   
1. Zoek **in** de pop-up Leden uitnodigen naar en selecteer leden die aan de groep willen toevoegen en selecteer **Vervolgens Opslaan**. 
   
   ![Machtigingen verlenen aan leden](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>Volgende stappen

Hier vindt u links naar gedetailleerde beschrijvingen van de andere rollen en taken in het Team Data Science-proces:

- [Team Lead taken voor een data science team](team-lead-tasks.md)
- [Projectleadtaken voor een data science-team](project-lead-tasks.md)
- [Project individuele bijdragertaken voor een data science-team](project-ic-tasks.md)
