---
title: Wat is het proces voor het weten van team data?
description: Biedt een Data Science-methodologie voor het leveren van predictive analytics oplossingen en intelligente toepassingen.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79088088"
---
# <a name="what-is-the-team-data-science-process"></a>Wat is het proces voor het weten van team data?

Het team data Science process (TDSP) is een flexibele, iteratieve methode voor gegevens wetenschap om predictive analytics oplossingen en intelligente toepassingen efficiënt te leveren. TDSP helpt de samen werking en het leren van teams te verbeteren door te suggereren hoe team rollen het beste samen werken. TDSP omvat de aanbevolen procedures en structuren van micro soft en andere toonaangevende leveranciers om te helpen bij de implementatie van data Science-initiatieven. Het doel is bedrijven te helpen om de voordelen van hun analyseprogramma volledig te kunnen benutten.

Dit artikel bevat een overzicht van TDSP en de belangrijkste onderdelen ervan. We bieden hier een algemene beschrijving van het proces dat kan worden geïmplementeerd met verschillende soorten hulp middelen. Een gedetailleerde beschrijving van de project taken en-rollen die bij de levens cyclus van het proces betrokken zijn, vindt u in aanvullende gekoppelde onderwerpen. Richt lijnen voor het implementeren van de TDSP met behulp van een specifieke set hulpprogram ma's en infra structuur van micro soft die we gebruiken voor het implementeren van de TDSP in onze teams, ook wel.

## <a name="key-components-of-the-tdsp"></a>Belangrijkste onderdelen van de TDSP

TDSP bestaat uit de volgende belang rijke onderdelen:

- Een definitie van een **Data Science-levens cyclus**
- Een **gestandaardiseerde project structuur**
- **Infra structuur en bronnen** voor data Science-projecten
- **Hulpprogram ma's en hulpprogram ma's** voor het uitvoeren van projecten


## <a name="data-science-lifecycle"></a>Data wetenschap-levens cyclus

Het team data Science process (TDSP) biedt een levens cyclus voor het structureren van de ontwikkeling van uw data Science-projecten. De levens cyclus geeft een overzicht van de volledige stappen die geslaagde projecten volgen.

Als u een andere data Science-levens cyclus gebruikt, zoals [glashelder-dm](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) of het eigen aangepaste proces van uw organisatie, kunt u nog steeds de op taken gebaseerde TDSP gebruiken in de context van deze ontwikkelings levenscycluss. Op hoog niveau zijn deze verschillende methodologieën veel gemeen. 

Deze levens cyclus is ontworpen voor data wetenschappen-projecten die als onderdeel van intelligente toepassingen worden geleverd. Deze toepassingen implementeren machine learning of kunst matige intelligentie modellen voor predictive analytics. Experimentele projecten voor gegevens Wetenschappen of Improvised Analytics-projecten kunnen ook profiteren van het gebruik van dit proces. Maar in dergelijke gevallen zijn enkele van de beschreven stappen mogelijk niet nodig.    

De levens cyclus bevat een overzicht van de belangrijkste fasen die door projecten meestal worden uitgevoerd, vaak iteratief:

* **Zakelijke inzichten**
* **Gegevens verkrijgen en meer informatie**
* **Modelleren**
* **Implementatie**
* **Klant acceptatie**

Hier volgt een visuele representatie van de **levens cyclus van team data Science process**. 

![TDSP-Lifecycle2](./media/overview/tdsp-lifecycle2.png) 

De doel stellingen, taken en documentatie artefacten voor elke fase van de levens cyclus in TDSP worden beschreven in het onderwerp [team data Science process Lifecycle](lifecycle.md) . Deze taken en artefacten zijn gekoppeld aan project rollen:

- Oplossings architect
- Projectmanager
- Gegevenswetenschapper
- Projectleider 

In het volgende diagram ziet u een raster weergave van de taken (in het blauw) en artefacten (in het groen) die zijn gekoppeld aan elke fase van de levens cyclus (op de horizontale as) voor deze rollen (op de verticale as). 

