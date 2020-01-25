---
title: Taken voor een individuele Inzender in Team Data Science Process
description: Een gedetailleerd overzicht van de taken voor een individuele mede werker van een Data Science-Team project.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721248"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Taken voor een individuele Inzender in Team Data Science Process

In dit onderwerp vindt u een overzicht van de taken die een *individuele mede werker* heeft voltooid voor het instellen van een project in het [team data Science process](overview.md) (TDSP). Het doel is om te werken in een samen werkende team omgeving die wordt gestandaardiseerd op de TDSP. De TDSP is ontworpen om samen werking en team educatie te verbeteren. Voor een overzicht van de personeels rollen en de bijbehorende taken die worden verwerkt door een Data Science-team dat wordt gestandardization op de TDSP, raadpleegt u [rollen en taken voor team data Science process](roles-tasks.md).

In het volgende diagram ziet u de taken die door project afzonderlijke mede werkers (gegevens wetenschappers) zijn voltooid om hun team omgeving in te stellen. Zie voor instructies over het uitvoeren van een Data Science-project onder het TDSP de [uitvoering van data Science-projecten](project-execution.md). 

![Individuele Inzender taken](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **ProjectRepository** is de opslag plaats die uw project team beheert om Project sjablonen en-assets te delen.
- **TeamUtilities** is de opslag plaats voor hulpprogram ma's die uw team specifiek voor uw team beheert. 
- **GroupUtilities** is de opslag plaats die uw groep onderhoudt om nuttige hulpprogram ma's te delen in de hele groep. 

> [!NOTE] 
> In dit artikel wordt gebruikgemaakt van Azure opslag plaatsen en een Data Science Virtual Machine (DSVM) voor het instellen van een TDSP-omgeving, omdat u hiermee TDSP implementeert bij micro soft. Als uw team gebruikmaakt van andere code hosting-of ontwikkelings platformen, zijn de afzonderlijke Inzender taken hetzelfde, maar de manier om ze te volt ooien, kan afwijken.

## <a name="prerequisites"></a>Vereisten

In deze zelf studie wordt ervan uitgegaan dat de volgende resources en machtigingen zijn ingesteld door uw [groeps Manager](group-manager-tasks.md), [team lead](team-lead-tasks.md)en [Project Lead](project-lead-tasks.md):

- De Azure DevOps **-organisatie** voor uw data Science-eenheid
- Een **project opslagplaats** die is ingesteld door de project leider om Project sjablonen en-assets te delen
- **GroupUtilities** -en **TeamUtilities** -opslag plaatsen die zijn ingesteld door de groeps Manager en team lead, indien van toepassing
- Azure **File Storage** is ingesteld voor gedeelde assets voor uw team of project, indien van toepassing
- **Machtigingen** voor het klonen van en terugsturen naar de project opslagplaats 

Als u opslag plaatsen wilt klonen en inhoud wilt wijzigen op uw lokale machine of DSVM of als u Azure File Storage wilt koppelen aan uw DSVM, moet u rekening houden met deze controle lijst:

- Een Azure-abonnement.
- Git geïnstalleerd op de computer. Als u een DSVM gebruikt, is Git vooraf geïnstalleerd. Raadpleeg anders de [platformen en hulpprogramma's voor bijlage](platforms-and-tools.md#appendix).
- Als u een DSVM wilt gebruiken, wordt de Windows-of Linux-DSVM gemaakt en geconfigureerd in Azure. Zie de [Data Science virtual machine-documentatie](/azure/machine-learning/data-science-virtual-machine/)voor meer informatie en instructies.
- Voor een Windows DSVM is [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) op uw computer geïnstalleerd. Schuif in het bestand *README.MD* omlaag naar de sectie **downloaden en installeren** en selecteer het **nieuwste installatie programma**. Down load het installatie programma *. exe* op de pagina installatie programma en voer het uit. 
- Voor een Linux-DSVM is een open bare SSH-sleutel ingesteld op uw DSVM en toegevoegd aan Azure DevOps. Zie de sectie **open bare SSH-sleutel maken** in de [bijlage platformen en hulpprogram ma's](platforms-and-tools.md#appendix)voor meer informatie en instructies. 
- De Azure File Storage-gegevens voor Azure file storage die u nodig hebt om te koppelen aan uw DSVM. 

## <a name="clone-repositories"></a>Opslag plaatsen klonen

Als u lokaal wilt werken met opslag plaatsen en u uw wijzigingen naar het gedeelde team en project opslagplaatsen wilt pushen, kopieert of *kloont* u de opslag plaatsen eerst naar uw lokale machine. 

1. Ga in azure DevOps naar de pagina project overzicht van uw team op *https:\//\<server naam >/\<organisatie naam >/\<team naam >* , bijvoorbeeld **https:\//dev.Azure.com/DataScienceUnit/MyTeam**.
   
1. Selecteer **opslag plaatsen** in de linkernavigatiebalk en selecteer boven aan de pagina de opslag plaats die u wilt klonen.
   
1. Selecteer op de pagina opslag plaats rechtsboven **klonen** .
   
1. In het dialoog venster kloon van de **opslag plaats** selecteert u **https** voor een http-verbinding of **SSH** voor een SSH-verbinding en kopieert u de kloon-URL onder de **opdracht regel** naar het klem bord.
   
   ![Opslag plaats klonen](./media/project-ic-tasks/clone.png)
   
1. Maak de volgende directory's op uw lokale computer of DSVM:
   
   - Voor Windows: **C:\GitRepos**
   - Voor Linux: **$Home/gitrepos**
   
1. Ga naar de map die u hebt gemaakt.
   
1. Voer in Git-Bash de opdracht uit `git clone <clone URL>` voor elke opslag plaats die u wilt klonen. 
   
   Met de volgende opdracht wordt bijvoorbeeld de **TeamUtilities** -opslag plaats gekloond naar de *MyTeam* -map op uw lokale computer. 
   
   **HTTPS-verbinding:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH-verbinding:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. Controleer of u de mappen kunt zien voor de gekloonde opslag plaatsen in de lokale projectmap.
   
   ![Drie lokale opslagplaats mappen](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>Azure File Storage koppelen aan uw DSVM

Als uw team of project gedeelde assets heeft in azure File Storage, koppelt u de bestands opslag aan uw lokale machine of DSVM. Volg de instructies bij het [koppelen van Azure File Storage op uw lokale machine of DSVM](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm).

## <a name="next-steps"></a>Volgende stappen

Hier vindt u koppelingen naar gedetailleerde beschrijvingen van de andere rollen en taken die worden gedefinieerd door het team data Science process:

- [Groepsbeheerder taken voor een team van gegevenswetenschappers](group-manager-tasks.md)
- [Team Lead taken voor een team van gegevenswetenschappers](team-lead-tasks.md)
- [Project Lead taken voor een team van gegevenswetenschappers](project-lead-tasks.md)

