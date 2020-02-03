---
title: Implementatiefase van de levenscyclus van het Team Data Science Process
description: De doelen, taken en producten voor de implementatiefase van uw data-science-projecten
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1138c95274c769186a9a29aa4d35517e378baeae
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720483"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Implementatiefase van de levenscyclus van het Team Data Science Process

In dit artikel bevat een overzicht van de doelen, taken en producten die zijn gekoppeld aan de implementatie van het Team Data Science Process (TDSP). Deze procedure biedt de levensduur van een aanbevolen die u gebruiken kunt voor het structureren van uw data-science-projecten. De levenscyclus van geeft een overzicht van de belangrijke fasen die projecten doorgaans worden uitgevoerd, vaak iteratief:

   1. **Zakelijke inzichten**
   2. **Gegevens verkrijgen en meer informatie**
   3. **Model**
   4. **Implementatie**
   5. **Klant acceptatie**

Hier volgt een visuele representatie van de TDSP-levenscyclus: 

![TDSP-levenscyclus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Doel
Implementeer modellen met een gegevenspijplijn in een productie- of productie-achtige omgeving voor de acceptatie van de eindgebruiker. 

## <a name="how-to-do-it"></a>Hoe voer ik
De belangrijkste taak in deze fase behandeld:

**Operationeel maken het model**: Implementeer het model en de pijp lijn op een productie-of productie omgeving voor het verbruik van toepassingen.

### <a name="operationalize-a-model"></a>Een model operationeel maken
Nadat u een set van modellen die goed presteren hebt, kunt u ze voor andere toepassingen om te gebruiken kunt uitvoeren. Afhankelijk van de zakelijke vereisten, worden voorspellingen gedaan in realtime of op basis van de batch. Voor het implementeren van modellen, stelt u deze bloot met een open API-interface. De interface kunt het model om te worden eenvoudig verbruikt via verschillende toepassingen, zoals:

   * Online websites
   * Spreadsheets 
   * Dashboards
   * Line-of-business-toepassingen 
   * Back-end-toepassingen 

Zie [Deploy a Azure machine learning web service](../studio/deploy-a-machine-learning-web-service.md)(Engelstalig) voor voor beelden van model uitoefening met een Azure machine learning-webservice. Het is een aanbevolen procedure voor het bouwen Telemetrie en bewaking in de productiemodel en de pijplijn die u implementeert. Met deze procedure helpt bij de volgende systeemstatus reporting en probleemoplossing.  

## <a name="artifacts"></a>Artefacten

* Een statusdashboard waarop de system health en de sleutel metrische gegevens
* Een rapport laatste modellering met details van de implementatie
* Een definitieve oplossing architectuur document


## <a name="next-steps"></a>Volgende stappen

Hier vindt u koppelingen naar elke stap in de levenscyclus van de TDSP:

   1. [Zakelijke inzichten](lifecycle-business-understanding.md)
   2. [Gegevens verkrijgen en meer informatie](lifecycle-data.md)
   3. [Model](lifecycle-modeling.md)
   4. [Implementatie](lifecycle-deployment.md)
   5. [Klant acceptatie](lifecycle-acceptance.md)

We bieden volledige instructies voor het demonstreren van alle stappen in het proces voor specifieke scenario's. Het artikel [voorbeeld](walkthroughs.md) scenario's bevat een lijst met de scenario's met koppelingen en miniaturen. De scenario's laten zien hoe u cloud, on-premises hulpprogramma's en services combineren in een werkstroom of een pijplijn te maken van een intelligente toepassingen. 

Zie [de TDSP gebruiken met Azure machine learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)voor voor beelden van het uitvoeren van stappen in TDSPs die gebruikmaken van Azure machine learning Studio.
