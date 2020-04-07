---
title: Opgenomen JSON-opgemaakte gegevens in Azure Data Explorer
description: Meer informatie over het innemen van OPGEMAAKTE JSON-gegevens in Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: bcf6a0ccfc04890052f1a4bab19f97ee4e55f87a
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756627"
---
# <a name="ingest-json-formatted-sample-data-into-azure-data-explorer"></a>Opgenomen JSON-opgemaakte voorbeeldgegevens in Azure Data Explorer

In dit artikel ziet u hoe u OPgemaakte JSON-gegevens inneemt in een Azure Data Explorer-database. Je begint met eenvoudige voorbeelden van ruwe en toegewezen JSON, gaat door met Json met meerdere teksten en pakt vervolgens complexere JSON-schema's aan met arrays en woordenboeken.  De voorbeelden beschrijven het proces van het innemen van JSON-opgemaakte gegevens met Kusto-querytaal (KQL), C#of Python. De opdrachten voor `ingest` het besturingselement voor query's van Kusto-query's worden rechtstreeks naar het eindpunt van de engine uitgevoerd. In productiescenario's wordt opname uitgevoerd naar de dienst Gegevensbeheer met behulp van clientbibliotheken of gegevensverbindingen. Lees [Inname gegevens met behulp van de Azure Data Explorer Python-bibliotheek](/azure/data-explorer/python-ingest-data) en [innamegegevens met behulp van de Azure Data Explorer .NET Standard SDK](/azure/data-explorer/net-standard-ingest-data) voor een walk-through met betrekking tot het innemen van gegevens met deze clientbibliotheken.

## <a name="prerequisites"></a>Vereisten

[Een cluster en database voor testdoeleinden](create-cluster-database-portal.md)

## <a name="the-json-format"></a>De JSON-indeling

Azure Data Explorer ondersteunt twee JSON-bestandsindelingen:
* `json`: Lijn gescheiden JSON. Elke regel in de invoergegevens heeft precies één JSON-record.
* `multijson`: Multi-lined JSON. De parser negeert de lijnscheidingstekens en leest een record van de vorige positie tot het einde van een geldige JSON.

### <a name="ingest-and-map-json-formatted-data"></a>Inname en kaart JSON-opgemaakte gegevens

