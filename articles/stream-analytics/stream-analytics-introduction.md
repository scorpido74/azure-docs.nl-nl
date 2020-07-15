---
title: Overzicht van Azure Stream Analytics
description: Lees over Stream Analytics, een beheerde service waarmee u streaminggegevens van het Internet of Things (IoT) in realtime kun analyseren.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 07/6/2020
ms.openlocfilehash: d62fd0a23a5f5553f27c7a399eb17d06d427a6f3
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108784"
---
# <a name="what-is-azure-stream-analytics"></a>Wat is Azure Stream Analytics?

Azure Stream Analytics is een realtime engine voor het analyseren en verwerken van complexe gebeurtenissen. Het is ontworpen om grote volumes van snelle streaminggegevens van verschillende bronnen tegelijkertijd te analyseren en verwerken. U kunt patronen en verbanden identificeren in informatie die wordt verkregen uit verschillende invoerbronnen waaronder apparaten, sensoren, clickstreams, socialemediafeeds en toepassingen. Deze patronen kunnen worden gebruikt als trigger voor acties of om werkstromen op te starten, bijvoorbeeld om waarschuwingen te maken, informatie door te sturen naar een rapportagetool of om getransformeerde gegevens op te slaan voor later. Daarnaast is Stream Analytics beschikbaar in Azure IoT Edge-runtime, zodat het ook gegevens op IoT-apparaten kan verwerken. 

De volgende scenario's zijn voorbeelden van wanneer u Azure Stream Analytics kunt gebruiken:

* Realtime telemetriegegevens van IoT-apparaten analyseren
* Weblogboeken/clickstream-analyses op internet
* Georuimtelijke analyses voor fleetmanagement en zelfrijdende auto’s
* Op afstand bewaken en predictief onderhoud van hoogwaardige middelen
* Realtime analyse van Point of Sale-gegevens voor voorraadbeheer en anomaliedetectie

## <a name="how-does-stream-analytics-work"></a>Hoe werkt Stream Analytics?

Een Azure Stream Analytics-taak bestaat uit een invoer, query en een uitvoer. Stream Analytics haalt gegevens op uit Azure Event Hubs (waaronder Azure Event Hubs van Apache Kafka), Azure IoT Hub of Azure Blob Storage. De query, die gebaseerd is op SQL-querytaal, kan gebruikt worden om streaminggegevens gedurende een bepaalde tijd eenvoudig te filteren, sorteren, combineren en samenvoegen. U kunt deze SQL-taal ook uitbreiden met JavaScript en door de C#-gebruiker gedefinieerde functies (UDF's). U kunt eenvoudig de opties voor gebeurtenisvolgorde en de duur van tijdvensters aanpassen bij het uitvoeren van combinatiebewerkingen met behulp van eenvoudige taalconstructs en/of configuraties.

Elke taak heeft een of meer uitvoerwaarden voor de getransformeerde gegevens, en u kunt bepalen wat er gebeurt als reactie op de informatie die u hebt geanalyseerd. U kunt bijvoorbeeld:

* Stuur gegevens naar services zoals Azure Functions, Service Bus Topics of Queues om communicatie of downstream aangepaste werkstromen te activeren.
* Stuur gegevens naar een Power BI-dashboard voor realtime dashboards.
* Gegevens in andere Azure-opslagservices opslaan om een Machine Learning-model te trainen op basis van historische gegevens of batchanalyses uit te voeren.

In de volgende afbeelding wordt getoond hoe gegevens naar Stream Analytics worden verzonden, geanalyseerd en verder verzonden voor andere acties, zoals opslag of presentatie:

