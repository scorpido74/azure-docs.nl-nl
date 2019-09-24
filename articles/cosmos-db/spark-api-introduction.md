---
title: Inleiding tot de ingebouwde operationele analyse in Azure Cosmos DB met Apache Spark
description: Meer informatie over het gebruik van de ingebouwde ondersteuning voor Apache Spark in Azure Cosmos DB voor het uitvoeren van operationele analyses en AI
ms.service: cosmos-db
ms.topic: overview
ms.date: 08/01/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: 0f070cb9a6e300dad0ec9e0b393b09b7f22d2942
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212590"
---
# <a name="built-in-operational-analytics-in-azure-cosmos-db-with-apache-spark-preview"></a>Ingebouwde operationele analyse in Azure Cosmos DB met Apache Spark (preview-versie) 

Met de ingebouwde ondersteuning voor Apache Spark in Azure Cosmos DB kunt u analyses uitvoeren vanuit Apache Spark van uw gegevens die zijn opgeslagen in een Azure Cosmos-account. Het biedt de systeem eigen ondersteuning voor Apache Spark taken om rechtstreeks uit te voeren op uw wereld wijd gedistribueerde Cosmos-data bases. Met deze mogelijkheden kunnen ontwikkel aars, gegevens technici en gegevens wetenschappers Azure Cosmos DB gebruiken als een flexibel, schaalbaar en geschikt gegevens platform voor het uitvoeren van zowel **OLTP-als OLAP-HTAP** -workloads. 

Spark Compute is automatisch beschikbaar in alle Azure-regio's die zijn gekoppeld aan uw Azure Cosmos-account. Spark-taken gebruiken de functionaliteit voor meerdere masters van het Azure Cosmos DB en kunnen de lokale replica's in elke regio schrijven of er query's op uitvoeren. 

