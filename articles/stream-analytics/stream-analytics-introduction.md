---
title: Overzicht van Azure Stream Analytics
description: Lees over Stream Analytics, een beheerde service waarmee u streaminggegevens van het Internet der dingen (IoT) in realtime kun analyseren.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: f15a4605d28beaf97e877f337051a2ec13148a41
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80235781"
---
# <a name="what-is-azure-stream-analytics"></a>Wat is Azure Stream Analytics?

Azure Stream Analytics is een realtime analyse- en complexe gebeurtenisverwerkingsengine die is ontworpen om grote hoeveelheden snelle streaminggegevens uit meerdere bronnen tegelijk te analyseren en te verwerken. Patronen en relaties kunnen worden geïdentificeerd in informatie die is geëxtraheerd uit een aantal invoerbronnen, waaronder apparaten, sensoren, clickstreams, sociale media-feeds en toepassingen. Deze patronen kunnen worden gebruikt om acties te activeren en workflows te starten, zoals het maken van waarschuwingen, het voeden van informatie naar een rapportagetool of het opslaan van getransformeerde gegevens voor later gebruik. Stream Analytics is ook beschikbaar op Azure IoT Edge-runtime en ondersteunt dezelfde exacte taal of syntaxis als in de cloud. 

De volgende scenario's zijn voorbeelden van wanneer u Azure Stream Analytics gebruiken:

* Real-time telemetriestreams van IoT-apparaten analyseren
* Weblogboeken/clickstream-analyses op internet
* Georuimtelijke analyses voor fleetmanagement en zelfrijdende auto’s
* Bewaking op afstand en voorspellend onderhoud van hoogwaardige activa
* Realtime analyse van Point of Sale-gegevens voor voorraadbeheer en anomaliedetectie

## <a name="how-does-stream-analytics-work"></a>Hoe werkt Stream Analytics?

Een Azure Stream Analytics-taak bestaat uit een invoer, query en een uitvoer. Stream Analytics neemt gegevens op uit Azure Event Hubs, Azure IoT Hub of Azure Blob Storage. De query, die is gebaseerd op SQL-querytaal, kan worden gebruikt om gedurende een bepaalde periode eenvoudig streaminggegevens te filteren, sorteren, te aggregeren en samenvoegen. U deze SQL-taal ook uitbreiden met JavaScript- en C#-gebruikersgedefinieerde functies (UDF's). U eenvoudig de opties voor het bestellen van gebeurtenissen en de duur van de tijdvensters aanpassen wanneer u aggregatiebewerkingen vooraf informeert via eenvoudige taalconstructies en/of configuraties.

Elke taak heeft een uitvoer voor de getransformeerde gegevens en u bepalen wat er gebeurt in reactie op de informatie die u hebt geanalyseerd. U kunt bijvoorbeeld:

* Stuur gegevens naar services zoals Azure-functies, servicebusonderwerpen of wachtrijen om communicatie of aangepaste werkstromen stroomafwaarts te activeren.
* Stuur gegevens naar een Power BI-dashboard voor realtime dashboarding.
* Sla gegevens op in andere Azure-opslagservices om een machine learning-model te trainen op basis van historische gegevens of batchanalyses uit te voeren.

In de volgende afbeelding ziet u hoe gegevens worden verzonden naar Stream Analytics, worden geanalyseerd en verzonden voor andere acties, zoals opslag of presentatie:

![Pijplijn voor binnenkomende gegevens voor Stream Analytics](./media/stream-analytics-introduction/stream-analytics-e2e-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Belangrijkste mogelijkheden en voordelen

Azure Stream Analytics is gebruiksvriendelijk, flexibel, betrouwbaar en schaalbaar tot elke taakgrootte. Het is beschikbaar in meerdere Azure-regio's. De volgende afbeelding illustreert de belangrijkste mogelijkheden van Azure Stream Analytics:

![Belangrijkste mogelijkheden van Azure Stream Analytics](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Eenvoudig aan de slag

Azure Stream Analytics is eenvoudig te starten. Het duurt slechts een paar klikken om verbinding te maken met meerdere bronnen en sinks, het creëren van een end-to-end pijplijn. Stream Analytics kan verbinding maken met [Azure Event Hubs](/azure/event-hubs/) en Azure [IoT Hub](/azure/iot-hub/) voor het streamen van gegevens en Azure [Blob-opslag](/azure/storage/storage-introduction) om historische gegevens in te nemen. Taakinvoer kan ook statische of langzaam veranderende referentiegegevens uit Azure Blob-opslag of [SQL-database](stream-analytics-use-reference-data.md#azure-sql-database) bevatten waaraan u deelnemen aan streaminggegevens om opzoekbewerkingen uit te voeren.

Stream Analytics kan taakuitvoer doorsturen naar veel opslagsystemen, zoals [Azure Blob-opslag,](/azure/storage/storage-introduction) [Azure SQL Database,](/azure/sql-database/) [Azure Data Lake Store](/azure/data-lake-store/)en [Azure CosmosDB.](/azure/cosmos-db/introduction) U batchanalyses uitvoeren op opgeslagen uitvoer met Azure HDInsight of u de uitvoer naar een andere service verzenden, zoals Gebeurtenishubs voor verbruik of [Power BI](https://docs.microsoft.com/power-bi/) voor realtime visualisatie.

Zie [Uitvoer van Azure Stream Analytics begrijpen](stream-analytics-define-outputs.md)voor de volledige lijst met Stream Analytics-uitvoer.

## <a name="programmer-productivity"></a>Productiviteit van programmeurs

Azure Stream Analytics maakt gebruik van een eenvoudige SQL-gebaseerde querytaal die is uitgebreid met krachtige tijdelijke beperkingen om gegevens in beweging te analyseren. Voor het definiëren van taaktransformaties gebruikt u een eenvoudige, declaratieve [Stream Analytics-querytaal](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) waarmee u complexe, tijdelijke query’s en analyses kunt schrijven met behulp van SQL-constructs. Omdat de querytaal van Stream Analytics consistent is voor de SQL-taal, is vertrouwdheid met SQL voldoende om taken te maken. U ook taken maken met behulp van hulpprogramma's voor ontwikkelaars zoals Azure PowerShell, [Stream Analytics Visual Studio-hulpprogramma's,](stream-analytics-tools-for-visual-studio-install.md)de [uitbreiding Visual Studio Code](quick-create-vs-code.md)van Stream Analytics of Azure Resource Manager-sjablonen. Met ontwikkelaarstalen kunt u offline transformatiequery’s ontwikkelen en de [CI/CD-pijplijn](stream-analytics-tools-for-visual-studio-cicd.md) gebruiken om taken bij Azure in te dienen.

De querytaal van Stream Analytics biedt een breed scala aan functies voor het analyseren en verwerken van streaminggegevens. Deze querytaal ondersteunt eenvoudige gegevensmanipulatie, aggregatiefuncties en complexe georuimtelijke functies. U query's in de portal bewerken en testen met voorbeeldgegevens die uit een livestream worden geëxtraheerd.

U kunt de mogelijkheden van de querytaal uitbreiden door extra functie te definiëren en aan te roepen. U functieoproepen definiëren in de Azure Machine Learning om te profiteren van Azure Machine Learning-oplossingen en JavaScript- of C#-gebruikersgedefinieerde functies (UDF's) of door de gebruiker gedefinieerde aggregaten integreren om complexe berekeningen uit te voeren als onderdeel van een Stream Analytics-query.

## <a name="fully-managed"></a>Volledig beheerd

Azure Stream Analytics is een volledige beheerde (PaaS) aanbieding in Azure. U hoeft geen hardware in te richten of clusters te beheren om uw taken uit te voeren. Azure Stream Analytics beheert uw taak volledig door complexe compute clusters in de cloud in te stellen en de prestatieafstemming te verzorgen die nodig is om de taak uit te voeren. Dankzij de integratie met Azure Event Hubs en Azure IoT Hub kan uw taak miljoenen gebeurtenissen per seconde opnemen die afkomstig zijn van een aantal bronnen, waaronder verbonden apparaten, clickstreams en logboekbestanden. Met de partitioneringsfunctie van Gebeurtenishubs u berekeningen in logische stappen verdelen, elk met de mogelijkheid om verder te worden verdeeld om de schaalbaarheid te vergroten.

## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>Uitvoeren in de cloud of op de intelligente rand

Azure Stream Analytics kan worden uitgevoerd in de cloud, voor grootschalige analyses, of worden uitgevoerd op IoT Edge voor analyse met ultralage latentie. Azure Stream Analytics gebruikt dezelfde querytaal op zowel de cloud als de edge, zodat ontwikkelaars echt hybride architecturen kunnen bouwen voor streamverwerking. 

## <a name="low-total-cost-of-ownership"></a>Lage totale eigendomskosten

Als cloudservice is Stream Analytics kostenefficiënt. Er zijn geen kosten vooraf aan verbonden - u betaalt alleen voor de [streaming-eenheden die u gebruikt](stream-analytics-streaming-unit-consumption.md)en de hoeveelheid gegevens die wordt verwerkt. Er is geen verplichting of clusterprovisioning vereist en u de taak op- of neerschalen op basis van uw bedrijfsbehoeften.

## <a name="mission-critical-ready"></a>Essentieel gereed

Azure Stream Analytics is beschikbaar in meerdere regio's over de hele wereld en is ontworpen om uw essentiële workloads uit te voeren doordat de vereisten voor betrouwbaarheid, veiligheid en naleving worden ondersteund.

### <a name="reliability"></a>Betrouwbaarheid

Azure Stream Analytics garandeert Exactly-once-gebeurtenissenverwerking en At-least-once-levering van gebeurtenissen, zodat gebeurtenissen nooit verloren gaan. Precies-zodra verwerking is gegarandeerd met geselecteerde output zoals beschreven in [Event Delivery Garanties](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).

Azure Stream Analytics bevat ingebouwde herstelfuncties in geval de levering van een gebeurtenis mislukt. Stream Analytics biedt ook ingebouwde controlepunten om de status van uw taak te behouden en biedt herhaalbare resultaten.

Als managed service garandeert Stream Analytics de verwerking van gebeurtenissen met een beschikbaarheid van 99,9% op een minieme granulariteitsniveau. Zie de [SLA-pagina Stream Analytics voor](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) meer informatie. 

### <a name="security"></a>Beveiliging

Wat betreft beveiliging, versleutelt Azure Stream Analytics alle inkomende en uitgaande communicatie en biedt ondersteuning voor TLS 1.2. Ingebouwde controlepunten zijn eveneens versleuteld. Stream Analytics slaat de binnenkomende gegevens niet op omdat alle verwerking in het geheugen gebeurt.

### <a name="compliance"></a>Naleving

Azure Stream Analytics volgt meerdere nalevingscertificeringen, zoals beschreven in het [Overzicht van Azure-naleving](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="performance"></a>Prestaties

Stream Analytics kan elke seconde miljoenen gebeurtenissen verwerken en resultaten leveren met ultra lage latencies. U kunt ermee omhoogschalen of uitschalen, zodat u intensieve toepassingen kunt afhandelen die in realtime complexe gebeurtenissen verwerken. Stream Analytics ondersteunt hogere prestaties door partitionering, waardoor complexe query's kunnen worden parallellopen en uitgevoerd op meerdere streamingknooppunten. Azure Stream Analytics is gebouwd op [Trill](https://github.com/Microsoft/Trill), een krachtige in-memory engine voor streaminganalyse die is ontwikkeld in samenwerking met Microsoft Research.

## <a name="next-steps"></a>Volgende stappen

U hebt nu een overzicht van Azure Stream Analytics. Hierna kunt u zich verder in de materie verdiepen en uw eerste Stream Analytics-taak maken:

* [Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md).
* [Maak een Stream Analytics-taak met Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Create a Stream Analytics job by using Visual Studio](stream-analytics-quick-create-vs.md) (Een Stream Analytics-taak maken met behulp van Visual Studio).
* [Maak een Functie Stream Analytics met Visual Studio Code](quick-create-vs-code.md).
