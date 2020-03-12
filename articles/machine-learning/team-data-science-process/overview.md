---
title: Wat is Team Data Science Process?
description: Een data science-methodologie voor het leveren van predictive analytics-oplossingen en intelligente toepassingen biedt.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: overview
ms.date: 1/10/2020
ms.author: tdsp
ms.custom: previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 10d6e562301e089700940ac5dfb212bcc4e09653
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79088088"
---
# <a name="what-is-the-team-data-science-process"></a>Wat is Team Data Science Process?

Het Team Data Science Process (TDSP) is een flexibele, iteratieve data science-methodologie predictive analytics-oplossingen en intelligente toepassingen efficiënt leveren. TDSP helpt de samen werking en het leren van teams te verbeteren door te suggereren hoe team rollen het beste samen werken. TDSP omvat de aanbevolen procedures en structuren van micro soft en andere toonaangevende leveranciers om te helpen bij de implementatie van data Science-initiatieven. Het doel is bedrijven te helpen om de voordelen van hun analyseprogramma volledig te kunnen benutten.

Dit artikel bevat een overzicht van TDSP en de belangrijkste onderdelen. We bieden hier een algemene beschrijving van het proces dat kan worden geïmplementeerd met verschillende soorten hulp middelen. Een gedetailleerde beschrijving van de rollen die betrokken zijn bij de levenscyclus van het proces van de project-taken en is beschikbaar in extra gekoppelde onderwerpen. Richtlijnen voor het implementeren van de TDSP met behulp van een specifieke set Microsoft-programma's en infrastructuur die we gebruiken voor het implementeren van de TDSP in onze teams is ook beschikbaar.

## <a name="key-components-of-the-tdsp"></a>Belangrijke onderdelen van de TDSP

TDSP bestaat uit de volgende belangrijke onderdelen:

- Een definitie van een **Data Science-levens cyclus**
- Een **gestandaardiseerde project structuur**
- **Infra structuur en bronnen** voor data Science-projecten
- **Hulpprogram ma's en hulpprogram ma's** voor het uitvoeren van projecten


## <a name="data-science-lifecycle"></a>Levenscyclus van wetenschappelijke gegevens

Het Team Data Science Process (TDSP) biedt een levenscyclus voor de ontwikkeling van uw data science-projecten structureren. De levens cyclus geeft een overzicht van de volledige stappen die geslaagde projecten volgen.

Als u een andere data Science-levens cyclus gebruikt, zoals [glashelder-dm](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) of het eigen aangepaste proces van uw organisatie, kunt u nog steeds de op taken gebaseerde TDSP gebruiken in de context van deze ontwikkelings levenscycluss. Op hoog niveau hebben deze verschillende methoden veel gemeen. 

Deze levenscyclus beheren, is ontworpen voor data science-projecten die worden geleverd als onderdeel van intelligente toepassingen. Deze toepassingen machine learning of kunstmatige intelligentie modellen voor voorspellende analyses te implementeren. Experimentele projecten voor gegevens Wetenschappen of Improvised Analytics-projecten kunnen ook profiteren van het gebruik van dit proces. Maar in dergelijke gevallen enkele van de stappen mogelijk niet nodig.    

De levenscyclus van geeft een overzicht van de belangrijke fasen die projecten doorgaans worden uitgevoerd, vaak iteratief:

* **Zakelijke inzichten**
* **Gegevens verkrijgen en meer informatie**
* **Model**
* **Implementatie**
* **Klant acceptatie**

Hier volgt een visuele representatie van de **levens cyclus van team data Science process**. 

![TDSP-Lifecycle2](./media/overview/tdsp-lifecycle2.png) 

De doel stellingen, taken en documentatie artefacten voor elke fase van de levens cyclus in TDSP worden beschreven in het onderwerp [team data Science process Lifecycle](lifecycle.md) . Deze taken en artefacten zijn gekoppeld aan het Projectrollen:

- Oplossingsarchitect
- Projectmanager
- Gegevenswetenschapper
- Projectleider 

Het volgende diagram biedt een rasterweergave van de taken (in het blauw) en de artefacten (in het groen) die zijn gekoppeld aan elke fase van de levenscyclus (op de horizontale as) voor deze rollen (op de verticale as). 

