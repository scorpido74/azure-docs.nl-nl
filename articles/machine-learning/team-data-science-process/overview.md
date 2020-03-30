---
title: Wat is het Team Data Science Process?
description: Biedt een data science methodologie om voorspellende analyse oplossingen en intelligente toepassingen te leveren.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79088088"
---
# <a name="what-is-the-team-data-science-process"></a>Wat is het Team Data Science Process?

Het Team Data Science Process (TDSP) is een flexibele, iteratieve data science methodologie om voorspellende analyseoplossingen en intelligente applicaties efficiënt te leveren. TDSP helpt teamsamenwerking en -leren te verbeteren door voor te stellen hoe teamrollen het beste samenwerken. TDSP bevat best practices en structuren van Microsoft en andere marktleiders om te helpen bij een succesvolle implementatie van data science-initiatieven. Het doel is bedrijven te helpen om de voordelen van hun analyseprogramma volledig te kunnen benutten.

Dit artikel geeft een overzicht van TDSP en de belangrijkste componenten. We geven hier een algemene beschrijving van het proces dat kan worden geïmplementeerd met verschillende soorten tools. Een meer gedetailleerde beschrijving van de projecttaken en -rollen die betrokken zijn bij de levenscyclus van het proces wordt gegeven in aanvullende gekoppelde onderwerpen. Richtlijnen voor de implementatie van de TDSP met behulp van een specifieke set Microsoft-tools en -infrastructuur die we gebruiken om de TDSP in onze teams te implementeren, worden ook verstrekt.

## <a name="key-components-of-the-tdsp"></a>Belangrijkste onderdelen van het TDSP

TDSP bestaat uit de volgende kerncomponenten:

- Een **definitie van de levenscyclus van gegevenswetenschap**
- Een **gestandaardiseerde projectstructuur**
- **Infrastructuur en middelen** voor data science-projecten
- **Tools en hulpprogramma's** voor projectuitvoering


## <a name="data-science-lifecycle"></a>Levenscyclus van gegevenswetenschap

Het Team Data Science Process (TDSP) biedt een levenscyclus om de ontwikkeling van uw data science-projecten te structureren. De levenscyclus geeft een overzicht van de volledige stappen die succesvolle projecten volgen.