> [!NOTE]
> De ingebouwde ondersteuning voor Apache Spark in Azure Cosmos DB is momenteel een beperkte preview-versie. Als u zich wilt aanmelden voor de preview, gaat u naar [registreren voor de voorbeeld pagina](https://portal.azure.com/?feature.customportal=false#create/Microsoft.DocumentDB). 

De Apache Spark ondersteuning in Azure Cosmos DB biedt de volgende voor delen:

* U kunt de snelste tijd om inzicht te krijgen in de geografische, gedistribueerde gebruikers en gegevens.

* U kunt de architectuur van uw oplossing vereenvoudigen en de [totale eigendoms kosten](total-cost-ownership.md) (TCO) verlagen. Het systeem heeft het minste aantal onderdelen voor gegevens verwerking en vermijdt overbodige gegevens verplaatsing.

* Hiermee maakt u een grens voor [beveiliging](secure-access-to-data.md), [naleving](compliance.md)en controle die alle gegevens onder beheer omvat.

* Biedt ' always on ' of ' [high available](high-availability.md) ' End-User Analytics die wordt ondersteund door strikte sla's.

![Ondersteuning voor Apache Spark in Azure Cosmos DB visualisatie](./media/spark-api-introduction/spark-api-visualization.png)
 
Met de Apache Spark ondersteuning in Azure Cosmos DB kunt u oplossingen maken en implementeren, zoals AI en diepe leer modellen, predictive analytics, aanbevelingen, IoT, klant 360, fraude detectie, tekst sentiment, clickstream analyse. Deze oplossingen werken rechtstreeks op uw Azure Cosmos DB gegevens.

U kunt batch-en streaming ETL-taak in Azure Cosmos DB instellen, zonder dat u buiten de database service hoeft te gaan of extra Compute-Services hoeft toe te voegen. U kunt de compute-omgeving op een flexibele manier schalen wanneer u de ETL-taak moet uitvoeren en deze opnieuw wilt schalen wanneer de taak is voltooid.

De Apache Spark ondersteuning in Azure Cosmos DB biedt ingebouwde Machine Learning ondersteuning in de Apache Spark Runtimes. De runtimes omvatten Spark MLLib, micro Soft Machine Learning voor Spark, Azure Machine Learning en Cognitive Services. Met deze functies, gegevens wetenschappers, gegevens technici en gegevens analisten kunnen machine learning modellen direct binnen Azure Cosmos DB worden gebouwd en geoperationeel makend, in een fractie van tijd en met de lage kosten.


## <a name="key-benefits"></a>Belangrijkste voordelen

### <a name="globally-distributed-low-latency-operational-analytics-and-ai"></a>Wereld wijd gedistribueerde, operationele analyses met lage latentie en AI

Met Apache Spark op de wereld wijd gedistribueerde Azure Cosmos-Data Base kunt u nu snel en eenvoudig inzicht krijgen in de hele wereld. Azure Cosmos DB maakt **wereld wijde, gedistribueerde, lage latentie operationele analyses** mogelijk op elastische schaal met drie belang rijke technieken:

* Omdat uw Azure Cosmos-data base wereld wijd is gedistribueerd, worden alle gegevens lokaal opgenomen, waar de producenten van de gegevens (bijvoorbeeld gebruikers) zich bevinden. De query's worden afgehandeld op basis van de lokale replica's die het dichtst bij de producenten en de consumenten van gegevens, ongeacht waar ze zich op de wereld bevinden. 

* Al uw analytische query's worden direct uitgevoerd op de geïndexeerde gegevens die zijn opgeslagen in de gegevens partities, zonder dat er onnodige gegevens verplaatsing nodig is. 

* Omdat Spark naast Azure Cosmos DB, worden er minder tussenliggende vertalingen en gegevens verplaatsingen uitgevoerd, wat resulteert in betere prestaties en schaal baarheid.

### <a name="unified-serverless-experience-for-apache-spark"></a>Uniforme serverloze ervaring voor Apache Spark

Als een Data Base met meerdere modellen, wordt de ondersteuning voor OSS-Api's door Azure Cosmos DB nu uitgebreid met een **uniforme serverloze ervaring voor Apache Spark** met gegevens modellen voor sleutel waarden, document, grafieken en kolom familie. Verschillende gegevens modellen worden ondersteund met behulp van MongoDB, Cassandra, Gremlin, Etcd en SQL Api's, die allemaal op dezelfde onderliggende gegevens werken. 

Met de ondersteuning van Apache Spark in Azure Cosmos DB kunt u toepassingen die zijn geschreven in scala, Python, Java en verschillende nauw geïntegreerde bibliotheken voor SQL gebruiken. Deze bibliotheken omvatten ([Spark SQL](https://spark.apache.org/sql/)), machine learning (Spark [MLlib](https://spark.apache.org/mllib/)), verwerking van streams ([Spark Structured streaming](https://spark.apache.org/streaming/)) en grafiek verwerking (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)). Deze hulpprogram ma's maken het gemakkelijker om de Apache Spark te gebruiken voor verschillende use cases. U hoeft niet te omgaan met het beheren van Spark-of Spark-clusters. U kunt de vertrouwde Apache Spark Api's en **Jupyter-notebooks** voor analyses en SQL API of een van de OSS NoSQL-Api's zoals Cassandra voor transactionele verwerking op dezelfde onderliggende gegevens tegelijk gebruiken.

### <a name="no-schema-or-index-management"></a>Geen schema- of indexbeheer

In tegens telling tot traditionele analytische data bases, met Azure Cosmos DB, gegevens technici en gegevens wetenschappers hoeven niet meer te worden geconfronteerd met het ingewikkelde schema-en index beheer. Voor de data base-engine in Azure Cosmos DB zijn geen expliciete schema-of index beheer vereist. alle opgenomen gegevens kunnen automatisch worden geïndexeerd om de Apache Spark query's snel te kunnen verwerken. 

### <a name="consistency-choices"></a>Consistentie opties

Omdat de Apache Spark taken worden uitgevoerd in de gegevens partities van uw Azure Cosmos-data base, krijgen de query's de [vijf goed gedefinieerde consistentie opties](consistency-levels.md). Deze consistentie modellen bieden de flexibiliteit om strikte consistentie te bieden voor de meest nauw keurige resultaten voor machine learning algoritmen zonder de latentie en hoge Beschik baarheid in gevaar te brengen. 

### <a name="comprehensive-slas"></a>Uitgebreide SLA's

De Apache Spark-taken hebben de Azure Cosmos DB voor delen zoals toonaangevende uitgebreide [sla's](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) (99,999), zonder enige overhead van het beheer van afzonderlijke Apache Spark clusters. Deze Sla's omvatten de door Voer, latentie bij het 99e percentiel, consistentie en hoge Beschik baarheid. 

### <a name="mixed-workloads"></a>Gemengde werk belastingen

De integratie van Apache Spark in Azure Cosmos DB zorgt voor de transactionele en analytische schei ding, een van de belangrijkste knel punten van klanten bij het bouwen van Cloud toepassingen op wereld wijde schaal. 

## <a name="scenarios-for-azure-cosmos-db-spark-support"></a>Scenario's voor Azure Cosmos DB Spark-ondersteuning

### <a name="retail-and-consumer-goods"></a>Retail-en consumenten goederen

U kunt ondersteuning voor Spark in Azure Cosmos DB gebruiken om realtime-aanbevelingen en aanbiedingen te leveren. U kunt klanten helpen bij het detecteren van de items die ze nodig hebben met real-time personalisatie en product aanbevelingen.

* U kunt de ingebouwde Machine Learning ondersteuning van de Apache Spark runtime gebruiken om realtime-aanbevelingen in de product catalogussen te genereren.

* U kunt op stroom gegevens, aankoop gegevens en klant gegevens klikken om aanbevelingen te geven voor de levens duur van het apparaat.

* Met behulp van de globale distributie functie van de Azure Cosmos DB kunnen grote hoeveel heden product gegevens die worden verdeeld over regio's, in milliseconden worden geanalyseerd.

* U kunt snel inzichten verkrijgen voor de geografisch gedistribueerde gebruikers en gegevens. U kunt de conversie snelheid van de promotie verbeteren door de juiste advertentie op het juiste moment naar de juiste gebruiker te sturen.

* U kunt gebruikmaken van de ingebouwde Spark streaming-mogelijkheid om Live gegevens te verrijken door deze te combi neren met statische klant gegevens. Op deze manier kunt u meer gepersonaliseerde en gerichte advertenties leveren in realtime en in de context van wat klanten doen.

In de volgende afbeelding ziet u hoe Azure Cosmos DB Spark-ondersteuning wordt gebruikt voor het optimaliseren van prijzen en promoties:

![Spark-ondersteuning van Azure Cosmos DB voor het optimaliseren van prijzen en promoties](./media/spark-api-introduction/optimize-pricing-and-promotions.png)


In de volgende afbeelding ziet u hoe Azure Cosmos DB Spark-ondersteuning wordt gebruikt in realtime aanbevolen Engine:

![Azure Cosmos DB Spark-ondersteuning in real-time aanbevelings engine](./media/spark-api-introduction/real-time-recommendation-engine.png)

### <a name="manufacturing-and-iot"></a>Productie en IoT

Met het in-builief Analytics-platform van Azure Cosmos DB kunt u real-time analyse van IoT-gegevens van miljoenen apparaten op wereld wijde schaal mogelijk maken. U kunt moderne innovaties maken zoals het voors pellen van weers patronen, voorspellende analyses en energie optimalisaties.

* Met behulp van Azure Cosmos DB kunt u gegevens gebruiken zoals metrische waarden voor realtime en weers factoren en vervolgens slimme grid Analytics Toep assen om de prestaties van verbonden apparaten in het veld te optimaliseren. Slimme grid Analytics is de sleutel om de operationele kosten te beheren, om de betrouw baarheid van het raster te verbeteren en om persoonlijke energie services aan consumenten te leveren.

De volgende afbeelding laat zien hoe de Spark-ondersteuning van Azure Cosmos DB wordt gebruikt voor het lezen van metrische gegevens van IoT-apparaten en het Toep assen van slimme grid Analytics:

![Spark-ondersteuning van Azure Cosmos DB voor het lezen van metrische gegevens van IoT-apparaten](./media/spark-api-introduction/read-metrics-from-iot-devices.png)


### <a name="predictive-maintenance"></a>Voorspeld onderhoud

* Het onderhouden van assets, zoals compressoren die worden gebruikt in kleine boor platforms tot diepe water, is een ingewikkeld bedrijf. Deze activa bevinden zich overal ter wereld en genereren PETA bytes gegevens. U kunt met behulp van Azure Cosmos DB een end-to-end gegevens pijplijn maken die gebruikmaakt van Spark-streaming voor het verwerken van grote hoeveel heden sensor telemetrie, het opslaan van Asset-onderdelen en sensor toewijzings gegevens.

* U kunt machine learning modellen bouwen en implementeren om activa fouten te voors pellen voordat ze plaatsvinden en onderhouds werk orders te verlenen voordat de fout optreedt.

In de volgende afbeelding ziet u hoe de Spark-ondersteuning van Azure Cosmos DB wordt gebruikt voor het bouwen van een voorspellend onderhouds systeem:

![Spark-ondersteuning van Azure Cosmos DB voor het bouwen van een voorspellend onderhouds systeem](./media/spark-api-introduction/predictive-maintenance-system.png)

In de volgende afbeelding ziet u hoe de Spark-ondersteuning van Azure Cosmos DB wordt gebruikt voor het bouwen van een systeem voor diagnose van de real-time:

![Spark-ondersteuning van Azure Cosmos DB voor het bouwen van een real-time diagnose systeem voor Voer tuigen](./media/spark-api-introduction/real-time-vehicle-diagnostic-system.png)

### <a name="gaming"></a>Gaming

* Met ingebouwde ondersteuning voor Spark kunt u met Azure Cosmos DB eenvoudig geavanceerde analyse-en machine learning modellen bouwen, schalen en implementeren in enkele minuten om de beste beleving van games mogelijk te maken.

* U kunt speler-, aankoop-en gedrags gegevens analyseren om relevante gepersonaliseerde aanbiedingen te maken om hoge conversie tarieven te bereiken.

* Met Spark machine learning kunt u informatie over het telemetrie van games analyseren en verkrijgen. U kunt een diagnose stellen en voor komen dat er trage laad tijden en spel problemen optreden.

In de volgende afbeelding ziet u hoe de Spark-ondersteuning van Azure Cosmos DB wordt gebruikt in gaming Analytics:

![Ondersteuning voor Spark van Azure Cosmos DB in gaming Analytics](./media/spark-api-introduction/gaming-analytics.png)

## <a name="next-steps"></a>Volgende stappen

* Zie het artikel [overzicht](introduction.md) voor meer informatie over de voor delen van Azure Cosmos db.
* [Aan de slag met de Azure Cosmos DB-API voor MongoDB](mongodb-introduction.md)
* [Aan de slag met de Azure Cosmos DB Cassandra-API](cassandra-introduction.md)
* [Aan de slag met de Azure Cosmos DB Gremlin-API](graph-introduction.md)
* [Aan de slag met de Azure Cosmos DB Table-API](table-introduction.md)




