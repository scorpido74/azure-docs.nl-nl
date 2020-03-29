---
title: Gegevens uit Kafka opnemen in Azure Data Explorer
description: In dit artikel leert u hoe u gegevens inneemt (laden) in Azure Data Explorer van Kafka.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 03b46ff50683149a22c71ccb155480a0f08455bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66497270"
---
# <a name="ingest-data-from-kafka-into-azure-data-explorer"></a>Gegevens uit Kafka opnemen in Azure Data Explorer
 
Azure Data Explorer is een snelle en zeer schaalbare service voor gegevensverkenning voor telemetrische gegevens en gegevens uit logboeken. Azure Data Explorer biedt opname (het laden van gegevens) uit Kafka. Kafka is een gedistribueerd streamingplatform voor het bouwen van pijplijnen voor realtime streaminggegevens, waarmee gegevens op betrouwbare wijze worden verplaatst tussen systemen of toepassingen.
 
## <a name="prerequisites"></a>Vereisten
 
* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint. 
 
* [Een testcluster en -database](create-cluster-database-portal.md).
 
* [Een voorbeeld-app](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/kafka) die gegevens genereert en naar Kafka stuurt.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) om de voorbeeld-app uit te voeren.
 
## <a name="kafka-connector-setup"></a>Kafka-connector instellen

Kafka Connect is een hulpprogramma voor het schaalbaar en betrouwbaar streamen van gegevens tussen Apache Kafka en andere systemen. Hiermee kunt u eenvoudig en snel connectors definiëren die grote verzamelingen gegevens naar en van Kafka verplaatsen. De ADX Kafka Sink fungeert als de connector vanuit Kafka.
 
### <a name="bundle"></a>Bundel

Kafka kan een `.jar` laden als een invoegtoepassing die als een aangepaste connector zal fungeren. Voor het produceren van een dergelijke `.jar`, wordt de code lokaal gekloond en gecompileerd met behulp van Maven. 

#### <a name="clone"></a>Klonen

```bash
git clone git://github.com:Azure/kafka-sink-azure-kusto.git
cd ./kafka-sink-azure-kusto/kafka/
```

#### <a name="build"></a>Ontwikkelen

Compileer lokaal met Maven om een `.jar`, compleet met afhankelijkheden te produceren.

* JDK >= 1.8 [download](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Maven [download](https://maven.apache.org/install.html)
 

Voor in de basismap *kafka-sink-azure-kusto* het volgende uit:

```bash
mvn clean compile assembly:single
```

### <a name="deploy"></a>Implementeren 

Laad de invoegtoepassing in Kafka. Een implementatievoorbeeld met behulp van docker kunt u vinden in [kafka-sink-azure-kusto](https://github.com/Azure/kafka-sink-azure-kusto#deploy)
 

Gedetailleerde documentatie over Kafka-connectors en het implementeren ervan vindt u in [Kafka Connect](https://kafka.apache.org/documentation/#connect) 

### <a name="example-configuration"></a>Voorbeeldconfiguratie 
 
```config
name=KustoSinkConnector 
connector.class=com.microsoft.azure.kusto.kafka.connect.sink.KustoSinkConnector 
kusto.sink.flush_interval_ms=300000 
key.converter=org.apache.kafka.connect.storage.StringConverter 
value.converter=org.apache.kafka.connect.storage.StringConverter 
tasks.max=1 
topics=testing1 
kusto.tables.topics_mapping=[{'topic': 'testing1','db': 'daniel', 'table': 'TestTable','format': 'json', 'mapping':'TestMapping'}] 
kusto.auth.authority=XXX 
kusto.url=https://ingest-{mycluster}.kusto.windows.net/ 
kusto.auth.appid=XXX 
kusto.auth.appkey=XXX 
kusto.sink.tempdir=/var/tmp/ 
kusto.sink.flush_size=1000
```
 
## <a name="create-a-target-table-in-adx"></a>Een doeltabel in ADX maken
 
Maak een tabel in ADX waarnaar Kafka gegevens kan verzenden. Maak de tabel in het cluster en de database die is ingericht in **Vereisten**.
 
1. Ga in de Azure-portal naar het cluster en selecteer **Query**.
 
    ![Toepassingskoppeling voor query](media/ingest-data-event-hub/query-explorer-link.png)
 
1. Kopieer de volgende opdracht in het venster en selecteer **Uitvoeren**.
 
    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```
 
    ![Maken van query uitvoeren](media/ingest-data-event-hub/run-create-query.png)
 
1. Kopieer de volgende opdracht in het venster en selecteer **Uitvoeren**.
 
    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.timeStamp","datatype":"datetime"},{"column":"Name","path":"$.name","datatype":"string"},{"column":"Metric","path":"$.metric","datatype":"int"},{"column":"Source","path":"$.source","datatype":"string"}]'
    ```

    Met deze opdracht worden binnenkomende JSON-gegevens toegewezen aan de kolomnamen en gegevenstypen van de tabel (TestTable).


## <a name="generate-sample-data"></a>Voorbeeldgegevens genereren

Nu het Kafka-cluster is verbonden met ADX, gebruikt u de [voorbeeld-app](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) die u hebt gedownload om gegevens te genereren.

### <a name="clone"></a>Klonen

U kunt als volgt de voorbeeld-app lokaal klonen:

```cmd
git clone git://github.com:Azure/azure-kusto-samples-dotnet.git
cd ./azure-kusto-samples-dotnet/kafka/
```

### <a name="run-the-app"></a>De app uitvoeren

1. Open de voorbeeld-app in Visual Studio.

1. Wijzig in het bestand `Program.cs` de constante `connectionString` in uw Kafka-verbindingsreeks.

    ```csharp    
    const string connectionString = @"<YourConnectionString>";
    ```

1. Bouw de app en voer deze uit. De app verzendt berichten naar het Kafka-cluster en geeft elke tien seconden de status weer.

1. Wanneer de app een aantal berichten heeft verzonden, gaat u verder met de volgende stap.
 
## <a name="query-and-review-the-data"></a>Query's uitvoeren en de gegevens controleren

1. U controleert als volgt of er tijdens de opname geen fouten zijn opgetreden:

    ```Kusto
    .show ingestion failures
    ```

1. U kunt als volgt de zojuist opgenomen gegevens bekijken:

    ```Kusto
    TestTable 
    | count
    ```

1. U kunt als volgt de inhoud van de berichten bekijken:
 
    ```Kusto
    TestTable
    ```
 
    De resultatenset ziet er ongeveer als volgt uit:
 
    ![Berichtresultatenset](media/ingest-data-event-hub/message-result-set.png)
 
## <a name="next-steps"></a>Volgende stappen
 
* [Query's uitvoeren op gegevens in Azure Data Explorer](web-query-data.md)