[![TDSP-rollen-en-taken](./media/overview/tdsp-tasks-by-roles.png)](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Gestandaardiseerde project structuur

Wanneer alle projecten een mapstructuur delen en sjablonen gebruiken voor project documenten, kunnen team leden gemakkelijk informatie over hun projecten vinden. Alle code en documenten worden opgeslagen in een versie beheersysteem (VCS) zoals git, TFS of Subversion om team samenwerking in te scha kelen. Het bijhouden van taken en functies in een flexibel systeem voor project tracking, zoals Jira, rally en Azure DevOps, maakt het mogelijk de code voor afzonderlijke functies bij te houden. Dit houdt ook in dat teams betere kosten ramingen kunnen verkrijgen. TDSP adviseert het maken van een afzonderlijke opslag plaats voor elk project op de VCS voor versie beheer, informatie beveiliging en samen werking. De gestandaardiseerde structuur voor alle projecten helpt bij het bouwen van institutionele kennis binnen de hele organisatie.

We bieden sjablonen voor de mappen structuur en de vereiste documenten op standaard locaties. In deze mapstructuur worden de bestanden die code bevatten voor het verkennen van gegevens en het uitpakken van onderdelen, geordend en die record model iteraties. Deze sjablonen maken het gemakkelijker voor team leden om inzicht te krijgen in werk dat door anderen wordt uitgevoerd en om nieuwe leden aan teams toe te voegen. U kunt gemakkelijk document sjablonen bekijken en bijwerken in de indeling prijs opgave. Gebruik sjablonen om controle lijsten in te stellen met belang rijke vragen voor elk project om ervoor te zorgen dat het probleem goed is gedefinieerd en dat de resultaten voldoen aan de verwachte kwaliteit. Voorbeelden zijn:

- een project Handvest om het bedrijfs probleem en de reik wijdte van het project te documenteren
- gegevens rapporten om de structuur en statistieken van de onbewerkte gegevens te documenteren
- model rapporten om de afgeleide functies te documenteren
- prestatie gegevens van modellen, zoals ROC curven of MSE, model leren


[![TDSP-directory's](./media/overview/tdsp-dir-structure.png)](./media/overview/tdsp-dir-structure.png#lightbox)

De mapstructuur kan worden gekloond van [github](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infra structuur en bronnen voor data Science-projecten  

TDSP biedt aanbevelingen voor het beheren van gedeelde analyses en opslag infrastructuren, zoals:

- Cloud bestands systemen voor het opslaan van gegevens sets 
- databases
- big data (Hadoop-of Spark-clusters) 
- machine learning-service 

De analyse-en opslag infrastructuur, waar RAW-en verwerkte gegevens sets worden opgeslagen, kunnen zich in de Cloud of on-premises bevinden. Deze infra structuur maakt reproduceer bare analyses mogelijk. Er wordt ook gevermijdd dupliceren, wat kan leiden tot inconsistenties en onnodige infrastructuur kosten. Er zijn hulpprogram ma's beschikbaar voor het inrichten van de gedeelde resources, het volgen ervan en het toestaan van elk teamlid om veilig verbinding te maken met die bronnen. Het is ook een goed idee om project leden een consistente Compute-omgeving te laten maken. Andere team leden kunnen vervolgens experimenten repliceren en valideren.

Hier volgt een voor beeld van een team dat aan meerdere projecten werkt en verschillende onderdelen van Cloud Analytics-infra structuur deelt.

[![TDSP-infra structuur](./media/overview/tdsp-analytics-infra.png)](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>Hulpprogram ma's en hulpprogram ma's voor het uitvoeren van projecten

Het introduceren van processen in de meeste organisaties is lastig. Hulp middelen voor het implementeren van het data Science proces en de levens cyclus helpen de belemmeringen te verkorten en de consistentie van de acceptatie te verg Roten. TDSP biedt een initiële set hulpprogram ma's en scripts waarmee u de TDSP binnen een team aan de slag kunt gaan. Het helpt ook enkele veelvoorkomende taken in de data Science-levens cyclus te automatiseren, zoals het verkennen van gegevens en het maken van basis lijnen. Er is een goed gedefinieerde structuur voor personen die gedeelde hulp middelen en hulpprogram ma's in de gedeelde code opslagplaats van hun team kunnen bijdragen. Deze resources kunnen vervolgens worden gebruikt door andere projecten binnen het team of de organisatie. TDSP is ook van plan om de bijdragen van hulpprogram ma's en hulpprogram ma's aan de hele community in te scha kelen. De TDSP-hulpprogram ma's kunnen worden gekloond van [github](https://github.com/Azure/Azure-TDSP-Utilities).


## <a name="next-steps"></a>Volgende stappen

[Team data Science process: rollen en taken](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) Geeft een overzicht van de belangrijkste personeels rollen en de bijbehorende taken voor een Data Science-team dat op dit proces wordt gestandaardiseerd. 
