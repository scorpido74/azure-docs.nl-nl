---
title: Taken voor de projectleider in het Team Data Science Proces
description: Een gedetailleerde overzicht van de taken voor een projectlead in een Team Data Science Process team
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4a4d3a1480a852218e698862a509c4af45e49eb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76714409"
---
# <a name="project-lead-tasks-in-the-team-data-science-process"></a>Projectleadtaken in het Team Data Science Process

In dit artikel worden taken beschreven die een *projectlead* voltooit om een opslagplaats voor hun projectteam in het [Team Data Science Process](overview.md) (TDSP) in te stellen. De TDSP is een framework ontwikkeld door Microsoft dat een gestructureerde reeks activiteiten biedt om cloudgebaseerde, voorspellende analyseoplossingen efficiënt uit te voeren. Het TDSP is ontworpen om samenwerking en teamlearning te verbeteren. Zie [Team Data Science Process rollen en taken](roles-tasks.md)voor een team dat zich standaard maakt voor een team dat zich op het TDSP standaardiseert.

Een projectlead beheert de dagelijkse activiteiten van individuele data scientists op een specifiek data science project in het TDSP. In het volgende diagram wordt de werkstroom voor projectleadtaken weergegeven:

![Werkstroom van projectleadtaak](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Deze zelfstudie heeft betrekking op stap 1: Projectrepository maken en Stap 2: Seed projectrepository van uw projectProjectTemplate-opslagplaats. 

Voor stap 3: Functiewerkitem voor project maken en stap 4: Verhalen toevoegen voor projectfasen, zie [Agile ontwikkeling van data science-projecten.](agile-development.md)

Voor stap 5: Opslag-/analyse-elementen maken en aanpassen en zo nodig delen, zie [Teamgegevens en analysebronnen maken.](team-lead-tasks.md#create-team-data-and-analytics-resources)

Voor stap 6: Beveiligingsbeheer instellen van projectrepository, zie [Teamleden toevoegen en machtigingen configureren.](team-lead-tasks.md#add-team-members-and-configure-permissions)

> [!NOTE] 
> In dit artikel wordt Azure Repos gebruikt om een TDSP-project in te stellen, omdat u TDSP bij Microsoft implementeren. Als uw team een ander codehostingplatform gebruikt, zijn de projectleadtaken hetzelfde, maar de manier om ze te voltooien kan anders zijn.

## <a name="prerequisites"></a>Vereisten

In deze zelfstudie wordt ervan uitgegaan dat uw [groepsmanager](group-manager-tasks.md) en [teamleider](team-lead-tasks.md) de volgende bronnen en machtigingen hebben ingesteld:

- De Azure **DevOps-organisatie** voor uw gegevenseenheid
- Een **teamproject** voor uw data science team
- Teamsjabloon en **hulpprogramma's repositories**
- **Machtigingen** voor uw organisatieaccount voor u om opslagplaatsen voor uw project te maken en te bewerken

Als u opslagplaatsen wilt klonen en inhoud op uw lokale machine of Data Science Virtual Machine (DSVM) wilt wijzigen of Azure-bestandsopslag wilt instellen en deze wilt monteren op uw DSVM, moet u ook rekening houden met deze checklist:

- Een Azure-abonnement.
- Git geïnstalleerd op uw machine. Als u een DSVM gebruikt, is Git vooraf geïnstalleerd. Anders zie de [bijlage platforms en gereedschappen](platforms-and-tools.md#appendix).
- Als u een DSVM wilt gebruiken, heeft de Windows- of Linux DSVM in Azure gemaakt en geconfigureerd. Zie de Data Science [Virtual Machine Documentation](/azure/machine-learning/data-science-virtual-machine/)voor meer informatie en instructies.
- Voor een Windows DSVM is [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) geïnstalleerd op uw machine. Schuif *in* het README.md-bestand omlaag naar de sectie **Downloaden en installeren** en selecteer het nieuwste **installatieprogramma.** Download de *.exe* installer van de installatiepagina en voer deze uit. 
- Voor een Linux DSVM is een SSH-openbare sleutel ingesteld op uw DSVM en toegevoegd in Azure DevOps. Zie de sectie **SSH public key maken** in de [bijlage platforms en hulpmiddelen](platforms-and-tools.md#appendix)voor meer informatie en instructies. 

## <a name="create-a-project-repository-in-your-team-project"></a>Een projectrepository maken in uw teamproject

Ga als het gaat om het maken van een projectrepository in het **MyTeam-project van** uw team:

1. Ga naar de **projectoverzichtspagina** van uw team op *\//\<https: servernaam>/\<organisatienaam>/\<teamnaam>*, bijvoorbeeld **https:\//dev.azure.com/DataScienceUnit/MyTeam**en selecteer **Repos** in de linkernavigatie. 
   
1. Selecteer de naam van de opslagplaats boven aan de pagina en selecteer **Vervolgens Nieuwe opslagplaats** in de vervolgkeuzelijst.
   
   ![Nieuwe opslagplaats selecteren](./media/project-lead-tasks/project-leads-9-select-repos.png)
   
1. Controleer in het dialoogvenster **Een nieuwe opslagplaats maken** of **Git** is geselecteerd onder **Type**. Voer *DSProject1* in onder **de naam Repository**en selecteer Vervolgens **Maken**.
   
   ![Repository maken](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)
   
1. Controleer of u de nieuwe **DSProject1-opslagplaats** op uw projectinstellingenpagina zien. 
   
   ![Projectrepository in Projectinstellingen](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

## <a name="import-the-team-template-into-your-project-repository"></a>De teamsjabloon importeren in uw projectopslagplaats

Ga als volgende over uw projectopslagplaats met de inhoud van de opslagplaats voor teamsjablonen:

1. Selecteer **Repo's** in de linkernavigatie op de pagina **Projectoverzicht van** uw team. 
   
1. Selecteer de naam van de opslagplaats boven aan de pagina en selecteer **DSProject1** in de vervolgkeuzelijst.
   
1. Selecteer **Importeren**op de pagina **DSProject1 is leeg** . 
   
   ![Selecteer Importeren](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)
   
1. Selecteer **Git** als **brontype**in het dialoogvenster **Een Git-opslagplaats importeren** en voer de URL in voor uw **TeamTemplate-opslagplaats** onder **Kloon-URL**. De URL is *\//\<https:\<servernaam>/ organisatienaam\<>/ teamnaam>/_git/\<teamtemplate repository>*. Bijvoorbeeld: **https:\//dev.azure.com/DataScienceUnit/MyTeam/_git/TeamTemplate**. 
   
1. Selecteer **Importeren**. De inhoud van uw teamsjabloonrepository wordt geïmporteerd in uw projectrepository. 
   
   ![Opslagplaats teamsjabloon importeren](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

Als u de inhoud van uw projectopslagplaats moet aanpassen aan de specifieke behoeften van uw project, u opslagplaatsen en mappen toevoegen, verwijderen of wijzigen. U rechtstreeks werken in Azure Repos, of de repository klonen naar uw lokale machine of DSVM, wijzigingen aanbrengen en uw updates vastleggen en pushen naar de gedeelde projectrepository. Volg de instructies [bij De inhoud van de teamrepositories aanpassen.](team-lead-tasks.md#customize-the-contents-of-the-team-repositories)

## <a name="next-steps"></a>Volgende stappen

Hier vindt u links naar gedetailleerde beschrijvingen van de andere rollen en taken die zijn gedefinieerd door het Team Data Science-proces:

- [Group Manager taken voor een data science team](group-manager-tasks.md)
- [Team Lead taken voor een data science team](team-lead-tasks.md)
- [Individuele bijdragertaken voor een data science-team](project-ic-tasks.md)
