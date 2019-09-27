---
title: Gebruik-cases voor ingebouwde analyses met Azure Cosmos DB.
description: Meer informatie over het gebruik van ingebouwde analyses met Azure Cosmos DB in verschillende use cases.
author: rimman
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 308e4d986fcbda155a7e6992f6efe0b1914bcfc2
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338956"
---
# <a name="use-cases-for-built-in-analytics-with-azure-cosmos-db"></a>Use-cases voor ingebouwde analyses met Azure Cosmos DB

U kunt de volgende gebruiks voorbeelden bereiken met behulp van de ingebouwde analyses met Apache Spark in Azure Cosmos DB:

## <a name="htap-scenarios"></a>HTAP-scenario's

Ingebouwde analyses in Azure Cosmos DB kunnen worden gebruikt voor het volgende:

* Fraude detecteren tijdens transactie verwerking.
* Geef kopers aanbevelingen voor het bladeren door een commerce-winkel.
* Waarschuwings operatoren voor een dreigende storing van een essentieel onderdeel van productie apparatuur.
* Maak snelle, praktische inzichten door real-time analyse rechtstreeks in te sluiten op operationele gegevens.

Azure Cosmos DB ondersteunt hybride transactionele/Analytical Processing-scenario's (HTAP) met behulp van de systeem eigen, ingebouwde Apache Spark. Azure Cosmos DB verwijdert de operationele en analytische schei ding die wordt geleverd met de traditionele systemen.

## <a name="globally-distributed-data-lake-without-requiring-any-etl"></a>Wereld wijd gedistribueerde data Lake zonder ETL te vereisen

Met native ingebouwde Apache Spark biedt Azure Cosmos DB een snelle, eenvoudige en schaal bare manier om een wereld wijd gedistribueerde data Lake te bouwen dat één installatie kopie van het systeem levert. De wereld wijd gedistribueerde multi-model gegevens elimineert de nood zaak om te investeren in dure ETL-pijp lijnen en schalen op aanvraag, Revolutionizing de manier waarop gegevens teams hun gegevens sets analyseren.

## <a name="time-series-analytics-over-historic-data"></a>Time Series-analyse via historische gegevens

In sommige gevallen kan het nodig zijn om vragen te beantwoorden op basis van gegevens, zoals bij een bepaald tijdstip in het verleden. Als u bijvoorbeeld het aantal statussen van de CRM-activiteit op een bepaalde datum wilt ophalen. Als u het rapport een week geleden hebt uitgevoerd, wordt het aantal statussen weer geven volgens de status van elke activiteit op dat moment. Als u hetzelfde rapport vandaag uitvoert, geeft u het aantal activiteiten weer waarvan de status is ingesteld op vandaag, wat sinds de laatste week mogelijk is gewijzigd, omdat de levens cyclus van het openen is afgelopen. U moet dus rapporteren over de moment opname in elke fase van de levens cyclus van de case.

In traditionele scenario's voor het Data Warehouse is het concept van moment opnamen niet mogelijk omdat de data warehouses niet zijn ontworpen om het op te nemen en de gegevens alleen een actuele weer gave van wat er gebeurt. Met Azure Cosmos DB hebben gebruikers de mogelijkheid om het concept van de tijd te implementeren, zodat er op een later tijdstip een query kan worden uitgevoerd op de gegevens en om te bepalen hoe de gegevens op een bepaald moment in de geschiedenis worden bekeken. Dit betekent dat gebruikers eenvoudig de huidige en historische weer gaven van de gegevens kunnen bekijken en er analyses op uitvoeren.

## <a name="globally-distributed-machine-learning-and-ai"></a>Wereld wijd gedistribueerde machine learning en AI

Als bedrijven concurreren zijn met snel groeiende hoeveel heden gegevens en een uitbrei ding van gegevens typen en-indelingen, is de mogelijkheid om diepere en nauw keurigere inzichten te verkrijgen in de buurt van PETA byte schaal op de hele wereld. Met native ingebouwde Apache Spark biedt Azure Cosmos DB een wereld wijd gedistribueerd analyse platform dat uitgebreide bibliotheek met machine learning-algoritmen biedt. U kunt interactieve Jupyter-notebooks gebruiken om modellen en Cluster beheer mogelijkheden te bouwen en te trainen. Deze mogelijkheden bieden u de mogelijkheid om op aanvraag zeer afgestemde en automatisch elastische Spark-clusters in te richten.

## <a name="deep-learning-on-multi-model-globally-distributed-data"></a>Dieper leren op wereld wijd gedistribueerde gegevens met meerdere modellen