[![TDSP-roles-and-tasks](./media/overview/tdsp-tasks-by-roles.png)](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Gestandaardiseerde projectstructuur

Met alle projecten delen van een mapstructuur en sjablonen gebruiken voor de project-documenten, kunt eenvoudig de teamleden voor informatie over hun projecten. Alle code en documenten worden opgeslagen in een versiebeheersysteem (Circuits), zoals Git, TFS of Subversion om in te schakelen samenwerking voor teams. Bijhouden van taken en -functies in een flexibele volgsysteem zoals Jira, Rally en Azure DevOps-project, kunt dichter in de buurt van de code voor afzonderlijke onderdelen bijhouden. Dergelijke bijhouden kan ook teams om op te halen beter kostenramingen. TDSP raadt aan om het maken van een aparte opslagplaats voor elk project in de Circuits voor versiebeheer, informatiebeveiliging en samenwerking. De gestandaardiseerde structuur voor alle projecten helpt met het bouwen van institutionele kennis in de hele organisatie.

We bieden sjablonen voor de mapstructuur en de vereiste documenten in standaardlocaties. Deze mapstructuur organiseert de bestanden die code voor gegevensverkenning en het ophalen van functies bevatten en dat model iteraties opnemen. Deze sjablonen wordt het eenvoudiger voor teamleden om te begrijpen werk van anderen en nieuwe leden toevoegen aan teams. Het is gemakkelijk om te bekijken en bijwerken van sjablonen in markdown-indeling. Gebruik sjablonen om controle lijsten in te stellen met belang rijke vragen voor elk project om ervoor te zorgen dat het probleem goed is gedefinieerd en dat de resultaten voldoen aan de verwachte kwaliteit. Voorbeelden zijn:

- een project Handvest vastleggen van de zakelijke probleem en het bereik van het project
- rapporten met gegevens naar het document de structuur en statistieken van de onbewerkte gegevens
- model-rapporten naar de afgeleide functies
- model maatstaven voor prestaties zoals ROC-curve of MSE


[![TDSP-directory's](./media/overview/tdsp-dir-structure.png)](./media/overview/tdsp-dir-structure.png#lightbox)

De mapstructuur kan worden gekloond van [github](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infrastructuur en resources voor data science-projecten  

TDSP bevat aanbevelingen voor het beheren van gedeelde analyse- en opslaginfrastructuur, zoals:

- cloud-bestandssystemen voor het opslaan van gegevenssets 
- databases
- de clusters (Hadoop- of Apache Spark) big data 
- machine learning-service 

De analyse-en opslag infrastructuur, waar RAW-en verwerkte gegevens sets worden opgeslagen, kunnen zich in de Cloud of on-premises bevinden. Deze infrastructuur kunt reproduceerbare analyse. Ook voorkomt u dubbele, wat tot inconsistenties en onnodige infrastructuurkosten leiden kan. Hulpprogramma's worden aan de gedeelde resources inrichten, traceren en zorgen dat elk teamlid veilig verbinding maken met deze resources verstrekt. Het is ook een goede gewoonte om het project leden maken van een consistente compute-omgeving. Andere teamleden kunnen vervolgens worden gerepliceerd en experimenten valideren.

Hier volgt een voorbeeld van een team werkt aan meerdere projecten en het delen van verschillende onderdelen van de cloud analytics infrastructuur.

[![TDSP-infra structuur](./media/overview/tdsp-analytics-infra.png)](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>Hulpprogramma's voor de projectuitvoering van

Maak kennis met de processen in de meeste organisaties uitdagingen met zich mee. Hulpprogramma's te implementeren de data science-proces en de levenscyclus help lager de barrières voor vergroten van de consistentie van hun goedkeuring. TDSP biedt een eerste set met hulpprogramma's en scripts aan de acceptatie van TDSP binnen een team snel aan de slag. Ook helpt u bij automatiseren van de algemene taken in de levenscyclus van wetenschappelijke gegevens, zoals gegevens verkennen en modelleren van de basislijn. Er is een goed gedefinieerde structuur voor personen die gedeelde hulp middelen en hulpprogram ma's in de gedeelde code opslagplaats van hun team kunnen bijdragen. Deze resources kunnen vervolgens worden gebruikt door andere projecten in het team of de organisatie. TDSP is ook van plan om in te schakelen van de bijdragen van de hulpprogramma's voor de gehele community. De TDSP-hulpprogram ma's kunnen worden gekloond van [github](https://github.com/Azure/Azure-TDSP-Utilities).


## <a name="next-steps"></a>Volgende stappen

[Team data Science process: rollen en taken](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) Geeft een overzicht van de belangrijkste personeels rollen en de bijbehorende taken voor een Data Science-team dat op dit proces wordt gestandaardiseerd. 
