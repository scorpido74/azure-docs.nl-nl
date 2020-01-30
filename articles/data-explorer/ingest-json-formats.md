---
title: Gegevens in JSON-indeling opnemen in azure Data Explorer
description: Meer informatie over het opnemen van gegevens in JSON-indeling in azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: d293b76e004d693813a074cb8551a86cb3c0bec2
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772336"
---
# <a name="ingest-json-formatted-sample-data-into-azure-data-explorer"></a>Voorbeeld gegevens in JSON-indeling opnemen in azure Data Explorer

Dit artikel laat u zien hoe u gegevens in JSON-indeling kunt opnemen in een Azure Data Explorer-data base. U begint met eenvoudige voor beelden van RAW en toegewezen JSON, gaat verder naar JSON met meerdere regels en vervolgens worden complexere JSON-schema's met matrices en woorden boeken door lopen.  De voor beelden beschrijven het proces van het opnemen van gegevens in JSON-indeling met behulp van C#Kusto query language (KQL), of python. De Kusto-query taal `ingest` besturings opdrachten worden direct uitgevoerd op het eind punt van de engine. In productie scenario's wordt opname voor de Gegevensbeheer-service uitgevoerd met behulp van client bibliotheken of gegevens verbindingen. Lees [opname gegevens met behulp van de azure Data Explorer python-bibliotheek](/azure/data-explorer/python-ingest-data) en neem [gegevens op met behulp van de Azure Data Explorer .NET Standard SDK](/azure/data-explorer/net-standard-ingest-data) voor een overzicht van de opname van gegevens met deze client bibliotheken.

## <a name="prerequisites"></a>Vereisten

[Een cluster en database voor testdoeleinden](create-cluster-database-portal.md)

## <a name="the-json-format"></a>De JSON-indeling

Azure Data Explorer ondersteunt twee JSON-bestands indelingen:
* `json`: JSON wordt gescheiden door een regel. Elke regel in de invoer gegevens heeft precies één JSON-record.
* `multijson`: JSON met meerdere regels. De parser negeert de regel scheidings tekens en leest een record van de vorige positie naar het einde van een geldige JSON.

### <a name="ingest-and-map-json-formatted-data"></a>Gegevens opnemen in JSON-indeling en deze toewijzen