Als u een andere levenscyclus van gegevenswetenschap gebruikt, zoals [CRISP-DM,](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining) [KDD of](https://wikipedia.org/wiki/Data_mining#Process) het eigen aangepaste proces van uw organisatie, u de op taken gebaseerde TDSP nog steeds gebruiken in de context van die ontwikkelingslevenscycluss. Op een hoog niveau hebben deze verschillende methoden veel gemeen. 

Deze levenscyclus is ontworpen voor data science-projecten die worden verzonden als onderdeel van intelligente toepassingen. Deze toepassingen implementeren machine learning- of kunstmatige intelligentie-modellen voor voorspellende analyses. Verkennende data science projecten of geïmproviseerde analytics projecten kunnen ook profiteren van het gebruik van dit proces. Maar in dergelijke gevallen kunnen sommige van de beschreven stappen niet nodig zijn.    

De levenscyclus schetst de belangrijkste fasen die projecten doorgaans uitvoeren, vaak iteratief:

* **Bedrijfsinzicht**
* **Gegevensverwerving en -inzicht**
* **Modelleren**
* **Implementatie**
* **Acceptatie door de klant**

Hier is een visuele weergave van de levenscyclus van het **Team Data Science Process.** 

![TDSP-Lifecycle2](./media/overview/tdsp-lifecycle2.png) 

De doelen, taken en documentatieartefacten voor elke fase van de levenscyclus in TDSP worden beschreven in het [levenscyclusonderwerp van teamgegevenswetenschapsproces.](lifecycle.md) Deze taken en artefacten zijn gekoppeld aan projectrollen:

- Oplossingarchitect
- Projectmanager
- Gegevenswetenschapper
- Projectleider 

Het volgende diagram biedt een rasterweergave van de taken (in blauw) en artefacten (in het groen) die zijn gekoppeld aan elke fase van de levenscyclus (op de horizontale as) voor deze rollen (op de verticale as). 

[![TDSP-rollen-en-taken](./media/overview/tdsp-tasks-by-roles.png)](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Gestandaardiseerde projectstructuur

Als alle projecten een directorystructuur delen en sjablonen voor projectdocumenten gebruiken, kunnen de teamleden eenvoudig informatie over hun projecten vinden. Alle code en documenten worden opgeslagen in een versiebeheersysteem (VCS) zoals Git, TFS of Subversion om teamsamenwerking mogelijk te maken. Het bijhouden van taken en functies in een wendbaar projectvolgsysteem zoals Jira, Rally en Azure DevOps maakt het mogelijk de code voor afzonderlijke functies beter te volgen. Een dergelijke tracking stelt teams ook in staat om betere kostenramingen te verkrijgen. TDSP raadt aan om voor elk project op de VCS een aparte opslagplaats te maken voor versiebeheer, informatiebeveiliging en samenwerking. De gestandaardiseerde structuur voor alle projecten helpt bij het opbouwen van institutionele kennis in de hele organisatie.

We bieden sjablonen voor de mapstructuur en vereiste documenten op standaardlocaties. Deze mapstructuur organiseert de bestanden die code bevatten voor gegevensverkenning en functieextractie, en die modeliteraties opnemen. Deze sjablonen maken het voor teamleden gemakkelijker om het werk van anderen te begrijpen en nieuwe leden toe te voegen aan teams. Het is eenvoudig om documentsjablonen in markdown-indeling te bekijken en bij te werken. Gebruik sjablonen om checklists te voorzien van belangrijke vragen voor elk project om ervoor te zorgen dat het probleem goed is gedefinieerd en dat deliverables voldoen aan de verwachte kwaliteit. Voorbeelden zijn:

- een projectcharter om het bedrijfsprobleem en de reikwijdte van het project vast te leggen
- gegevensrapporten om de structuur en statistieken van de ruwe gegevens te documenteren
- modelrapporten om de afgeleide functies te documenteren
- modelprestatiestatistieken zoals ROC-curven of MSE


[![TDSP-mappen](./media/overview/tdsp-dir-structure.png)](./media/overview/tdsp-dir-structure.png#lightbox)

De directorystructuur kan worden gekloond van [GitHub.](https://github.com/Azure/Azure-TDSP-ProjectTemplate)

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infrastructuur en middelen voor data science-projecten  

TDSP biedt aanbevelingen voor het beheren van gedeelde analyses en opslaginfrastructuur, zoals:

- cloudbestandssystemen voor het opslaan van gegevenssets 
- databases
- big data (Hadoop of Spark) clusters 
- machine learning-service 

De analyse- en opslaginfrastructuur, waar ruwe en verwerkte gegevenssets worden opgeslagen, kan zich in de cloud of on-premises bevinden. Deze infrastructuur maakt reproduceerbare analyse mogelijk. Het voorkomt ook duplicatie, wat kan leiden tot inconsistenties en onnodige infrastructuurkosten. Er worden tools verstrekt om de gedeelde resources in te richten, ze bij te houden en elk teamlid in staat te stellen veilig verbinding te maken met deze bronnen. Het is ook een goede gewoonte om projectleden een consistente compute-omgeving te laten maken. Verschillende teamleden kunnen vervolgens experimenten repliceren en valideren.

Hier is een voorbeeld van een team dat aan meerdere projecten werkt en verschillende componenten van cloudanalytics-infrastructuur deelt.

[![TDSP-infrastructuur](./media/overview/tdsp-analytics-infra.png)](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>Tools en hulpprogramma's voor projectuitvoering

Het introduceren van processen in de meeste organisaties is een uitdaging. Tools die worden verstrekt om het data science-proces en de levenscyclus te implementeren, helpen de barrières voor en de consistentie van de adoptie ervan te verlagen. TDSP biedt een eerste set tools en scripts om de adoptie van TDSP binnen een team te starten. Het helpt ook bij het automatiseren van een aantal van de algemene taken in de levenscyclus van gegevenswetenschap, zoals gegevensverkenning en basislijnmodellering. Er is een goed gedefinieerde structuur voorzien voor individuen om gedeelde tools en hulpprogramma's bij te dragen aan de gedeelde coderepository van hun team. Deze resources kunnen vervolgens worden gebruikt door andere projecten binnen het team of de organisatie. TDSP is ook van plan om de bijdragen van tools en nutsvoorzieningen aan de hele gemeenschap mogelijk te maken. De TDSP-hulpprogramma's kunnen worden gekloond van [GitHub.](https://github.com/Azure/Azure-TDSP-Utilities)


## <a name="next-steps"></a>Volgende stappen

[Team Data Science Process: Rollen en taken](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) Geeft een overzicht van de belangrijkste personeelsrollen en de bijbehorende taken voor een data science-team dat dit proces standaardiseert. 
