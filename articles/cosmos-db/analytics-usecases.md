---
title: Use-cases voor ingebouwde analyses met Azure Cosmos DB.
description: Meer informatie over het gebruik van ingebouwde analyses met Azure Cosmos DB in verschillende use cases.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: b9bac14ff47107a4b39f12989685e2e510569ba2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72757080"
---
# <a name="use-cases-for-built-in-analytics-with-azure-cosmos-db"></a>Use-cases voor ingebouwde analyses met Azure Cosmos DB

De volgende use-cases kunnen worden bereikt door gebruik te maken van de ingebouwde analyses met Apache Spark in Azure Cosmos DB:

## <a name="htap-scenarios"></a>HTAP-scenario's

Ingebouwde analyses in Azure Cosmos DB kunnen worden gebruikt om:

* Fraude detecteren tijdens de verwerking van transacties.
* Geef aanbevelingen aan shoppers terwijl ze door een E-commercewinkel bladeren.
* Alarmer operators voor het dreigende falen van een cruciaal stuk productie-apparatuur.
* Creëer snel en actiebaar inzicht door real-time analytics direct in te bedden op operationele gegevens.

Azure Cosmos DB ondersteunt HTAP-scenario's (Hybrid Transactional/Analytical Processing) met behulp van de native ingebouwde Apache Spark. Azure Cosmos DB verwijdert de operationele en analytische scheiding die wordt geleverd met de traditionele systemen.

## <a name="globally-distributed-data-lake-without-requiring-any-etl"></a>Wereldwijd gedistribueerd datalake zonder eTL

Met de native ingebouwde Apache Spark biedt Azure Cosmos DB een snelle, eenvoudige en schaalbare manier om een wereldwijd gedistribueerd datalake te bouwen dat één systeemafbeelding biedt. De wereldwijd verspreide multi-model gegevens elimineert de noodzaak om te investeren in dure ETL-pijplijnen en schalen on-demand, een revolutie in de manier waarop datateams hun gegevenssets analyseren.

## <a name="time-series-analytics-over-historic-data"></a>Tijdreeksanalyses over historische gegevens

In sommige gevallen moet u vragen beantwoorden op basis van gegevens, omdat op een specifiek tijdstip gebeurtenissen zijn voltooid die in het verleden zijn voltooid. Bijvoorbeeld om het aantal CRM-activiteitsstatussen op een bepaalde datum te krijgen. Als u het rapport een week geleden hebt uitgevoerd, zou de telling van de statussen zijn volgens de statussen van elke activiteit op dat moment. Het uitvoeren van hetzelfde rapport vandaag geeft u de telling van de activiteiten waarvan de status sis zoals ze zijn zoals ze nu zijn, die kan zijn veranderd sinds vorige week, als ze gaan door hun levenscyclus van open tot sluiten. U moet dus in elke fase van de levenscyclus van de aanvraag verslag uitbrengen van de momentopname.

In traditionele scenario's voor gegevensmagazijnen is het concept van momentopname niet mogelijk omdat de gegevensmagazijnen niet zijn ontworpen om het op te nemen en de gegevens alleen een actueel overzicht geven van wat er gebeurt. Met Azure Cosmos DB hebben gebruikers de mogelijkheid om het concept van tijdreizen te implementeren, in staat om met terugwerkende kracht analyses op de gegevens te bevragen en uit te voeren en te vragen hoe de gegevens naar een specifiek tijdstip in de geschiedenis hebben gekeken. Dit betekent dat gebruikers gemakkelijk zowel de huidige als historische weergaven van de gegevens kunnen bekijken en er analyses op kunnen uitvoeren.

## <a name="globally-distributed-machine-learning-and-ai"></a>Wereldwijd gedistribueerde machine learning en AI

Aangezien bedrijven kampen met snel groeiende hoeveelheden gegevens en een groeiende verscheidenheid aan gegevenstypen en -formaten, wordt de mogelijkheid om diepere en nauwkeurigere inzichten te verkrijgen bijna onmogelijk op petabyteschaal over de hele wereld. Met de native ingebouwde Apache Spark biedt Azure Cosmos DB een wereldwijd gedistribueerd analyseplatform dat uitgebreide bibliotheek met machine learning-algoritmen biedt. U interactieve Jupyter-notitieblokken gebruiken om modellen te bouwen en te trainen, en clusterbeheermogelijkheden. Met deze mogelijkheden u op aanvraag zeer afgestemde en automatisch elastische Spark-clusters inrichten.