Voor opname van gegevens in JSON-indeling moet u de *indeling* opgeven met behulp van de [eigenschap inslikken](/azure/kusto/management/data-ingestion/index#ingestion-properties). Voor opname van JSON-gegevens is [toewijzing](/azure/kusto/management/mappings)vereist, waarmee een JSON-bron vermelding wordt toegewezen aan de doel kolom. Wanneer u gegevens opneemt, gebruikt u de vooraf gedefinieerde `jsonMappingReference` opname eigenschap of geeft u de eigenschap `jsonMapping`opname op. In dit artikel wordt gebruikgemaakt van de `jsonMappingReference` opname-eigenschap, die vooraf is gedefinieerd in de tabel die wordt gebruikt voor opname. In de onderstaande voor beelden beginnen we met het opnemen van JSON-records als onbewerkte gegevens in een tabel met één kolom. Vervolgens gebruiken we de toewijzing om elke eigenschap op te nemen in de toegewezen kolom. 

### <a name="simple-json-example"></a>Eenvoudig JSON-voor beeld

Het volgende voor beeld is een eenvoudige JSON met een vlakke structuur. De gegevens hebben informatie over de Tempe ratuur en vochtigheid, verzameld door verschillende apparaten. Elke record is gemarkeerd met een ID en tijds tempel.

```json
{
    "timestamp": "2019-05-02 15:23:50.0369439",
    "deviceId": "2945c8aa-f13e-4c48-4473-b81440bb5ca2",
    "messageId": "7f316225-839a-4593-92b5-1812949279b3",
    "temperature": 31.0301639051317,
    "humidity": 62.0791099602725
}
```

## <a name="ingest-raw-json-records"></a>Onbewerkte JSON-records opnemen 

In dit voor beeld neemt u JSON-records op als onbewerkte gegevens in een tabel met één kolom. De gegevens bewerking, het gebruik van query's en het update beleid wordt uitgevoerd nadat de gegevens zijn opgenomen.

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

Gebruik de Kusto-query taal om gegevens op te nemen in een onbewerkte JSON-indeling.

1. Meld u aan bij [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. Selecteer **Add Cluster**.

1. Voer in het dialoog venster **cluster toevoegen** uw cluster-URL in de notatie `https://<ClusterName>.<Region>.kusto.windows.net/`en selecteer vervolgens **toevoegen**.

1. Plak de volgende opdracht en selecteer **uitvoeren** om de tabel te maken.

    ```Kusto
    .create table RawEvents (Event: dynamic)
    ```

    Met deze query maakt u een tabel met één `Event` kolom van een [dynamisch](/azure/kusto/query/scalar-data-types/dynamic) gegevens type.

1. Maak de JSON-toewijzing.

    ```Kusto
    .create table RawEvents ingestion json mapping 'RawEventMapping' '[{"column":"Event","path":"$"}]'
    ```

    Met deze opdracht maakt u een toewijzing en wijst u het pad naar de JSON-hoofdmap `$` toe aan de kolom `Event`.

1. Gegevens opnemen in de `RawEvents` tabel.

    ```Kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=RawEventMapping)
    ```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

Gebruiken C# om gegevens op te nemen in de onbewerkte JSON-indeling.

1. Maak de `RawEvents` tabel.

    ```C#
    var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
    var kustoConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder);

    var table = "RawEvents";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Events", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Maak de JSON-toewijzing.
    
    ```C#
    var tableMapping = "RawEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                new JsonColumnMapping {ColumnName = "Events", JsonPath = "$"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```
    Met deze opdracht maakt u een toewijzing en wijst u het pad naar de JSON-hoofdmap `$` toe aan de kolom `Event`.

1. Gegevens opnemen in de `RawEvents` tabel.

    ```C#
    var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var ingestConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder);
    
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

> [!NOTE]
> Gegevens worden geaggregeerd volgens het [batch beleid](/azure/kusto/concepts/batchingpolicy), wat resulteert in een latentie van een paar minuten.

# <a name="pythontabpython"></a>[Python](#tab/python)

Gebruik python om gegevens op te nemen in de onbewerkte JSON-indeling.

1. Maak de `RawEvents` tabel.

    ```Python
    KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_URI, AAD_TENANT_ID)
    KUSTO_CLIENT = KustoClient(KCSB_DATA)
    TABLE = "RawEvents"

    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Events: dynamic)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Maak de JSON-toewijzing.

    ```Python
    MAPPING = "RawEventMapping"
    CREATE_MAPPING_COMMAND = ".create table " + TABLE + " ingestion json mapping '" + MAPPING + """' '[{"column":"Event","path":"$"}]'"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Gegevens opnemen in de `RawEvents` tabel.

    ```Python
    INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(INGEST_URI, AAD_TENANT_ID)
    INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    > [!NOTE]
    > Gegevens worden geaggregeerd volgens het [batch beleid](/azure/kusto/concepts/batchingpolicy), wat resulteert in een latentie van een paar minuten.

---

## <a name="ingest-mapped-json-records"></a>Door opname toegewezen JSON-records

In dit voor beeld neemt u gegevens van JSON-records op. Elke JSON-eigenschap is toegewezen aan één kolom in de tabel. 

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. Maak een nieuwe tabel met een vergelijkbaar schema voor de JSON-invoer gegevens. Deze tabel wordt gebruikt voor alle volgende voor beelden en opname opdrachten. 

    ```Kusto
    .create table Events (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)
    ```

1. Maak de JSON-toewijzing.

    ```Kusto
    .create table Events ingestion json mapping 'FlatEventMapping' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'
    ```

    In deze toewijzing, zoals gedefinieerd door het tabel schema, worden de `timestamp` vermeldingen opgenomen in de kolom `Time` als `datetime` gegevens typen.

