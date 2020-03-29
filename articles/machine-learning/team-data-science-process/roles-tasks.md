---
title: Rollen en taken van team Data Science Process
description: Een overzicht van de belangrijkste onderdelen, personeelsrollen en bijbehorende taken voor een gegevenswetenschapsgroep.
author: marktab
manager: marktab
editor: marktab
services: machine-learning
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c1ed731943abf0efdd99ea54d2318fa402835e08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720007"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Rollen en taken van team Data Science Process

Het Team Data Science Process (TDSP) is een framework ontwikkeld door Microsoft dat een gestructureerde methodologie biedt om efficiënt voorspellende analyseoplossingen en intelligente toepassingen te bouwen. In dit artikel worden de belangrijkste personeelsrollen en bijbehorende taken beschreven voor een data science-team dat dit proces standaardiseert.

Dit inleidende artikel verwijst naar zelfstudies over het instellen van de TDSP-omgeving. De zelfstudies bieden gedetailleerde richtlijnen voor het gebruik van Azure DevOps Projects, Azure Repos-repositories en Azure Boards.  Het motiverende doel is het verplaatsen van concept via modellering en naar implementatie.

De tutorials maken gebruik van Azure DevOps, want dat is hoe tdsp te implementeren bij Microsoft. Azure DevOps faciliteert samenwerking door rolgebaseerde beveiliging, beheer en tracking van werkitems en codehosting, delen en bronbeheer te integreren. De tutorials gebruiken ook een Azure [Data Science Virtual Machine](https://aka.ms/dsvm) (DSVM) als het analytics-bureaublad, dat verschillende populaire data science-tools vooraf heeft geconfigureerd en geïntegreerd met Microsoft-software en Azure-services. 

U de zelfstudies gebruiken om TDSP te implementeren met behulp van andere codehosting, flexibele planning en ontwikkelingstools en -omgevingen, maar sommige functies zijn mogelijk niet beschikbaar.

## <a name="structure-of-data-science-groups-and-teams"></a>Structuur van data science groepen en teams

Data science functies in ondernemingen worden vaak georganiseerd in de volgende hiërarchie:

- Data science groep
  - Data science team/s binnen de groep

In een dergelijke structuur zijn er groepsleads en teamleads. Typisch, een data science project wordt gedaan door een data science team. Data science teams hebben project leads voor project management en governance taken, en individuele data scientists en engineers om de data science en data engineering onderdelen van het project uit te voeren. De initiële projectinstelling en -governance wordt uitgevoerd door de groep, het team of de projectleads.

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>Definitie en taken voor de vier TDSP-rollen
Met de veronderstelling dat de data science unit bestaat uit teams binnen een groep, zijn er vier verschillende rollen voor TDSP personeel:

1. **Groepsmanager**: beheert de volledige data science-eenheid in een onderneming. Een data science-eenheid kan meerdere teams hebben, die elk werken aan meerdere data science-projecten in verschillende bedrijfsverticalen. Een groepsmanager kan zijn taken delegeren aan een surrogaat, maar de taken die aan de rol zijn gekoppeld, worden niet gewijzigd.
   
2. **Teamlead**: Beheert een team in de data science-eenheid van een onderneming. Een team bestaat uit meerdere data scientists. Voor een kleine data science-eenheid kunnen de groepsmanager en de teamleider dezelfde persoon zijn.
   
3. **Projectlead**: Beheert de dagelijkse activiteiten van individuele data scientists op een specifiek data science project.
   
4. **Project individuele bijdragers**: Data Scientists, Business Analisten, Data Engineers, Architecten, en anderen die een data science project uit te voeren.

> [!NOTE]
> Afhankelijk van de structuur en grootte van een onderneming kan één persoon meer dan één rol spelen, of kan meer dan één persoon een rol vervullen.

### <a name="tasks-to-be-completed-by-the-four-roles"></a>Taken die door de vier rollen moeten worden voltooid

In het volgende diagram worden de taken op het hoogste niveau weergegeven voor elke teamgegevenswetenschapsprocesrol. Dit schema en de volgende, meer gedetailleerde overzicht van taken voor elke TDSP-rol kunnen u helpen bij het kiezen van de zelfstudie die u nodig hebt op basis van uw verantwoordelijkheden.

![Overzicht van rollen en taken](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>Groepsbeheertaken

De groepsmanager of een aangewezen TDSP-systeembeheerder voltooit de volgende taken om de TDSP over te nemen:

- Hiermee maakt u een **organization** Azure DevOps-organisatie en een groepsproject binnen de organisatie. 
- Hiermee maakt u een **projectsjabloonopslagplaats** in het azure DevOps-groepsproject en wordt deze verwijderd uit de projectsjabloonopslagplaats die is ontwikkeld door het Microsoft TDSP-team. De Microsoft TDSP-projectsjabloonrepository biedt:
  - Een **gestandaardiseerde directorystructuur,** inclusief mappen voor gegevens, code en documenten.
  - Een set **gestandaardiseerde documentsjablonen** om een efficiënt data science-proces te begeleiden.
- Hiermee maakt u een **hulpprogrammarepository**en wordt deze gescheiden uit de hulpprogramma-opslagplaats die is ontwikkeld door het Microsoft TDSP-team. De TDSP utility repository van Microsoft biedt een reeks nuttige hulpprogramma's om het werk van een data scientist efficiënter te maken. De Microsoft utility repository bevat hulpprogramma's voor interactieve gegevensverkenning, analyse, rapportage en basislijnmodellering en rapportage.
- Hiermee stelt u het **beveiligingsbeheerbeleid** in voor het organisatieaccount.

Zie [Groepsbeheertaken voor een data science-team voor](group-manager-tasks.md)gedetailleerde instructies.

## <a name="team-lead-tasks"></a>Teamleadtaken

De teamleider of een aangewezen projectbeheerder voltooit de volgende taken om het TDSP over te nemen:

- Hiermee maakt u een **teamproject** in de Azure DevOps-organisatie van de groep.
- Hiermee maakt u de **projectsjabloonopslagplaats** in het project en wordt deze verwijderd uit de groepsprojectsjabloonopslagplaats die is ingesteld door de groepsmanager of gemachtigde.
- Hiermee maakt u de **teamutility repository,** wordt deze verwijderd uit de groepshulpprogrammarepository en worden teamspecifieke hulpprogramma's toegevoegd aan de repository.
- Maakt optioneel [Azure-bestandsopslag](https://azure.microsoft.com/services/storage/files/) om nuttige gegevenselementen voor het team op te slaan. Andere teamleden kunnen deze gedeelde cloudbestandswinkel op hun analytics-desktops monteren.
- Optioneel monteert u de Azure-bestandsopslag op de **DSVM** van het team en voegt er teamgegevenselementen aan toe.
- Stelt **beveiligingsbeheer** in door teamleden toe te voegen en hun machtigingen te configureren.

Zie [Teamleadtaken voor een data science-team voor](team-lead-tasks.md)gedetailleerde instructies.


## <a name="project-lead-tasks"></a>Projectleadtaken

De projectlead voltooit de volgende taken om het TDSP over te nemen:

- Hiermee maakt u een **projectopslagplaats** in het teamproject en wordt deze verwijderd uit de opslagplaats voor projectsjablonen.
- Maakt optioneel **Azure-bestandsopslag** om de gegevenselementen van het project op te slaan.
- Optioneel monteert u de Azure-bestandsopslag aan de **DSVM** en voegt er projectgegevenselementen aan toe.
- Hiermee stelt u **beveiligingsbeheer** in door projectleden toe te voegen en hun machtigingen te configureren.

Zie [Projectleadtaken voor een data science-team voor](project-lead-tasks.md)gedetailleerde instructies.

## <a name="project-individual-contributor-tasks"></a>Taken voor individuele inzender van project

De projectbijdrager, meestal een gegevenswetenschapper, voert de volgende taken uit met behulp van het TDSP:

- Kloont de **projectrepository** die is ingesteld door de projectlead.
- Optioneel monteert het gedeelde team en project **Azure-bestandsopslag** op hun **Data Science Virtual Machine** (DSVM).
- Voert het project uit.

Zie [Project Individual Contributor-taken voor een data science-team voor](project-ic-tasks.md)gedetailleerde instructies voor onboarding op een project.

## <a name="data-science-project-execution-workflow"></a>Werkstroom voor het uitvoeren van data science-projecten

Door de relevante zelfstudies te volgen, kunnen gegevenswetenschappers, projectleads en teamleads werkitems maken om alle taken en fasen voor het project van begin tot eind bij te houden. Het gebruik van Azure Repos bevordert de samenwerking tussen gegevenswetenschappers en zorgt ervoor dat de artefacten die tijdens de uitvoering van het project worden gegenereerd, worden beheerd door de versie en worden gedeeld door alle projectleden. Met Azure DevOps u uw Azure Boards-werkitems koppelen aan uw Azure Repos-opslagplaatsen en eenvoudig bijhouden wat er voor een werkitem is gedaan.

In de volgende afbeelding wordt de TDSP-werkstroom voor projectuitvoering beschreven:

![Typische workflow voor data science-projecten](./media/roles-tasks/overview-project-execute.png)

De werkstroomstappen kunnen worden gegroepeerd in drie activiteiten:

- Projectleads voeren sprintplanning uit
- Gegevenswetenschappers ontwikkelen artefacten op `git` takken om werkitems aan te pakken
- Projectleads of andere teamleden doen codebeoordelingen en voegen werkbranches samen met de hoofdbranch

Zie [Agile ontwikkeling van data science-projecten](agile-development.md)voor gedetailleerde instructies over de workflow voor projectuitvoering.

## <a name="tdsp-project-template-repository"></a>TDSP-projectsjabloonopslagplaats

Gebruik de [projectsjabloonrepository](https://github.com/Azure/Azure-TDSP-ProjectTemplate) van het Microsoft TDSP-team om efficiënte projectuitvoering en -samenwerking te ondersteunen. De repository biedt u een gestandaardiseerde directorystructuur en documentsjablonen die u gebruiken voor uw eigen TDSP-projecten.

## <a name="next-steps"></a>Volgende stappen

Bekijk meer gedetailleerde beschrijvingen van de rollen en taken die zijn gedefinieerd door het Team Data Science Process:

- [Group Manager taken voor een data science team](group-manager-tasks.md)
- [Team Lead taken voor een data science team](team-lead-tasks.md)
- [Projectleadtaken voor een data science-team](project-lead-tasks.md)
- [Project individuele bijdragertaken voor een data science-team](project-ic-tasks.md)