Diep leren is de ideale manier om big data predictive analytics oplossingen te bieden als gegevens volume en complexiteit blijft groeien. Met een grondige kennis kunnen bedrijven de kracht van ongestructureerde en semi-gestructureerde gegevens benutten om use cases te leveren die gebruikmaken van technieken zoals AI, natuurlijke taal verwerking en meer.

## <a name="reporting-integrating-with-power-apps-power-bi"></a>Rapportage (integreren met Power apps, Power BI)

Power BI biedt interactieve visualisaties met self-service business intelligence mogelijkheden, waardoor eind gebruikers zelf rapporten en dash boards kunnen maken. U kunt met behulp van de ingebouwde Spark-Connector Power BI Desktop verbinding maken met Apache Spark clusters in Azure Cosmos DB. Met deze connector kunt u een directe query gebruiken om de verwerking van de verwerkings capaciteit naar Apache Spark in Azure Cosmos DB te verfijnen. Dit is geweldig wanneer u een enorme hoeveelheid gegevens hebt die u niet wilt laden in Power BI of als u bijna realtime analyse wilt uitvoeren.

## <a name="iot-analytics-at-global-scale"></a>IoT Analytics op wereld wijde schaal

De gegevens die worden gegenereerd op basis van toenemende netwerk Sens oren, hebben een ongekende zicht baarheid in eerdere ondoorzichtige systemen en processen. De sleutel is om inzicht te krijgen in deze torrent informatie, ongeacht waar IoT-apparaten over de hele wereld worden gedistribueerd. Azure Cosmos DB kunnen IOT-bedrijven hoge snelheids sensors en gegevens in realtime overal ter wereld analyseren. Zo kunt u de werkelijke waarde van een onderling verbonden wereld benutten om betere klant ervaringen, operationele efficiency en nieuwe omzet mogelijkheden te bieden.

## <a name="stream-processing-and-event-analytics"></a>Stroom verwerking en gebeurtenis analyse 

Van logboek bestanden tot sensor gegevens hebben bedrijven steeds de nood zaak om te omgaan met ' streams ' van gegevens. Deze gegevens arriveren in een stabiele stroom, vaak van meerdere bronnen tegelijk. Hoewel het haalbaar is om deze gegevens stromen op schijf op te slaan en ze retro actief te analyseren, kan het verstandig of belang rijk zijn om de gegevens te verwerken en ermee te reageren wanneer deze binnenkomen. Gegevens stromen met betrekking tot financiële trans acties kunnen bijvoorbeeld in realtime worden verwerkt om potentieel frauduleuze trans acties te identificeren en te weigeren.

## <a name="interactive-analytics"></a>Interactieve analyses

Naast het uitvoeren van vooraf gedefinieerde query's voor het maken van statische Dash boards voor verkoop-, productiviteits-of aandelen prijzen, wilt u mogelijk de gegevens interactief verkennen. Met interactieve analyses kunt u vragen stellen, de resultaten bekijken, de eerste vraag wijzigen op basis van het antwoord of het detail niveau in resultaten. Apache Spark in Azure Cosmos DB ondersteunt interactieve query's door te reageren en snel aan te passen.

## <a name="data-exploration-using-jupyter-notebooks"></a>Gegevens verkennen met behulp van Jupyter-notebooks

Wanneer u een nieuwe gegevensset hebt, moet u uw gegevens controleren voordat u de modellen en tests uitvoert. Met andere woorden, u moet experimentele gegevens analyse uitvoeren. Gegevens onderzoek kan verschillende beslissingen nemen. U kunt bijvoorbeeld informatie vinden over de methoden die geschikt zijn voor het gebruik van uw gegevens, ongeacht of de gegevens voldoen aan bepaalde model veronderstellingen, of de gegevens moeten worden gereinigd, gestructureerd, enzovoort. Met behulp van de ingebouwde Jupyter-notebooks en Apache Spark van de Azure Cosmos DB kunt u snel en effectief experimentele gegevens analyse uitvoeren op transactionele en analytische gegevens.

## <a name="next-steps"></a>Volgende stappen

Ga naar de volgende artikelen om aan de slag te gaan met deze use cases:

* [Ingebouwde Jupyter-notebooks in Azure Cosmos DB](cosmosdb-jupyter-notebooks.md)
* [Notebooks inschakelen voor Azure Cosmos-accounts](enable-notebooks.md)
* [Een notitie blok maken om gegevens te analyseren en te visualiseren](create-notebook-visualize-data.md)