1. Gegevens opnemen in de `Events` tabel.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=FlatEventMapping)
    ```

    Het bestand ' Simple. json ' heeft een aantal door regels gescheiden JSON-records. De indeling is `json`en de toewijzing die wordt gebruikt in de opdracht opnemen is de `FlatEventMapping` die u hebt gemaakt.

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. Maak een nieuwe tabel met een vergelijkbaar schema voor de JSON-invoer gegevens. Deze tabel wordt gebruikt voor alle volgende voor beelden en opname opdrachten. 

    ```C#
    var table = "Events";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Time", "System.DateTime"),
                Tuple.Create("Device", "System.String"),
                Tuple.Create("MessageId", "System.String"),
                Tuple.Create("Temperature", "System.Double"),
                Tuple.Create("Humidity", "System.Double"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Maak de JSON-toewijzing.

    ```C#
    var tableMapping = "FlatEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.timestamp"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.deviceId"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.messageId"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.temperature"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.humidity"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

    In deze toewijzing, zoals gedefinieerd door het tabel schema, worden de `timestamp` vermeldingen opgenomen in de kolom `Time` als `datetime` gegevens typen.    

1. Gegevens opnemen in de `Events` tabel.

    ```C#
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

    Het bestand ' Simple. json ' heeft een aantal door regels gescheiden JSON-records. De indeling is `json`en de toewijzing die wordt gebruikt in de opdracht opnemen is de `FlatEventMapping` die u hebt gemaakt.

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Maak een nieuwe tabel met een vergelijkbaar schema voor de JSON-invoer gegevens. Deze tabel wordt gebruikt voor alle volgende voor beelden en opname opdrachten. 

    ```Python
    TABLE = "RawEvents"
    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Maak de JSON-toewijzing.

    ```Python
    MAPPING = "FlatEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Gegevens opnemen in de `Events` tabel.

    ```Python
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    Het bestand ' Simple. json ' heeft een aantal regels gescheiden van JSON-records. De indeling is `json`en de toewijzing die wordt gebruikt in de opdracht opnemen is de `FlatEventMapping` die u hebt gemaakt.    
---

## <a name="ingest-multi-lined-json-records"></a>Meerdere geregelde JSON-records opnemen

In dit voor beeld neemt u meerdere geregelde JSON-records op. Elke JSON-eigenschap is toegewezen aan één kolom in de tabel. Het bestand ' meerdere regels. json ' heeft een aantal Inge sprongen JSON-records. De indeling `multijson` vertelt dat de Engine records moet lezen op basis van de JSON-structuur.

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

Gegevens opnemen in de `Events` tabel.

```Kusto
.ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=FlatEventMapping)
```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

Gegevens opnemen in de `Events` tabel.

```C#
var tableMapping = "FlatEventMapping";
var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
var properties =
    new KustoQueuedIngestionProperties(database, table)
    {
        Format = DataSourceFormat.multijson,
        IngestionMappingReference = tableMapping
    };

ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Gegevens opnemen in de `Events` tabel.

```Python
MAPPING = "FlatEventMapping"
BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.JSON?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
```

---

## <a name="ingest-json-records-containing-arrays"></a>JSON-records opnemen die matrices bevatten

Matrix gegevens typen zijn een geordende verzameling waarden. Opname van een JSON-matrix wordt uitgevoerd door een [Update beleid](/azure/kusto/management/update-policy). De JSON wordt geconsumeerd als een tussenliggende tabel. Een update beleid voert een vooraf gedefinieerde functie uit in de tabel `RawEvents`, waarbij de resultaten worden teruggestuurd naar de doel tabel. Er worden gegevens opgenomen met de volgende structuur:

```json
{
    "records": 
    [
        {
            "timestamp": "2019-05-02 15:23:50.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "7f316225-839a-4593-92b5-1812949279b3",
            "temperature": 31.0301639051317,
            "humidity": 62.0791099602725
        },
        {
            "timestamp": "2019-05-02 15:23:51.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "57de2821-7581-40e4-861e-ea3bde102364",
            "temperature": 33.7529423105311,
            "humidity": 75.4787976739364
        }
    ]
}
```

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. Maak een `update policy`-functie die de verzameling `records` uitbreidt, zodat elke waarde in de verzameling een afzonderlijke rij ontvangt met behulp van de operator `mv-expand`. We gebruiken tabel `RawEvents` als bron tabel en `Events` als doel tabel.

    ```Kusto
    .create function EventRecordsExpand() {
        RawEvents
        | mv-expand records = Event
        | project
            Time = todatetime(records["timestamp"]),
            Device = tostring(records["deviceId"]),
            MessageId = tostring(records["messageId"]),
            Temperature = todouble(records["temperature"]),
            Humidity = todouble(records["humidity"])
    }
    ```

1. Het schema dat door de functie wordt ontvangen, moet overeenkomen met het schema van de doel tabel. Gebruik de operator `getschema` om het schema te controleren.

    ```Kusto
    EventRecordsExpand() | getschema
    ```

1. Voeg het update beleid toe aan de doel tabel. Met dit beleid wordt de query automatisch uitgevoerd op nieuwe opgenomen gegevens in de `RawEvents` tussenliggende tabel en worden de resultaten opgenomen in de tabel `Events`. Definieer een beleid voor 0 bewaren om te voor komen dat de tussenliggende tabel persistent wordt gemaakt.

    ```Kusto
    .alter table Events policy update @'[{"Source": "RawEvents", "Query": "EventRecordsExpand()", "IsEnabled": "True"}]'
    ```

