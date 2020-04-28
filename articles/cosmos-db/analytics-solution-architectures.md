---
title: Oplossingen die gebruikmaken van wereld wijd gedistribueerde analyses in Azure Cosmos DB.
description: Meer informatie over de oplossingen die kunnen worden gebouwd met behulp van de wereld wijd gedistribueerde analyses in Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: d0b5042c1401a95cc75f4c01ace843659b6babfe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73681746"
---
# <a name="solutions-using-globally-distributed-analytics-in-azure-cosmos-db"></a>Oplossingen die gebruikmaken van wereld wijd gedistribueerde analyses in Azure Cosmos DB

In dit artikel worden de oplossingen beschreven die kunnen worden gebouwd met behulp van de wereld wijd gedistribueerde analyse in Azure Cosmos DB.

## <a name="retail-and-consumer-goods"></a>Retail-en consumenten goederen

U kunt ondersteuning voor Spark in Azure Cosmos DB gebruiken om realtime-aanbevelingen en aanbiedingen te leveren. U kunt klanten helpen bij het detecteren van de items die ze nodig hebben met real-time personalisatie en product aanbevelingen.

* U kunt de ingebouwde Machine Learning ondersteuning van de Apache Spark runtime gebruiken om realtime-aanbevelingen in de product catalogussen te genereren.

* U kunt op stroom gegevens, aankoop gegevens en klant gegevens klikken om aanbevelingen te geven voor de levens duur van het apparaat.

* Met behulp van de globale distributie functie van de Azure Cosmos DB kunnen grote hoeveel heden product gegevens die worden verdeeld over regio's, in milliseconden worden geanalyseerd.

* U kunt snel inzichten verkrijgen voor de geografisch gedistribueerde gebruikers en gegevens. U kunt de conversie snelheid van de promotie verbeteren door de juiste advertentie op het juiste moment naar de juiste gebruiker te sturen.

* U kunt gebruikmaken van de ingebouwde Spark streaming-mogelijkheid om Live gegevens te verrijken door deze te combi neren met statische klant gegevens. Op deze manier kunt u meer gepersonaliseerde en gerichte advertenties leveren in realtime en in de context van wat klanten doen.

In de volgende afbeelding ziet u hoe Azure Cosmos DB Spark-ondersteuning wordt gebruikt voor het optimaliseren van prijzen en promoties:

![Spark-ondersteuning van Azure Cosmos DB voor het optimaliseren van prijzen en promoties](./media/analytics-solution-architectures/optimize-pricing-and-promotions.png)


In de volgende afbeelding ziet u hoe Azure Cosmos DB Spark-ondersteuning wordt gebruikt in realtime aanbevolen Engine:

![Azure Cosmos DB Spark-ondersteuning in real-time aanbevelings engine](./media/analytics-solution-architectures/real-time-recommendation-engine.png)

## <a name="manufacturing-and-iot"></a>Productie en IoT

Met het in-builief Analytics-platform van Azure Cosmos DB kunt u real-time analyse van IoT-gegevens van miljoenen apparaten op wereld wijde schaal mogelijk maken. U kunt moderne innovaties maken zoals het voors pellen van weers patronen, voorspellende analyses en energie optimalisaties.

* Met behulp van Azure Cosmos DB kunt u gegevens gebruiken zoals metrische waarden voor realtime en weers factoren en vervolgens slimme grid Analytics Toep assen om de prestaties van verbonden apparaten in het veld te optimaliseren. Slimme grid Analytics is de sleutel om de operationele kosten te beheren, om de betrouw baarheid van het raster te verbeteren en om persoonlijke energie services aan consumenten te leveren.

De volgende afbeelding laat zien hoe de Spark-ondersteuning van Azure Cosmos DB wordt gebruikt voor het lezen van metrische gegevens van IoT-apparaten en het Toep assen van slimme grid Analytics:

![Spark-ondersteuning van Azure Cosmos DB voor het lezen van metrische gegevens van IoT-apparaten](./media/analytics-solution-architectures/read-metrics-from-iot-devices.png)

## <a name="predictive-maintenance"></a>Voorspellend onderhoud

* Het onderhouden van assets, zoals compressoren die worden gebruikt in kleine boor platforms tot diepe water, is een ingewikkeld bedrijf. Deze activa bevinden zich overal ter wereld en genereren PETA bytes gegevens. U kunt met behulp van Azure Cosmos DB een end-to-end gegevens pijplijn maken die gebruikmaakt van Spark-streaming voor het verwerken van grote hoeveel heden sensor telemetrie, het opslaan van Asset-onderdelen en sensor toewijzings gegevens.

* U kunt machine learning modellen bouwen en implementeren om activa fouten te voors pellen voordat ze plaatsvinden en onderhouds werk orders te verlenen voordat de fout optreedt.

In de volgende afbeelding ziet u hoe de Spark-ondersteuning van Azure Cosmos DB wordt gebruikt voor het bouwen van een voorspellend onderhouds systeem:

![Spark-ondersteuning van Azure Cosmos DB voor het bouwen van een voorspellend onderhouds systeem](./media/analytics-solution-architectures/predictive-maintenance-system.png)

In de volgende afbeelding ziet u hoe de Spark-ondersteuning van Azure Cosmos DB wordt gebruikt voor het bouwen van een systeem voor diagnose van de real-time:

![Spark-ondersteuning van Azure Cosmos DB voor het bouwen van een real-time diagnose systeem voor Voer tuigen](./media/analytics-solution-architectures/real-time-vehicle-diagnostic-system.png)

## <a name="gaming"></a>Gaming

* Met ingebouwde ondersteuning voor Spark kunt u met Azure Cosmos DB eenvoudig geavanceerde analyse-en machine learning modellen bouwen, schalen en implementeren in enkele minuten om de beste beleving van games mogelijk te maken.

* U kunt speler-, aankoop-en gedrags gegevens analyseren om relevante gepersonaliseerde aanbiedingen te maken om hoge conversie tarieven te bereiken.

* Met Spark machine learning kunt u informatie over het telemetrie van games analyseren en verkrijgen. U kunt een diagnose stellen en voor komen dat er trage laad tijden en spel problemen optreden.

In de volgende afbeelding ziet u hoe de Spark-ondersteuning van Azure Cosmos DB wordt gebruikt in gaming Analytics:

![Ondersteuning voor Spark van Azure Cosmos DB in gaming Analytics](./media/analytics-solution-architectures/gaming-analytics.png)

## <a name="next-steps"></a>Volgende stappen

* Zie het artikel [overzicht](introduction.md) voor meer informatie over de voor delen van Azure Cosmos db.
* [Aan de slag met de Azure Cosmos DB-API voor MongoDB](mongodb-introduction.md)
* [Aan de slag met de Azure Cosmos DB Cassandra-API](cassandra-introduction.md)
* [Aan de slag met de Azure Cosmos DB Gremlin-API](graph-introduction.md)
* [Aan de slag met de Azure Cosmos DB Table-API](table-introduction.md)