## <a name="deep-learning-on-multi-model-globally-distributed-data"></a>Deep Learning op multi-model wereldwijd gedistribueerde gegevens

Deep learning is de ideale manier om predictive analytics-oplossingen voor big data te bieden, omdat het datavolume en de complexiteit blijven groeien. Met deep learning kunnen bedrijven de kracht van ongestructureerde en semi-gestructureerde gegevens benutten om use cases te leveren die gebruikmaken van technieken zoals AI, natuurlijke taalverwerking en meer.

## <a name="reporting-integrating-with-power-apps-power-bi"></a>Rapportage (integreren met Power Apps, Power BI)

Power BI biedt interactieve visualisaties met self-service business intelligence-mogelijkheden, waardoor eindgebruikers zelf rapporten en dashboards kunnen maken. Met de ingebouwde Spark-connector u Power BI Desktop aansluiten op Apache Spark-clusters in Azure Cosmos DB. Met deze connector u directe query gebruiken om de verwerking naar Apache Spark in Azure Cosmos DB te ontladen, wat geweldig is wanneer u een enorme hoeveelheid gegevens hebt die u niet in Power BI wilt laden of wanneer u bijna realtime analyses wilt uitvoeren.

## <a name="iot-analytics-at-global-scale"></a>IoT-analyses op wereldwijde schaal

De gegevens die worden gegenereerd door toenemende netwerksensoren brengen een ongekend inzicht in voorheen ondoorzichtige systemen en processen. De sleutel is het vinden van bruikbare inzichten in deze stortvloed van informatie, ongeacht waar IoT-apparaten worden verspreid over de hele wereld. Azure Cosmos DB stelt IOT-bedrijven in staat om high-velocity sensor- en tijdreeksgegevens overal ter wereld in realtime te analyseren. Het stelt u in staat om de werkelijke waarde van een onderling verbonden wereld te benutten om verbeterde klantervaringen, operationele efficiëntie en nieuwe omzetmogelijkheden te bieden.

## <a name="stream-processing-and-event-analytics"></a>Analyse van streamverwerking en gebeurtenissen 

Van logbestanden tot sensorgegevens, bedrijven hebben steeds meer de behoefte om om te gaan met "stromen" van gegevens. Deze gegevens komen in een gestage stroom, vaak uit meerdere bronnen tegelijk. Hoewel het haalbaar is om deze gegevensstromen op schijf op te slaan en ze met terugwerkende kracht te analyseren, kan het soms verstandig of belangrijk zijn om de gegevens te verwerken en ernaar te handelen zodra deze aankomt. Zo kunnen gegevensstromen met betrekking tot financiële transacties in realtime worden verwerkt om mogelijk frauduleuze transacties te identificeren en te weigeren.

## <a name="interactive-analytics"></a>Interactieve analyses

Naast het uitvoeren van vooraf gedefinieerde query's om statische dashboards te maken voor verkoop, productiviteit of aandelenkoersen, u de gegevens interactief verkennen. Met interactieve analyses u vragen stellen, de resultaten bekijken, de eerste vraag wijzigen op basis van het antwoord of dieper ingaan op resultaten. Apache Spark in Azure Cosmos DB ondersteunt interactieve query's door snel te reageren en aan te passen.

## <a name="data-exploration-using-jupyter-notebooks"></a>Gegevensverkenning met Jupyter-notitieblokken

Wanneer u een nieuwe gegevensset hebt, moet u voordat u in lopende modellen en tests duikt, uw gegevens inspecteren. Met andere woorden, u moet verkennende gegevensanalyse uitvoeren. Data-exploratie kan verschillende beslissingen bepalen. U bijvoorbeeld details vinden, zoals de methoden die geschikt zijn om op uw gegevens te gebruiken, of de gegevens voldoen aan bepaalde modelleringsveronderstellingen, of de gegevens moeten worden gereinigd, geherstructureerd enz. Door gebruik te maken van de native ingebouwde Jupyter-notebooks en Apache Spark van Azure Cosmos DB, u snelle en effectieve verkennende gegevensanalyse uitvoeren op transactionele en analytische gegevens.

## <a name="next-steps"></a>Volgende stappen

Ga om aan de slag met deze use cases naar de volgende artikelen:

* [Ingebouwde Jupyter-laptops in Azure Cosmos DB](cosmosdb-jupyter-notebooks.md)
* [Notitieblokken inschakelen voor Azure Cosmos-accounts](enable-notebooks.md)
* [Een notitieblok maken om gegevens te analyseren en te visualiseren](create-notebook-visualize-data.md)