1. Gegevens opnemen in de `RawEvents` tabel.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=RawEventMapping)
    ```

1. Controleer de gegevens in de tabel `Events`.

    ```Kusto
    Events
    ```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. Maak een update functie die de verzameling `records` uitbreidt, zodat elke waarde in de verzameling een afzonderlijke rij ontvangt met behulp van de `mv-expand` operator. We gebruiken tabel `RawEvents` als bron tabel en `Events` als doel tabel.   

    ```C#
    var command =
        CslCommandGenerator.GenerateCreateFunctionCommand(
            "EventRecordsExpand",
            "UpdateFunctions",
            string.Empty,
            null,
            @"RawEvents
                | mv-expand records = Event
                | project
                    Time = todatetime(records['timestamp']),
                    Device = tostring(records['deviceId']),
                    MessageId = tostring(records['messageId']),
                    Temperature = todouble(records['temperature']),
                    Humidity = todouble(records['humidity'])",
            ifNotExists: false);

    kustoClient.ExecuteControlCommand(command);
    ```

    > [!NOTE]
    > Het schema dat door de functie wordt ontvangen, moet overeenkomen met het schema van de doel tabel.

1. Voeg het update beleid toe aan de doel tabel. Met dit beleid wordt de query automatisch uitgevoerd op nieuwe opgenomen gegevens in de `RawEvents` tussenliggende tabel en worden de resultaten opgenomen in de tabel `Events`. Definieer een beleid voor 0 bewaren om te voor komen dat de tussenliggende tabel persistent wordt gemaakt.

    ```C#
    var command =
        ".alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]";

    kustoClient.ExecuteControlCommand(command);
    ```

1. Gegevens opnemen in de `RawEvents` tabel.

    ```C#
    var table = "RawEvents";
    var tableMapping = "RawEventMapping";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```
    
1. Controleer de gegevens in de tabel `Events`.

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Maak een update functie die de verzameling `records` uitbreidt, zodat elke waarde in de verzameling een afzonderlijke rij ontvangt met behulp van de `mv-expand` operator. We gebruiken tabel `RawEvents` als bron tabel en `Events` als doel tabel.   

    ```Python
    CREATE_FUNCTION_COMMAND = 
        '''.create function EventRecordsExpand() { 
            RawEvents
            | mv-expand records = Event
            | project
                Time = todatetime(records["timestamp"]),
                Device = tostring(records["deviceId"]),
                MessageId = tostring(records["messageId"]),
                Temperature = todouble(records["temperature"]),
                Humidity = todouble(records["humidity"])
            }'''
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_FUNCTION_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

    > [!NOTE]
    > Het schema dat door de functie wordt ontvangen, moet overeenkomen met het schema van de doel tabel.

1. Voeg het update beleid toe aan de doel tabel. Met dit beleid wordt de query automatisch uitgevoerd op nieuwe opgenomen gegevens in de `RawEvents` tussenliggende tabel en worden de resultaten opgenomen in de tabel `Events`. Definieer een beleid voor 0 bewaren om te voor komen dat de tussenliggende tabel persistent wordt gemaakt.

    ```Python
    CREATE_UPDATE_POLICY_COMMAND = 
        """.alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_UPDATE_POLICY_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Gegevens opnemen in de `RawEvents` tabel.

    ```Python
    TABLE = "RawEvents"
    MAPPING = "RawEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

1. Controleer de gegevens in de tabel `Events`.

---    

## <a name="ingest-json-records-containing-dictionaries"></a>JSON-records opnemen die woorden boeken bevatten

Gestructureerde JSON van woorden lijst bevat sleutel-waardeparen. JSON-records worden opgenomen in de opname toewijzing met een logische expressie in de `JsonPath`. U kunt gegevens opnemen met de volgende structuur:

```json
{
    "event": 
    [
        {
            "Key": "timestamp",
            "Value": "2019-05-02 15:23:50.0000000"
        },
        {
            "Key": "deviceId",
            "Value": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71"
        },
        {
            "Key": "messageId",
            "Value": "7f316225-839a-4593-92b5-1812949279b3"
        },
        {
            "Key": "temperature",
            "Value": 31.0301639051317
        },
        {
            "Key": "humidity",
            "Value": 62.0791099602725
        }
    ]
}
```

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. Maak een JSON-toewijzing.

    ```Kusto
    .create table Events ingestion json mapping 'KeyValueEventMapping' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'
    ```

1. Gegevens opnemen in de `Events` tabel.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=KeyValueEventMapping)
    ```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. Maak een JSON-toewijzing.

    ```C#
    var tableName = "Events";
    var tableMapping = "KeyValueEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.event[?(@.Key == 'timestamp')]"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.event[?(@.Key == 'deviceId')]"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.event[?(@.Key == 'messageId')]"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.event[?(@.Key == 'temperature')]"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.event[?(@.Key == 'humidity')]"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Gegevens opnemen in de `Events` tabel.

    ```C#
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Maak een JSON-toewijzing.

    ```Python
    MAPPING = "KeyValueEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Gegevens opnemen in de `Events` tabel.

     ```Python
    MAPPING = "KeyValueEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)u
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

---    

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van gegevens opname](ingest-data-overview.md)
* [Query's schrijven](write-queries.md)