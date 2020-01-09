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
ms.openlocfilehash: 3399cc47af20df05d6315a4bd77965f799eaf5f9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426170"
---
# <a name="what-is-azure-stream-analytics"></a>Wat is Azure Stream Analytics?

Azure Stream Analytics is een real-time analyse-engine voor gebeurtenis verwerking die is ontworpen voor het analyseren en verwerken van grote hoeveel heden gegevens snel streamen uit meerdere bronnen tegelijk. Patronen en relaties kunnen worden geïdentificeerd in gegevens die worden geëxtraheerd uit een aantal invoer bronnen, waaronder apparaten, Sens oren, clickstreams, sociale media-feeds en toepassingen. Deze patronen kunnen worden gebruikt voor het activeren van acties en het initiëren van werk stromen, zoals het maken van waarschuwingen, het uitvoeren van gegevens naar een rapportage programma of het opslaan van getransformeerde gegevens voor later gebruik. Stream Analytics is ook beschikbaar in Azure IoT Edge runtime en ondersteunt dezelfde exacte taal of syntaxis als de Cloud. 

De volgende scenario's zijn voor beelden van wanneer u Azure Stream Analytics kunt gebruiken:

* Real-time telemetriegegevens van IoT-apparaten analyseren
* Weblogboeken/clickstream-analyses op internet
* Georuimtelijke analyses voor fleetmanagement en zelfrijdende auto’s
* Externe controle en voor speld onderhoud van hoogwaardige assets
* Realtime analyse van Point of Sale-gegevens voor voorraadbeheer en anomaliedetectie

## <a name="how-does-stream-analytics-work"></a>Hoe werkt Stream Analytics?

Een Azure Stream Analytics-taak bestaat uit een invoer, query en een uitvoer. Stream Analytics neemt gegevens op uit Azure Event Hubs, Azure IoT Hub of Azure Blob Storage. De query, die is gebaseerd op de SQL-query taal, kan worden gebruikt om streaminggegevens in een bepaalde periode eenvoudig te filteren, te sorteren, te combi neren en samen te voegen. U kunt deze SQL-taal ook uitbreiden met Java C# script en door de gebruiker gedefinieerde functies (udf's). U kunt eenvoudig de opties voor het ordenen van gebeurtenissen en de duur van tijd Vensters aanpassen wanneer u aggregatie bewerkingen voordoet via eenvoudige taal constructies en/of-configuraties.

Elke taak heeft een uitvoer voor de getransformeerde gegevens en u kunt bepalen wat er gebeurt als reactie op de informatie die u hebt geanalyseerd. U kunt bijvoorbeeld:

* Gegevens verzenden naar Services als Azure Functions, Service Bus onderwerpen of wacht rijen om communicaties of aangepaste werk stromen downstream te activeren.
* Gegevens verzenden naar een dash board van Power BI voor realtime-Dash boards.
* Sla gegevens op in andere Azure Storage-services om een machine learning model te trainen op basis van historische gegevens of om batch analyses uit te voeren.

In de volgende afbeelding ziet u hoe gegevens worden verzonden naar Stream Analytics, geanalyseerd en verzonden voor andere acties, zoals opslag of presentatie:

![Pijplijn voor binnenkomende gegevens voor Stream Analytics](./media/stream-analytics-introduction/stream-analytics-intro-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Belangrijkste mogelijkheden en voordelen

Azure Stream Analytics is gebruiksvriendelijk, flexibel, betrouwbaar en schaalbaar tot elke taakgrootte. Het is beschikbaar in meerdere Azure-regio's. In de volgende afbeelding ziet u de belangrijkste mogelijkheden van Azure Stream Analytics:

![Belangrijkste mogelijkheden van Azure Stream Analytics](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Eenvoudig aan de slag

Azure Stream Analytics is gemakkelijk te starten. Er zijn slechts enkele klikken om verbinding te maken met meerdere bronnen en sinks, waarbij u een end-to-end-pijp lijn maakt. Stream Analytics kan verbinding maken met [azure Event hubs](/azure/event-hubs/) en [Azure IOT hub](/azure/iot-hub/) voor de opname van gegevens stromen, maar ook met [Azure Blob Storage](/azure/storage/storage-introduction) om historische gegevens op te nemen. Taak invoer kan ook statische of langzaam veranderende referentie gegevens bevatten van Azure Blob-opslag of [SQL database](stream-analytics-use-reference-data.md#azure-sql-database) dat u kunt deel nemen aan streaminggegevens om opzoek bewerkingen uit te voeren.

Stream Analytics kunt taak uitvoer naar veel opslag systemen routeren, zoals [Azure Blob Storage](/azure/storage/storage-introduction), [Azure SQL database](/azure/sql-database/), [Azure data Lake Store](/azure/data-lake-store/)en [Azure CosmosDB](/azure/cosmos-db/introduction). U kunt batch analyses uitvoeren op opgeslagen uitvoer met Azure HDInsight, of u kunt de uitvoer naar een andere service verzenden, zoals Event Hubs voor verbruik of [Power bi](https://docs.microsoft.com/power-bi/) voor realtime visualisatie.

Zie voor de volledige lijst met Stream Analytics uitvoer [inzicht in de uitvoer van Azure stream Analytics](stream-analytics-define-outputs.md).

## <a name="programmer-productivity"></a>Programmeer productiviteit

Azure Stream Analytics maakt gebruik van een eenvoudige SQL-gebaseerde query taal die is uitgebreid met krachtige tijdelijke beperkingen voor het analyseren van gegevens in beweging. Voor het definiëren van taaktransformaties gebruikt u een eenvoudige, declaratieve [Stream Analytics-querytaal](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) waarmee u complexe, tijdelijke query’s en analyses kunt schrijven met behulp van SQL-constructs. Omdat Stream Analytics query taal consistent is met de SQL-taal, is vertrouwd met SQL voldoende om taken te kunnen maken. U kunt ook taken maken met behulp van ontwikkel hulpprogramma's als Azure PowerShell, [Stream Analytics Visual Studio Tools](stream-analytics-tools-for-visual-studio-install.md), de [Stream Analytics Visual Studio Code extension](quick-create-vs-code.md)of Azure Resource Manager-sjablonen. Met ontwikkelaarstalen kunt u offline transformatiequery’s ontwikkelen en de [CI/CD-pijplijn](stream-analytics-tools-for-visual-studio-cicd.md) gebruiken om taken bij Azure in te dienen.

De Stream Analytics query taal biedt een breed scala aan functies voor het analyseren en verwerken van streaming-gegevens. Deze query taal ondersteunt eenvoudige gegevens manipulatie, aggregatie functies en complexe georuimtelijke functies. U kunt query's in de portal bewerken en ze testen met voorbeeld gegevens die zijn geëxtraheerd uit een live stream.

U kunt de mogelijkheden van de querytaal uitbreiden door extra functie te definiëren en aan te roepen. U kunt functie aanroepen definiëren in de Azure Machine Learning om te profiteren van Azure Machine Learning oplossingen en Java script of C# door de gebruiker gedefinieerde functies (udf's) of door de gebruiker gedefinieerde samen tellingen integreren om complexe berekeningen uit te voeren als onderdeel van een stream Analytics query.

## <a name="fully-managed"></a>Volledig beheerd

Azure Stream Analytics is een volledige beheerde (PaaS) aanbieding in Azure. U hoeft geen hardware in te richten of clusters te beheren om uw taken uit te voeren. Azure Stream Analytics uw taak volledig beheert door complexe reken clusters in de cloud in te stellen en de prestaties af te stemmen die nodig zijn om de taak uit te voeren. Dankzij de integratie met Azure Event Hubs en Azure IoT Hub kan uw taak miljoenen gebeurtenissen per seconde opnemen, afkomstig van een aantal bronnen, om verbonden apparaten, clickstreams en logboek bestanden toe te voegen. Met de functie voor partitioneren van Event Hubs kunt u berekeningen partitioneren in logische stappen, elk met de mogelijkheid om verder te partitioneren om de schaal baarheid te verg Roten.

## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>Uitvoeren in de Cloud of op de intelligente rand

Azure Stream Analytics kan worden uitgevoerd in de Cloud, voor grootschalige analyses of voor het uitvoeren van een IoT Edge voor een ultra lage latentie analyse. Azure Stream Analytics gebruikt dezelfde query taal in zowel de Cloud als de rand, waardoor ontwikkel aars echte hybride architecturen kunnen bouwen voor de verwerking van streams. 

## <a name="low-total-cost-of-ownership"></a>Lage total cost of ownership

Als cloudservice is Stream Analytics kostenefficiënt. Er zijn geen kosten vooraf betrokken. u betaalt alleen voor de [streaming-eenheden die u verbruikt](stream-analytics-streaming-unit-consumption.md)en de hoeveelheid gegevens die wordt verwerkt. Er is geen toezeg ging of het inrichten van een cluster vereist en u kunt de taak omhoog of omlaag schalen op basis van de behoeften van uw bedrijf.

## <a name="mission-critical-ready"></a>Essentieel gereed

Azure Stream Analytics is beschikbaar in meerdere regio's over de hele wereld en is ontworpen om uw essentiële workloads uit te voeren doordat de vereisten voor betrouwbaarheid, veiligheid en naleving worden ondersteund.

### <a name="reliability"></a>Betrouwbaarheid

Azure Stream Analytics garandeert Exactly-once-gebeurtenissenverwerking en At-least-once-levering van gebeurtenissen, zodat gebeurtenissen nooit verloren gaan. Eenmalige verwerking wordt gegarandeerd met de geselecteerde uitvoer, zoals wordt beschreven in de [gebeurtenis leverings garanties](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).

Azure Stream Analytics bevat ingebouwde herstelfuncties in geval de levering van een gebeurtenis mislukt. Stream Analytics biedt ook ingebouwde controle punten om de status van uw taak te behouden en herhaal bare resultaten te bieden.

Als beheerde service Stream Analytics de verwerking van gebeurtenissen met een Beschik baarheid van 99,9% per minuut niveau nauw keuriger. Zie de pagina [Stream Analytics Sla](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) voor meer informatie. 

### <a name="security"></a>Beveiliging

Wat betreft beveiliging, versleutelt Azure Stream Analytics alle inkomende en uitgaande communicatie en biedt ondersteuning voor TLS 1.2. Ingebouwde controlepunten zijn eveneens versleuteld. Stream Analytics slaat de binnenkomende gegevens niet op omdat alle verwerking in het geheugen gebeurt.

### <a name="compliance"></a>Naleving

Azure Stream Analytics volgt meerdere nalevingscertificeringen, zoals beschreven in het [Overzicht van Azure-naleving](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="performance"></a>Prestaties

Stream Analytics kunt miljoenen gebeurtenissen elke seconde verwerken en er kunnen ook resultaten worden geleverd met een zeer lage latentie. U kunt ermee omhoogschalen of uitschalen, zodat u intensieve toepassingen kunt afhandelen die in realtime complexe gebeurtenissen verwerken. Stream Analytics ondersteunt betere prestaties door partities te maken, waardoor complexe query's kunnen worden geparallelleerd en uitgevoerd op meerdere streaming-knoop punten. Azure Stream Analytics is gebouwd op [Trill](https://github.com/Microsoft/Trill), een krachtige in-memory engine voor streaminganalyse die is ontwikkeld in samenwerking met Microsoft Research.

## <a name="next-steps"></a>Volgende stappen

U hebt nu een overzicht van Azure Stream Analytics. Hierna kunt u zich verder in de materie verdiepen en uw eerste Stream Analytics-taak maken:

* [Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md).
* [Create a Stream Analytics job by using Azure PowerShell](stream-analytics-quick-create-powershell.md) (Een Stream Analytics-taak maken met behulp van Azure PowerShell).
* [Create a Stream Analytics job by using Visual Studio](stream-analytics-quick-create-vs.md) (Een Stream Analytics-taak maken met behulp van Visual Studio).
* [Maak een stream Analytics taak met behulp van Visual Studio code](quick-create-vs-code.md).
