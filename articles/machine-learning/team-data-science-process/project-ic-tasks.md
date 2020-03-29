---
title: Taken voor een individuele bijdrager in het Team Data Science-proces
description: Een gedetailleerde overzicht van de taken voor een individuele bijdrager aan een data science team project.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d9942c31b63de77196b8b51b88376cb8ef74b990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721248"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Taken voor een individuele bijdrager in het Team Data Science-proces

In dit onderwerp worden de taken beschreven die een *individuele bijdrager* heeft uitgevoerd om een project in het [Team Data Science Process](overview.md) (TDSP) in te stellen. Het doel is om te werken in een collaboratieve teamomgeving die standaardiseert op de TDSP. Het TDSP is ontworpen om samenwerking en teamlearning te verbeteren. Zie [Team Data Science Process rollen en taken](roles-tasks.md)voor een overzicht van de personeelsrollen en de bijbehorende taken die worden uitgevoerd door een data science-team dat standaard is op het TDSP.

In het volgende diagram ziet u de taken die afzonderlijke bijdragers (gegevenswetenschappers) uitvoeren om hun teamomgeving in te stellen. Zie [Uitvoering van data science-projecten](project-execution.md)voor instructies over het uitvoeren van een data science-project onder het TDSP. 

![Taken voor individuele inzender](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **ProjectRepository** is de opslagplaats die uw projectteam onderhoudt om projectsjablonen en -assets te delen.
- **TeamUtilities** is de utilities repository die uw team specifiek voor uw team onderhoudt. 
- **GroupUtilities** is de opslagplaats die uw groep onderhoudt om nuttige hulpprogramma's te delen in de hele groep. 

> [!NOTE] 
> In dit artikel wordt Azure Repos en een Data Science Virtual Machine (DSVM) gebruikt om een TDSP-omgeving in te stellen, omdat u TDSP bij Microsoft implementeren. Als uw team andere codehosting- of ontwikkelingsplatforms gebruikt, zijn de afzonderlijke taken voor bijdragen hetzelfde, maar de manier om deze te voltooien kan anders zijn.

## <a name="prerequisites"></a>Vereisten

In deze zelfstudie wordt ervan uitgegaan dat de volgende bronnen en machtigingen zijn ingesteld door uw [groepsmanager,](group-manager-tasks.md) [teamleider](team-lead-tasks.md)en [projectlead:](project-lead-tasks.md)

- De Azure **DevOps-organisatie** voor uw data science-eenheid
- Een **projectrepository** die door uw projectlead is ingesteld om projectsjablonen en -assets te delen
- **GroupUtilities** en **TeamUtilities** repositories opgezet door de groepsmanager en teamleider, indien van toepassing
- **Azure-bestandsopslag** die is ingesteld voor gedeelde elementen voor uw team of project, indien van toepassing
- **Machtigingen** voor u om te klonen van en terug te duwen naar uw project repository 

Als u opslagplaatsen wilt klonen en inhoud op uw lokale machine of DSVM wilt wijzigen of Azure-bestandsopslag wilt monteren op uw DSVM, moet u deze checklist overwegen:

- Een Azure-abonnement.
- Git geïnstalleerd op uw machine. Als u een DSVM gebruikt, is Git vooraf geïnstalleerd. Anders zie de [bijlage platforms en gereedschappen](platforms-and-tools.md#appendix).
- Als u een DSVM wilt gebruiken, heeft de Windows- of Linux DSVM in Azure gemaakt en geconfigureerd. Zie de Data Science [Virtual Machine Documentation](/azure/machine-learning/data-science-virtual-machine/)voor meer informatie en instructies.
- Voor een Windows DSVM is [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) geïnstalleerd op uw machine. Schuif *in* het README.md-bestand omlaag naar de sectie **Downloaden en installeren** en selecteer het nieuwste **installatieprogramma.** Download de *.exe* installer van de installatiepagina en voer deze uit. 
- Voor een Linux DSVM is een SSH-openbare sleutel ingesteld op uw DSVM en toegevoegd in Azure DevOps. Zie de sectie **SSH public key maken** in de [bijlage platforms en hulpmiddelen](platforms-and-tools.md#appendix)voor meer informatie en instructies. 
- De Azure-bestandsopslaggegevens voor elke Azure-bestandsopslag die u aan uw DSVM moet monteren. 

## <a name="clone-repositories"></a>Clone repositories

Om lokaal met repositories te werken en uw wijzigingen door te schuiven naar het gedeelde team en projectrepositories, kopieert of *kloont* u de repositories eerst naar uw lokale machine. 

1. Ga in Azure DevOps naar de projectoverzichtspagina van uw team op *\//\<https: servernaam>/\<organisatienaam>/\<teamnaam>*, bijvoorbeeld **https:\//dev.azure.com/DataScienceUnit/MyTeam**.
   
1. Selecteer **Repo's** in de linkernavigatie en selecteer boven aan de pagina de opslagplaats die u wilt klonen.
   
1. Selecteer **kloon** rechtsboven op de repo-pagina.
   
1. Selecteer **in** het dialoogvenster **Kloonopslagplaats** de optie HTTPS voor een HTTP-verbinding of **SSH** voor een SSH-verbinding en kopieer de kloon-URL onder **opdrachtregel** naar het klembord.
   
   ![Kloon repo](./media/project-ic-tasks/clone.png)
   
1. Maak op uw lokale machine of DSVM de volgende mappen:
   
   - Voor Windows: **C:\GitRepos**
   - Voor Linux: **$home/GitRepos**
   
1. Wijzigen in de map die u hebt gemaakt.
   
1. Voer in Git Bash `git clone <clone URL>` de opdracht uit voor elke opslagplaats die u wilt klonen. 
   
   Met de volgende opdracht wordt bijvoorbeeld de **TeamUtilities-opslagplaats** naar de *MyTeam-map* op uw lokale machine gekerend. 
   
   **HTTPS-verbinding:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH-verbinding:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. Controleer of u de mappen voor de gekloonde opslagplaatsen zien in uw lokale projectmap.
   
   ![Drie lokale repository-mappen](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>Azure-bestandsopslag op uw DSVM monteren

Als uw team of project gedeelde elementen heeft in Azure-bestandsopslag, monteert u de bestandsopslag op uw lokale machine of DSVM. Volg de instructies bij [Azure-bestandsopslag monteren op uw lokale machine of DSVM.](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm)

## <a name="next-steps"></a>Volgende stappen

Hier vindt u links naar gedetailleerde beschrijvingen van de andere rollen en taken die zijn gedefinieerd door het Team Data Science-proces:

- [Group Manager taken voor een data science team](group-manager-tasks.md)
- [Team Lead taken voor een data science team](team-lead-tasks.md)
- [Projectleadtaken voor een data science-team](project-lead-tasks.md)