Voor de opname van JSON-opgemaakte gegevens moet u de *indeling* opgeven met de [eigenschap Inname.](/azure/kusto/management/data-ingestion/index#ingestion-properties) Inname van JSON-gegevens vereist [toewijzing,](/azure/kusto/management/mappings)die een JSON-bronvermelding in de doelkolom in kaart brengt. Gebruik bij het innemen van gegevens `jsonMappingReference` de vooraf `jsonMapping`gedefinieerde eigenschap of specificeert de eigenschap opname. In dit artikel `jsonMappingReference` wordt gebruik gemaakt van de eigenschap inname, die vooraf is gedefinieerd op de tabel die wordt gebruikt voor inname. In de onderstaande voorbeelden beginnen we met het opnemen van JSON-records als ruwe gegevens in een enkele kolomtabel. Vervolgens gebruiken we de toewijzing om elke eigenschap in te nemen in de toegewezen kolom. 

### <a name="simple-json-example"></a>Eenvoudig JSON-voorbeeld

Het volgende voorbeeld is een eenvoudige JSON, met een platte structuur. De gegevens bevatten temperatuur- en vochtigheidsinformatie, verzameld door verschillende apparaten. Elke record is gemarkeerd met een ID en tijdstempel.

```json
{
    "timestamp": "2019-05-02 15:23:50.0369439",
    "deviceId": "2945c8aa-f13e-4c48-4473-b81440bb5ca2",
    "messageId": "7f316225-839a-4593-92b5-1812949279b3",
    "temperature": 31.0301639051317,
    "humidity": 62.0791099602725
}
```

## <a name="ingest-raw-json-records"></a>Inname van raw JSON records 

In dit voorbeeld neemt u JSON-records in als ruwe gegevens in een enkele kolomtabel. De gegevensmanipulatie, het gebruik van query's en het updatebeleid worden uitgevoerd nadat de gegevens zijn ingenomen.

# <a name="kql"></a>[KQL KQL](#tab/kusto-query-language)

Gebruik Kusto-querytaal om gegevens in te nemen in een ruwe JSON-indeling.

1. Log hier [https://dataexplorer.azure.com](https://dataexplorer.azure.com)in

1. Selecteer **Add Cluster**.

1. Voer **in** het dialoogvenster Cluster toevoegen de `https://<ClusterName>.<Region>.kusto.windows.net/`URL van het cluster in het formulier in en selecteer **Toevoegen**.

1. Plak in de volgende opdracht en selecteer **Uitvoeren** om de tabel te maken.

    ```Kusto
    .create table RawEvents (Event: dynamic)
    ```

    Met deze query wordt `Event` een tabel met één kolom van een [dynamisch](/azure/kusto/query/scalar-data-types/dynamic) gegevenstype.

1. Maak de JSON-toewijzing.

    ```Kusto
    .create table RawEvents ingestion json mapping 'RawEventMapping' '[{"column":"Event","path":"$"}]'
    ```

    Met deze opdracht wordt een toewijzing gemaakt `$` en `Event` wordt het JSON-hoofdpad naar de kolom toegewezen.

1. Gegevens innemen `RawEvents` in de tabel.

    ```Kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=RawEventMapping)
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

Gebruik C# om gegevens in raw JSON-indeling in te nemen.

1. Maak `RawEvents` de tabel.

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
    Met deze opdracht wordt een toewijzing gemaakt `$` en `Event` wordt het JSON-hoofdpad naar de kolom toegewezen.

1. Gegevens innemen `RawEvents` in de tabel.

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
> Gegevens worden geaggregeerd volgens [batchingbeleid,](/azure/kusto/concepts/batchingpolicy)wat resulteert in een latentie van een paar minuten.

# <a name="python"></a>[Python](#tab/python)

Gebruik Python om gegevens in raw JSON-formaat in te nemen.

1. Maak `RawEvents` de tabel.

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

1. Gegevens innemen `RawEvents` in de tabel.

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
    > Gegevens worden geaggregeerd volgens [batchingbeleid,](/azure/kusto/concepts/batchingpolicy)wat resulteert in een latentie van een paar minuten.

---

## <a name="ingest-mapped-json-records"></a>Inkaart gebrachte JSON-records

In dit voorbeeld neemt u JSON records op. Elke JSON-eigenschap wordt toegewezen aan één kolom in de tabel. 

# <a name="kql"></a>[KQL KQL](#tab/kusto-query-language)

1. Maak een nieuwe tabel, met een vergelijkbaar schema als de JSON-invoergegevens. We gebruiken deze tabel voor alle volgende voorbeelden en innameopdrachten. 

    ```Kusto
    .create table Events (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)
    ```

1. Maak de JSON-toewijzing.

    ```Kusto
    .create table Events ingestion json mapping 'FlatEventMapping' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'
    ```

    In deze toewijzing, zoals gedefinieerd door `timestamp` het tabelschema, worden de `Time` `datetime` vermeldingen in de kolom opgenomen als gegevenstypen.

1. Gegevens innemen `Events` in de tabel.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=FlatEventMapping)
    ```

    Het bestand 'simple.json' heeft een paar line-gescheiden JSON records. De indeling `json`is , en de toewijzing die `FlatEventMapping` wordt gebruikt in de inname opdracht is de u hebt gemaakt.

# <a name="c"></a>[C#](#tab/c-sharp)

1. Maak een nieuwe tabel, met een vergelijkbaar schema als de JSON-invoergegevens. We gebruiken deze tabel voor alle volgende voorbeelden en innameopdrachten. 

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

    In deze toewijzing, zoals gedefinieerd door `timestamp` het tabelschema, worden de `Time` `datetime` vermeldingen in de kolom opgenomen als gegevenstypen.    

1. Gegevens innemen `Events` in de tabel.

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

    Het bestand 'simple.json' heeft een paar line-gescheiden JSON records. De indeling `json`is , en de toewijzing die `FlatEventMapping` wordt gebruikt in de inname opdracht is de u hebt gemaakt.

# <a name="python"></a>[Python](#tab/python)

1. Maak een nieuwe tabel, met een vergelijkbaar schema als de JSON-invoergegevens. We gebruiken deze tabel voor alle volgende voorbeelden en innameopdrachten. 

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

1. Gegevens innemen `Events` in de tabel.

    ```Python
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    Het bestand 'simple.json' heeft een paar regel gescheiden JSON records. De indeling `json`is , en de toewijzing die `FlatEventMapping` wordt gebruikt in de inname opdracht is de u hebt gemaakt.    
---

## <a name="ingest-multi-lined-json-records"></a>Inname multi-lined JSON records

In dit voorbeeld neemt u json-records met meerdere lined's in. Elke JSON-eigenschap wordt toegewezen aan één kolom in de tabel. Het bestand 'multilined.json' heeft een paar ingesprongen JSON-records. Het `multijson` formaat vertelt de engine om records te lezen door de JSON-structuur.

# <a name="kql"></a>[KQL KQL](#tab/kusto-query-language)

Gegevens innemen `Events` in de tabel.

```Kusto
.ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=FlatEventMapping)
```

# <a name="c"></a>[C#](#tab/c-sharp)

Gegevens innemen `Events` in de tabel.

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

# <a name="python"></a>[Python](#tab/python)

Gegevens innemen `Events` in de tabel.

```Python
MAPPING = "FlatEventMapping"
BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.JSON?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
```

---

## <a name="ingest-json-records-containing-arrays"></a>Json-records innemen die arrays bevatten

Arraygegevenstypen zijn een geordende verzameling waarden. Inname van een JSON-array gebeurt door een [updatebeleid](/azure/kusto/management/update-policy). Json wordt ingenomen as-is aan een middenlijst. In een updatebeleid wordt een vooraf `RawEvents` gedefinieerde functie op de tabel uitgevoerd, waarbij de resultaten opnieuw worden toegerust in de doeltabel. We zullen gegevens innemen met de volgende structuur:

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

# <a name="kql"></a>[KQL KQL](#tab/kusto-query-language)

1. Maak `update policy` een functie die `records` de verzameling uitbreidt, zodat elke waarde in `mv-expand` de verzameling een aparte rij ontvangt, met behulp van de operator. We gebruiken de `RawEvents` tabel als `Events` brontabel en als doeltabel.

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

1. Het schema dat door de functie wordt ontvangen, moet overeenkomen met het schema van de doeltabel. Gebruik `getschema` operator om het schema te bekijken.

    ```Kusto
    EventRecordsExpand() | getschema
    ```

1. Voeg het updatebeleid toe aan de doeltabel. Met dit beleid wordt de query automatisch uitgevoerd `RawEvents` op nieuw ingenomen gegevens `Events` in de tussentabel en worden de resultaten in de tabel opgenomen. Definieer een beleid voor nulbehoud om te voorkomen dat de tussenliggende tabel blijft bestaan.

    ```Kusto
    .alter table Events policy update @'[{"Source": "RawEvents", "Query": "EventRecordsExpand()", "IsEnabled": "True"}]'
    ```

1. Gegevens innemen `RawEvents` in de tabel.

    ```Kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=RawEventMapping)
    ```

1. Gegevens in `Events` de tabel bekijken.

    ```Kusto
    Events
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

