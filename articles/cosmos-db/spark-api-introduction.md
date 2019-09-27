---
title: Inleiding tot de ingebouwde operationele analyse in Azure Cosmos DB met Apache Spark
description: Meer informatie over het gebruik van de ingebouwde ondersteuning voor Apache Spark in Azure Cosmos DB voor het uitvoeren van operationele analyses en AI
ms.service: cosmos-db
ms.topic: overview
ms.date: 09/26/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: 952dd084bccd67a0a8833002d73d3aaf1d5dfb25
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338616"
---
# <a name="built-in-operational-analytics-in-azure-cosmos-db-with-apache-spark"></a>Ingebouwde operationele analyse in Azure Cosmos DB met Apache Spark

Met Azure Cosmos DB kunt u wereld wijd gedistribueerde, lage latentie analyse en AI op transactionele gegevens uitvoeren. Met de ingebouwde ondersteuning voor Apache Spark-en Jupyter-notebooks, Azure Cosmos DB helpt de tijd te beperken tot inzichten. Omdat de gegevens worden opgenomen, geleverd en Analytics, worden uitgevoerd op basis van de lokale database replica in een Azure-regio. U kunt Apache Spark query's rechtstreeks uitvoeren op geïndexeerde gegevens van meerdere modellen die zijn opgeslagen in uw gegevens partities.

Met de ingebouwde ondersteuning voor Apache Spark in Azure Cosmos DB kunt u analyses uitvoeren vanuit Apache Spark van uw gegevens die zijn opgeslagen in een Azure Cosmos-account. Het biedt de systeem eigen ondersteuning voor Apache Spark taken om rechtstreeks uit te voeren op uw wereld wijd gedistribueerde Cosmos-data bases. Met deze mogelijkheden kunnen ontwikkel aars, gegevens technici en gegevens wetenschappers Azure Cosmos DB gebruiken als een flexibel, schaalbaar en geschikt gegevens platform voor het uitvoeren van zowel **OLTP-als OLAP-HTAP** -workloads.

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

### <a name="low-latency-operational-analytics-and-ai"></a>Operationele analyse en AI met lage latentie

Met Apache Spark op de wereld wijd gedistribueerde Azure Cosmos-Data Base kunt u nu snel en eenvoudig inzicht krijgen in de hele wereld. Azure Cosmos DB maakt **wereld wijde, gedistribueerde, lage latentie operationele analyses** mogelijk op elastische schaal met drie belang rijke technieken:

* Omdat uw Azure Cosmos-data base wereld wijd is gedistribueerd, worden alle gegevens lokaal opgenomen, waar de producenten van de gegevens (bijvoorbeeld gebruikers) zich bevinden. De query's worden afgehandeld op basis van de lokale replica's die het dichtst bij de producenten en de consumenten van gegevens, ongeacht waar ze zich op de wereld bevinden.

* Al uw analytische query's worden direct uitgevoerd op de geïndexeerde gegevens die zijn opgeslagen in de gegevens partities, zonder dat er onnodige gegevens verplaatsing nodig is.

* Omdat Spark naast Azure Cosmos DB, worden er minder tussenliggende vertalingen en gegevens verplaatsingen uitgevoerd, wat resulteert in betere prestaties en schaal baarheid.

* Alle Azure Cosmos DB kern functies, zoals multi-master, automatische failover, Beschikbaarheidszones, enzovoort, zijn beschikbaar voor ingebouwde Apache Spark in Azure Cosmos DB.

### <a name="unified-serverless-experience-for-apache-spark"></a>Uniforme serverloze ervaring voor Apache Spark

Als een Data Base met meerdere modellen, wordt de ondersteuning voor OSS-Api's door Azure Cosmos DB nu uitgebreid met een **uniforme serverloze ervaring voor Apache Spark** met gegevens modellen voor sleutel waarden, document, grafieken en kolom familie. Verschillende gegevens modellen worden ondersteund met behulp van MongoDB, Cassandra, Gremlin, Etcd en SQL Api's, die allemaal op dezelfde onderliggende gegevens werken. 