![Pijplijn voor binnenkomende gegevens voor Stream Analytics](./media/stream-analytics-introduction/stream-analytics-e2e-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Belangrijkste mogelijkheden en voordelen

Azure Stream Analytics is gebruiksvriendelijk, flexibel, betrouwbaar en schaalbaar tot elke taakgrootte. Het is beschikbaar in meerdere Azure-regio's. In de volgende afbeelding worden de belangrijkste mogelijkheden van Azure Stream Analytics getoond:

![Belangrijkste mogelijkheden van Azure Stream Analytics](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Eenvoudig aan de slag

Met Azure Stream Analytics kunt u zo beginnen. Met slecht een paar klikken maakt u verbinding met meerdere bronnen en sinks om een end-to-endpijplijn te maken. Stream Analytics kan verbinding maken met [Azure Event Hubs](/azure/event-hubs/) en [Azure IoT Hub](/azure/iot-hub/) voor opname van streamgegevens, en met de [Azure Blob-opslag](/azure/storage/common/storage-introduction) voor opname van historische gegevens. Taakinvoer kan ook bestaan uit statische referentiegegevens van de Azure Blob-opslag of [SQL-database](stream-analytics-use-reference-data.md#azure-sql-database) die u met deze verwijzingsgegevens kunt samenvoegen om opzoekbewerkingen uit te voeren.

Stream Analytics kan taakuitvoer omleiden naar talloze opslagsystemen, bijvoorbeeld [Azure Blob-opslag](/azure/storage/common/storage-introduction), [Azure SQL Database](/azure/sql-database/), [Azure Data Lake Stores](/azure/data-lake-store/) en [Azure Cosmos DB](/azure/cosmos-db/introduction). U kunt batch-analyses uitvoeren op opgeslagen uitvoer met HDInsight of u kunt de uitvoer verzenden naar een andere service, zoals Event Hubs voor verbruik, of naar [Power BI](https://docs.microsoft.com/power-bi/) voor realtime visualisatie.

Zie [Uitvoer van Azure Stream Analytics begrijpen](stream-analytics-define-outputs.md) voor een volledige lijst met Stream Analytics-uitvoer.

## <a name="programmer-productivity"></a>Productiviteit van programmeurs

Azure Stream Analytics maakt gebruik van een eenvoudige, op SQL gebaseerde querytaal die is uitgebreid met krachtige, tijdelijke beperkingen voor het analyseren van gegevens in beweging. Voor het definiëren van taaktransformaties gebruikt u een eenvoudige, declaratieve [Stream Analytics-querytaal](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) waarmee u complexe, tijdelijke query’s en analyses kunt schrijven met behulp van SQL-constructs. Omdat de querytaal van Stream Analytics is consistent met SQL. Als u bekend bent met SQL, is dat voldoende om taken te kunnen maken. U kunt ook taken maken met ontwikkelaarstalen als Azure PowerShell, [Stream Analytics Visual Studio tools](stream-analytics-tools-for-visual-studio-install.md), de [Stream Analytics Visual Studio Code-extensie](quick-create-vs-code.md) of Azure Resource Manager-sjablonen. Met ontwikkelaarstalen kunt u offline transformatiequery’s ontwikkelen en de [CI/CD-pijplijn](stream-analytics-tools-for-visual-studio-cicd.md) gebruiken om taken bij Azure in te dienen.

De querytaal van Stream Analytics biedt een breed spectrum aan functies voor het analyseren en verwerken van streaming gegevens. Deze querytaal ondersteunt eenvoudige gegevensbewerking, -samenvoeging en analysefuncties, [georuimtelijke functies](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-geospatial-functions), [patroonherkenning](https://docs.microsoft.com/stream-analytics-query/match-recognize-stream-analytics) en [anomaliedetectie](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-machine-learning-anomaly-detection). U kunt query’s in de portal bewerken en ze testen met voorbeeldgegevens die uit een livestream worden opgehaald.

U kunt de mogelijkheden van de querytaal uitbreiden door extra functie te definiëren en aan te roepen. In Azure Machine Learning kunt u functieaanroepen definiëren om gebruik te kunnen maken van Azure Machine Learning-oplossingen en door gebruikers gedefinieerde JavaScript- of C#-functies (UDF’s) of door gebruikers gedefinieerde aggregaten te integreren voor het uitvoeren van complexe berekeningen als onderdeel van een Stream Analytics-query.

## <a name="fully-managed"></a>Volledig beheerd

Azure Stream Analytics is een volledige beheerde (PaaS) aanbieding in Azure. U moet geen hardware inrichten, clusters beheren om uw taken uit te voeren of besturingssystemen of software bij te werken. Azure Stream Analytics beheert uw taak volledig, zodat u zicht kunt richten op uw bedrijfslogica en niet op de infrastructuur.

## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>In de cloud of op de intelligente rand uitvoeren

Azure Stream Analytics kan in de cloud worden uitgevoerd voor grootschalige analysetaken, of op IoT Edge worden uitgevoerd voor analyses met een zeer lage latentie. Azure Stream Analytics maakt gebruik van dezelfde tools en querytaal in zowel de cloud als op de intelligente Edge, zodat ontwikkelaars ware hybride architecturen kunnen bouwen voor het verwerken van streams. 

## <a name="low-total-cost-of-ownership"></a>Lage total cost of ownership

Als cloudservice is Stream Analytics kostenefficiënt. Er is geen sprake van opstartkosten; u betaalt slechts voor de [streaming-eenheden die u verbruikt](stream-analytics-streaming-unit-consumption.md). Er is geen verplichting en het inrichten van clusters is niet nodig. U kunt de taak omhoog of omlaag schalen in functie van uw zakelijke behoeften.

## <a name="mission-critical-ready"></a>Essentieel gereed

Azure Stream Analytics is beschikbaar in meerdere regio's over de hele wereld en is ontworpen om uw essentiële workloads uit te voeren doordat de vereisten voor betrouwbaarheid, veiligheid en naleving worden ondersteund.

### <a name="reliability"></a>Betrouwbaarheid

Azure Stream Analytics garandeert Exactly-once-gebeurtenissenverwerking en At-least-once-levering van gebeurtenissen, zodat gebeurtenissen nooit verloren gaan. Exactly-once-verwerking is gegarandeerd met geselecteerde uitvoer, zoals beschreven in [Garanties voor de levering van gebeurtenissen](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).

Azure Stream Analytics bevat ingebouwde herstelfuncties in geval de levering van een gebeurtenis mislukt. Stream Analytics biedt ook de ingebouwde mogelijkheid voor het gebruik van controlepunten om de status van uw taak bij te houden, en levert herhaalbare resultaten.

Als een beheerde service garandeert Stream Analytics een beschikbaarheid op minuutniveau van 99,9% voor de verwerking van gebeurtenissen. Zie de pagina [Stream Analytics SLA](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) voor meer informatie. 

### <a name="security"></a>Beveiliging

Wat betreft beveiliging, versleutelt Azure Stream Analytics alle inkomende en uitgaande communicatie en biedt ondersteuning voor TLS 1.2. Ingebouwde controlepunten zijn eveneens versleuteld. Stream Analytics slaat de binnenkomende gegevens niet op omdat alle verwerking in het geheugen gebeurt.

### <a name="compliance"></a>Naleving

Azure Stream Analytics volgt meerdere nalevingscertificeringen, zoals beschreven in het [Overzicht van Azure-naleving](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="performance"></a>Prestaties

Stream Analytics verwerkt miljoenen gebeurtenissen per seconde en kan resultaten met een ultralage latenties leveren. U kunt ermee omhoogschalen of uitschalen, zodat u intensieve toepassingen kunt afhandelen die in realtime complexe gebeurtenissen verwerken. Stream Analytics ondersteunt de hogere prestaties door gebruik te maken van partitionering, zodat complexe query's parallel en op meerdere streamingknooppunten kunnen worden uitgevoerd. Azure Stream Analytics is gebouwd op [Trill](https://github.com/Microsoft/Trill), een krachtige in-memory engine voor streaminganalyse die is ontwikkeld in samenwerking met Microsoft Research.

## <a name="next-steps"></a>Volgende stappen

U hebt nu een overzicht van Azure Stream Analytics. Hierna kunt u zich verder in de materie verdiepen en uw eerste Stream Analytics-taak maken:

* [Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md).
* [Create a Stream Analytics job by using Azure PowerShell](stream-analytics-quick-create-powershell.md) (Een Stream Analytics-taak maken met behulp van Azure PowerShell).
* [Create a Stream Analytics job by using Visual Studio](stream-analytics-quick-create-vs.md) (Een Stream Analytics-taak maken met behulp van Visual Studio).
* [Create a Stream Analytics job by using Visual Studio Code](quick-create-vs-code.md) (Een Stream Analytics-taak maken met behulp van Visual Studio).