1. Maak een updatefunctie waarmee `records` de verzameling wordt uitgebreid, zodat elke waarde `mv-expand` in de verzameling een aparte rij ontvangt, met behulp van de operator. We gebruiken de `RawEvents` tabel als `Events` brontabel en als doeltabel.   

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
    > Het schema dat door de functie wordt ontvangen, moet overeenkomen met het schema van de doeltabel.

1. Voeg het updatebeleid toe aan de doeltabel. Met dit beleid wordt de query automatisch uitgevoerd `RawEvents` op nieuw ingenomen gegevens `Events` in de tussentabel en worden de resultaten ervan in de tabel opgenomen. Definieer een beleid voor nulbehoud om te voorkomen dat de tussenliggende tabel blijft bestaan.

    ```C#
    var command =
        ".alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]";

    kustoClient.ExecuteControlCommand(command);
    ```

1. Gegevens innemen `RawEvents` in de tabel.

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
    
1. Gegevens in `Events` de tabel bekijken.

# <a name="python"></a>[Python](#tab/python)

1. Maak een updatefunctie waarmee `records` de verzameling wordt uitgebreid, zodat elke waarde `mv-expand` in de verzameling een aparte rij ontvangt, met behulp van de operator. We gebruiken de `RawEvents` tabel als `Events` brontabel en als doeltabel.   

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
    > Het schema dat door de functie wordt ontvangen, moet overeenkomen met het schema van de doeltabel.

1. Voeg het updatebeleid toe aan de doeltabel. Met dit beleid wordt de query automatisch uitgevoerd `RawEvents` op nieuw ingenomen gegevens `Events` in de tussentabel en worden de resultaten ervan in de tabel opgenomen. Definieer een beleid voor nulbehoud om te voorkomen dat de tussenliggende tabel blijft bestaan.

    ```Python
    CREATE_UPDATE_POLICY_COMMAND = 
        """.alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_UPDATE_POLICY_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Gegevens innemen `RawEvents` in de tabel.

    ```Python
    TABLE = "RawEvents"
    MAPPING = "RawEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

1. Gegevens in `Events` de tabel bekijken.

---    

## <a name="ingest-json-records-containing-dictionaries"></a>Inname JSON records met woordenboeken

Woordenboek gestructureerde JSON bevat sleutel-waarde paren. Json-records ondergaan innamemapping `JsonPath`met behulp van logische expressie in de . U gegevens opnemen met de volgende structuur:

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

# <a name="kql"></a>[KQL KQL](#tab/kusto-query-language)

1. Maak een JSON-toewijzing.

    ```Kusto
    .create table Events ingestion json mapping 'KeyValueEventMapping' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'
    ```

1. Gegevens innemen `Events` in de tabel.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=KeyValueEventMapping)
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

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

1. Gegevens innemen `Events` in de tabel.

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

# <a name="python"></a>[Python](#tab/python)

1. Maak een JSON-toewijzing.

    ```Python
    MAPPING = "KeyValueEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Gegevens innemen `Events` in de tabel.

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

* [Overzicht van gegevensopname](ingest-data-overview.md)
* [Query's schrijven](write-queries.md)