Met de ondersteuning van Apache Spark in Azure Cosmos DB kunt u toepassingen die zijn geschreven in scala, Python, Java en verschillende nauw geïntegreerde bibliotheken voor SQL gebruiken. Deze bibliotheken omvatten ([Spark SQL](https://spark.apache.org/sql/)), machine learning (Spark [MLlib](https://spark.apache.org/mllib/)), verwerking van streams ([Spark Structured streaming](https://spark.apache.org/streaming/)) en grafiek verwerking (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)). Deze hulpprogram ma's maken het gemakkelijker om de Apache Spark te gebruiken voor verschillende use cases. U hoeft niet te omgaan met het beheren van Spark-of Spark-clusters. U kunt de vertrouwde Apache Spark Api's en **Jupyter-notebooks** voor analyses en SQL API of een van de OSS NoSQL-Api's zoals Cassandra voor transactionele verwerking op dezelfde onderliggende gegevens tegelijk gebruiken.

### <a name="no-schema-or-index-management"></a>Geen schema- of indexbeheer

In tegens telling tot traditionele analytische data bases, met Azure Cosmos DB, gegevens technici en gegevens wetenschappers hoeven niet meer te worden geconfronteerd met het ingewikkelde schema-en index beheer. Voor de data base-engine in Azure Cosmos DB zijn geen expliciete schema-of index beheer vereist. alle opgenomen gegevens kunnen automatisch worden geïndexeerd om de Apache Spark query's snel te kunnen verwerken.

### <a name="consistency-choices"></a>Consistentie opties

Omdat de Apache Spark taken worden uitgevoerd in de gegevens partities van uw Azure Cosmos-data base, krijgen de query's de [vijf goed gedefinieerde consistentie opties](consistency-levels.md). Deze consistentie modellen bieden de flexibiliteit om strikte consistentie te bieden voor de meest nauw keurige resultaten voor machine learning algoritmen zonder de latentie en hoge Beschik baarheid in gevaar te brengen.

### <a name="comprehensive-slas"></a>Uitgebreide SLA's

De Apache Spark-taken hebben de Azure Cosmos DB voor delen zoals toonaangevende uitgebreide [sla's](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) (99,999), zonder enige overhead van het beheer van afzonderlijke Apache Spark clusters. Deze Sla's omvatten de door Voer, latentie bij het 99e percentiel, consistentie en hoge Beschik baarheid. 

### <a name="mixed-operational-and-analytical-workloads-with-complete-isolation"></a>Gemengde operationele en analytische werk belastingen met volledige isolatie

De integratie van Apache Spark in Azure Cosmos DB zorgt voor de transactionele en analytische schei ding, een van de belangrijkste knel punten van klanten bij het bouwen van Cloud toepassingen op wereld wijde schaal. De OLTP-en OLAP-werk belastingen worden naast elkaar uitgevoerd en zijn niet van invloed op elkaar.

### <a name="low-latency-analytical-and-transactional-storage"></a>Analytische en transactionele opslag met een lage latentie

Azure Cosmos DB systeem eigen gegevens opslaat in twee afzonderlijke opslag lagen: transactionele (Row-georiënteerde) en analytische opslag (kolom-georiënteerd, in Apache Parquet-bestands indeling). De gegevens worden in elke laag globaal gerepliceerd en Hiermee kunnen gebruikers onafhankelijk gegevens in deze lagen beheren op basis van het Bewaar beleid.

Deze opslag architectuur maakt zowel bedrijfskritische transactionele als analytische werk belastingen op dezelfde wereld wijd gedistribueerde gegevens mogelijk. Bij Cosmos DB hebt u geen ETL-bewerkingen nodig of kunt u geen onnodige gegevens verplaatsing uitvoeren. U kunt gewoon transactionele en analytische werk belastingen uitvoeren op dezelfde onderliggende gegevens. De transactionele werk belastingen kunnen gebruikmaken van de bekende transactionele toegangs-Api's, zoals SQL, Cassandra, MongoDB, Gremlin en Etcd. De analyse-en AI-workloads kunnen gebruikmaken van de ingebouwde Apache Spark SQL, ML frameworks en de volledige Apache Spark hulpprogramma keten.

### <a name="snapshots-and-historical-analytical-queries"></a>Moment opnamen en historische analytische query's

U kunt moment opnamen op aanvraag of op schema maken van de in kolom vorm gecomprimeerde gegevens die zijn opgeslagen in de analytische laag om analytische query's rechtstreeks uit te voeren op een specifieke moment opname. Deze functie maakt Flashback of tijdrovende analytische query's, terugdraai acties, volledige historische controle sporen en reproduceer bare machine learning-en AI-experimenten.

## <a name="next-steps"></a>Volgende stappen

* Zie het artikel [overzicht](introduction.md) voor meer informatie over de voor delen van Azure Cosmos db.
* [Aan de slag met de Azure Cosmos DB-API voor MongoDB](mongodb-introduction.md)
* [Aan de slag met de Azure Cosmos DB Cassandra-API](cassandra-introduction.md)
* [Aan de slag met de Azure Cosmos DB Gremlin-API](graph-introduction.md)
* [Aan de slag met de Azure Cosmos DB Table-API](table-introduction.md)