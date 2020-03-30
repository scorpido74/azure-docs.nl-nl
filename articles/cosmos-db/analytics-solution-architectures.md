---
title: Oplossingen met wereldwijd gedistribueerde analyses in Azure Cosmos DB.
description: Meer informatie over de oplossingen die kunnen worden gebouwd met behulp van de wereldwijd gedistribueerde analyses in Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: d0b5042c1401a95cc75f4c01ace843659b6babfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73681746"
---
# <a name="solutions-using-globally-distributed-analytics-in-azure-cosmos-db"></a>Oplossingen met wereldwijd gedistribueerde analyses in Azure Cosmos DB

In dit artikel worden de oplossingen beschreven die kunnen worden gebouwd met behulp van de wereldwijd gedistribueerde analyses in Azure Cosmos DB.

## <a name="retail-and-consumer-goods"></a>Detailhandel en consumptiegoederen

U Spark-ondersteuning in Azure Cosmos DB gebruiken om realtime aanbevelingen en aanbiedingen te leveren. U klanten helpen bij het ontdekken van de items die ze nodig hebben met real-time personalisatie en productaanbevelingen.

* U de ingebouwde Machine Learning-ondersteuning van de Apache Spark-runtime gebruiken om realtime aanbevelingen te genereren in de productcatalogi.

* U de gegevens van de klikstroom, aankoopgegevens en klantgegevens delven om gerichte aanbevelingen te geven die de levensduurwaarde bepalen.

* Met behulp van de wereldwijde distributiefunctie van Azure Cosmos DB kunnen grote hoeveelheden productgegevens die zich over regio's verspreiden, in milliseconden worden geanalyseerd.

* U snel inzichten krijgen voor de geografisch verspreide gebruikers en gegevens. U de promotieconversieratio verbeteren door de juiste advertentie op het juiste moment aan de juiste gebruiker weer te geven.

* U gebruikmaken van de ingebouwde Spark-streamingmogelijkheid om live gegevens te verrijken door deze te combineren met statische klantgegevens. Op deze manier u meer gepersonaliseerde en gerichte advertenties weergeven in realtime en in context met wat klanten doen.

In de volgende afbeelding ziet u hoe azure cosmos DB Spark-ondersteuning wordt gebruikt om prijzen en promoties te optimaliseren:

![Azure Cosmos DB's Spark-ondersteuning voor het optimaliseren van prijzen en promoties](./media/analytics-solution-architectures/optimize-pricing-and-promotions.png)


In de volgende afbeelding ziet u hoe Azure Cosmos DB Spark-ondersteuning wordt gebruikt in de realtime aanbevelingsengine:

![Azure Cosmos DB Spark-ondersteuning in realtime aanbevelingsengine](./media/analytics-solution-architectures/real-time-recommendation-engine.png)

## <a name="manufacturing-and-iot"></a>Productie en IoT

Met het ingebouwde analyseplatform van Azure Cosmos DB u realtime iot-gegevens van miljoenen apparaten op wereldwijde schaal analyseren. U moderne innovaties maken, zoals het voorspellen van weerpatronen, voorspellende analyses en energie-optimalisaties.

* Door Azure Cosmos DB te gebruiken, u gegevens zoals realtime assetmetrics en weersfactoren delven en vervolgens smart grid-analyses toepassen om de prestaties van verbonden apparaten in het veld te optimaliseren. Smart grid analytics is de sleutel om de bedrijfskosten te beheersen, de betrouwbaarheid van het net te verbeteren en gepersonaliseerde energiediensten aan consumenten te leveren.

In de volgende afbeelding ziet u hoe de Spark-ondersteuning van Azure Cosmos DB wordt gebruikt om statistieken van IoT-apparaten te lezen en smart grid-analyses toe te passen:

![Azure Cosmos DB's Spark-ondersteuning voor het lezen van statistieken van IoT-apparaten](./media/analytics-solution-architectures/read-metrics-from-iot-devices.png)

## <a name="predictive-maintenance"></a>Voorspellend onderhoud

* Het onderhouden van activa zoals compressoren die worden gebruikt in kleine boorplatforms tot diepwaterplatforms is een complexe onderneming. Deze activa bevinden zich over de hele wereld en genereren petabytes aan gegevens. Met Azure Cosmos DB u een end-to-end voorspellende gegevenspijplijn bouwen die Spark-streaming gebruikt om grote hoeveelheden sensortelemetrie, assetonderdelen en sensortoewijzingsgegevens te verwerken.

* U machine learning-modellen bouwen en implementeren om foute activa te voorspellen voordat deze plaatsvinden en onderhoudswerkorders uit te geven voordat de fout optreedt.

In de volgende afbeelding ziet u hoe de Spark-ondersteuning van Azure Cosmos DB wordt gebruikt om een voorspellend onderhoudssysteem te bouwen:

![Azure Cosmos DB's Spark-ondersteuning voor het bouwen van een voorspellend onderhoudssysteem](./media/analytics-solution-architectures/predictive-maintenance-system.png)

In de volgende afbeelding ziet u hoe de Spark-ondersteuning van Azure Cosmos DB wordt gebruikt om een realtime voertuigdiagnosesysteem te bouwen:

![Azure Cosmos DB's Spark-ondersteuning voor het bouwen van een realtime voertuigdiagnosesysteem](./media/analytics-solution-architectures/real-time-vehicle-diagnostic-system.png)

## <a name="gaming"></a>Gaming

* Met ingebouwde Spark-ondersteuning u met Azure Cosmos DB geavanceerde analyse- en machine learning-modellen eenvoudig bouwen, schalen en implementeren om de best mogelijke game-ervaring te bouwen, te schalen en te implementeren.

* U spelers-, aankoop- en gedragsgegevens analyseren om relevante gepersonaliseerde aanbiedingen te maken om hoge conversiepercentages te bereiken.

* Met Spark machine learning u informatie over telemetriegegevens van games analyseren en verkrijgen. U trage laadtijden en problemen in de game diagnosticeren en voorkomen.

In de volgende afbeelding ziet u hoe de Spark-ondersteuning van Azure Cosmos DB wordt gebruikt in gaming-analyses:

![Azure Cosmos DB's Spark-ondersteuning voor gaminganalytics](./media/analytics-solution-architectures/gaming-analytics.png)

## <a name="next-steps"></a>Volgende stappen

* Zie het [overzichtsartikel](introduction.md) voor meer informatie over de voordelen van Azure Cosmos DB.
* [Aan de slag met de Azure Cosmos DB API voor MongoDB](mongodb-introduction.md)
* [Aan de slag met de Azure Cosmos DB Cassandra API](cassandra-introduction.md)
* [Aan de slag met de Azure Cosmos DB Gremlin API](graph-introduction.md)
* [Aan de slag met de Azure Cosmos DB Table API](table-introduction.md)
