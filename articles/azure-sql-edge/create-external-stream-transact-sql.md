---
title: EXTERNE STREAM maken (Transact-SQL)-Azure SQL Edge (preview)
description: Meer informatie over de instructie externe STREAM maken in Azure SQL Edge (preview)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 9e1f672a62ee7687fec9cea96ca03240c893ba95
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233335"
---
# <a name="create-external-stream-transact-sql"></a>EXTERNE STREAM maken (Transact-SQL)

Het externe stroom object heeft een dubbel doel van zowel invoer als uitvoer. Het kan worden gebruikt als invoer voor het uitvoeren van query's in streaminggegevens van services voor gebeurtenis opname, zoals Azure event of IoT hubs, of wordt gebruikt als uitvoer om op te geven waar en hoe resultaten van een streaming-query moeten worden opgeslagen.

Een externe stroom kan ook worden opgegeven en gemaakt als uitvoer en invoer voor services zoals Event hub of Blob Storage. Dit geldt voor Chaining-scenario's waarbij een streaming-query de resultaten van de externe stream als uitvoer persistent maakt en een andere streaming-query die uit dezelfde externe stroom als invoer wordt gelezen. 


## <a name="syntax"></a>Syntaxis

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
(column definition [,column definitions ] * ) --Used for input - optional 
WITH  
(  
  DATA_SOURCE = <data_source_name>, 
  LOCATION = <location_name>, 
  EXTERNAL_FILE_FORMAT = <external_file_format_name>, --Used for input - optional 
   
  INPUT_OPTIONS =  
    ‘CONSUMER_GROUP=<consumer_group_name>; 
    ‘TIME_POLICY=<time_policy>; 
    LATE_EVENT_TOLERANCE=<late_event_tolerance_value>; 
    OUT_OF_ORDER_EVENT_TOLERANCE=<out_of_order_tolerance_value> 
     
    /* Edge options */ 
  , 
  OUTPUT_OPTIONS =  
    ‘REJECT_POLICY=<reject_policy>; 
    MINIMUM_ROWS=<row_value>; 
    MAXIMUM_TIME=<time_value_minutes>; 
    PARTITION_KEY_COLUMN=<partition_key_column_name>; 
    PROPERTY_COLUMNS=(); 
    SYSTEM_PROPERTY_COLUMNS=(); 
    PARTITION_KEY=<partition_key_name>; 
    ROW_KEY=<row_key_name>; 
    BATCH_SIZE=<batch_size_value>; 
    MAXIMUM_BATCH_COUNT=<batch_value>;  
    STAGING_AREA=<blob_data_source>’ 
     
    /* Edge options */ TAGS=<tag_column_value> 
); 
```


## <a name="arguments"></a>Argumenten


- [EXTERNE GEGEVENS BRON](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [EXTERNE BESTANDS INDELING](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **Locatie**: Hiermee geeft u de naam op van de werkelijke gegevens of locatie in de gegevens bron. In het geval van een Edge hub-of Kafka-stroom object, locatie geeft de naam van de Edge hub of het Kafka onderwerp op om te lezen van of schrijven naar.
- **INPUT_OPTIONS**: Geef opties op als sleutel-waardeparen voor services zoals gebeurtenis-en IOT-hubs die worden uitgevoerd voor het streamen van query's
    - CONSUMER_GROUP: gebeurtenis-en IoT-hubs beperken het aantal lezers binnen één consumer groep (tot 5). Als dit veld leeg blijft, wordt de gebruikers groep ' $Default ' gebruikt.
      - Van toepassing op gebeurtenis-en IOT-hubs
    - TIME_POLICY: beschrijft of gebeurtenissen moeten worden verwijderd of de gebeurtenis tijd moet worden aangepast wanneer er gebeurtenissen of gebeurtenissen met een aflopende volg orde worden door gegeven aan de tolerantie waarde.
      - Van toepassing op gebeurtenis-en IOT-hubs
    - LATE_EVENT_TOLERANCE: de Maxi maal toegestane tijds vertraging. De vertraging geeft het verschil aan tussen de tijds tempel van de gebeurtenis en de systeem klok.
      - Van toepassing op gebeurtenis-en IOT-hubs
    - OUT_OF_ORDER_EVENT_TOLERANCE: gebeurtenissen kunnen buiten de juiste volg orde komen nadat ze de reis hebben gemaakt van de invoer naar de streaming-query. Deze gebeurtenissen kunnen worden geaccepteerd als-is, of u kunt ervoor kiezen om een bepaalde periode te onderbreken om ze opnieuw te rangschikken.
      - Van toepassing op gebeurtenis-en IOT-hubs
- **OUTPUT_OPTIONS**: Geef opties op als sleutel-waardeparen voor ondersteunde services die worden uitgevoerd voor het streamen van query's 
  - REJECT_POLICY: DROP | De soort nieuwe poging voor het verwerken van gegevens fouten wanneer er fouten in de gegevens conversie optreden. 
    - Is van toepassing op alle ondersteunde uitvoer bewerkingen 
  - MINIMUM_ROWS:  
    Mini maal vereiste rijen per batch die naar een uitvoer wordt geschreven. Voor Parquet maakt elke batch een nieuw bestand. 
    - Is van toepassing op alle ondersteunde uitvoer bewerkingen 
  - MAXIMUM_TIME:  
    Maximale wacht tijd per batch. Na deze periode wordt de batch naar de uitvoer geschreven, zelfs als niet aan de vereiste voor de minimum rijen wordt voldaan. 
    - Is van toepassing op alle ondersteunde uitvoer bewerkingen 
  - PARTITION_KEY_COLUMN:  
    De kolom die wordt gebruikt voor de partitie sleutel. 
    - Is van toepassing op de Event hub en het Service Bus onderwerp 
  - PROPERTY_COLUMNS:  
    Een door komma's gescheiden lijst met de namen van uitvoer kolommen die als aangepaste eigenschappen aan berichten worden toegevoegd.  
    - Van toepassing op Event hub en Service Bus onderwerp en wachtrij 
  - SYSTEM_PROPERTY_COLUMNS:  
    Een JSON-indeling met naam/waarde-paren van namen en uitvoer kolommen van systeem eigenschappen die moeten worden ingevuld op Service Bus berichten. bijvoorbeeld {"MessageId": "Kolom1", "PartitionKey": "Kolom2"} 
    - Is van toepassing op Service Bus onderwerp en wachtrij 
  - PARTITION_KEY:  
    De naam van de uitvoer kolom met de partitie sleutel. De partitie sleutel is een unieke id voor de partitie in een bepaalde tabel die het eerste deel vormt van de primaire sleutel van een entiteit. Het is een teken reeks waarde die een grootte van Maxi maal 1 KB kan hebben. 
    - Van toepassing op Table Storage en Azure function 
  - ROW_KEY:  
    De naam van de uitvoer kolom met de rij-sleutel. De rij is een unieke id voor een entiteit binnen een bepaalde partitie. Het vormt het tweede deel van de primaire sleutel van een entiteit. De rij is een teken reeks waarde die een grootte van Maxi maal 1 KB kan hebben. 
    - Van toepassing op Table Storage en Azure function 
  - BATCH_SIZE:  
    Hiermee wordt het aantal trans acties voor de tabel opslag aangegeven, waarbij het maximum tot 100 records kan gaan. Voor Azure Functions vertegenwoordigt dit de Batch grootte in bytes die worden verzonden naar de functie per aanroep. de standaard waarde is 256 kB. 
    - Van toepassing op Table Storage en Azure function 
  - MAXIMUM_BATCH_COUNT:  
    Het maximum aantal gebeurtenissen dat is verzonden naar de functie per aanroep voor Azure function: de standaard waarde is 100. Voor SQL Database vertegenwoordigt dit het maximum aantal records dat wordt verzonden met elke bulksgewijze insert-trans actie. de standaard waarde is 10.000. 
    - Van toepassing op SQL Database en Azure function 
  - STAGING_AREA: extern gegevens bron object voor het Blob Storage van het faserings gebied voor gegevens opname met hoge door Voer in SQL Data Warehouse 
    - Is van toepassing op SQL Data Warehouse


## <a name="examples"></a>Voorbeelden

### <a name="example-1---edgehub"></a>Voor beeld 1-EdgeHub

Type: invoer of uitvoer<br>
Parameters:
- Invoer of uitvoer
  - Alias 
  - Serialisatie-indeling voor gebeurtenissen 
  - Encoding 
- Alleen invoer: 
  - Gebeurteniscompressietype 

Syntaxis:

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub 
WITH  
(      
  LOCATION = 'edgehub://'       
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat  
WITH (  
   FORMAT_TYPE = 'JSON', 
); 
 
CREATE EXTERNAL STREAM Stream_A  
WITH    
(   
   DATA_SOURCE = MyEdgeHub, 
   EXTERNAL_FILE_FORMAT = myFileFormat, 
   LOCATION = ‘<mytopicname>’, 
   OUTPUT_OPTIONS =   
     ‘REJECT_TYPE: Drop’ 
);
```


### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>Voor beeld 2-Azure SQL Database, Azure SQL Edge, SQL Server

Type: uitvoer<br>
Parameters:
- Uitvoeralias  
- Data Base (vereist voor SQL Database) 
- Server (vereist voor SQL Database) 
- Gebruikers naam (vereist voor SQL Database) 
- Wacht woord (vereist voor SQL Database) 
- Tabel 
- Alle invoer partities samen voegen tot één partitie schema voor schrijven of overnemen van een vorige query stap of-invoer (vereist voor SQL Database) 
- Maximum aantal batches 

Syntaxis:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Database/Edge 
CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = ‘<DatabaseName>.<SchemaName>.<TableName>’ 
   --Note: If table is container in the database, <TableName> should be sufficient 
   --Note: Do not need external file format in this case 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop 
); 
```

### <a name="example-3---kafka"></a>Voor beeld 3-Kafka

Type: invoer<br>
Parameters:

- Boots trap-server Kafka 
- Naam van Kafka-onderwerp 
- Aantal bron partities 

Syntaxis:

```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = ' kafka://<kafkaserver>:<ipaddress>’, 
  CREDENTIAL = kafkaCredName 
 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = ‘<KafkaTopicName>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 5’ 
); 
```

### <a name="example-4---blob-storage"></a>Voor beeld 4-Blob-opslag

Type: invoer of uitvoer<br>
Parameters:
- Invoer of uitvoer:
  - Alias 
  - Storage-account 
  - Sleutel van het opslag account 
  - Container 
  - Padpatroon 
  - Datum notatie 
  - Tijd notatie 
  - Serialisatie-indeling voor gebeurtenissen 
  - Encoding 
- Alleen invoer: 
  - Partities (invoer) 
  - Type gebeurtenis compressie (invoer) 
- Alleen uitvoer: 
  - Minimum aantal rijen (uitvoer) 
  - Maximum tijd (uitvoer) 
  - Verificatie modus (uitvoer) 

Syntaxis:

```sql
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = 'AccountKey'; 
 
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredNameMSI 
WITH IDENTITY = 'Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyBlobStorage_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATE_FORMAT = 'YYYY/MM/DD HH', --Both Date and Time format 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyBlobStorage_tweets, 
    LOCATION = ‘<path_pattern>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 1’, 
  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: (), 
      MINUMUM_ROWS: 100000, 
      MAXIMUM_TIME: 60’ 
); 
```

### <a name="example-5---event-hub"></a>Voor beeld 5-Event hub

Type: invoer of uitvoer<br>
Parameters:
- Invoer of uitvoer:
  - Alias 
  - Service Bus naam ruimte 
  - Event Hub-naam 
  - Naam van het Event Hub-beleid 
  - Event hub-beleids sleutel 
  - Serialisatie-indeling voor gebeurtenissen 
  - Encoding 
- Alleen invoer: 
  - Event Hub-consumentengroep 
  - Gebeurteniscompressietype 
- Alleen uitvoer: 
  - Partitie sleutel kolom 
  - Eigenschappen kolommen 

Syntaxis:

```sql
CREATE DATABASE SCOPED CREDENTIAL eventHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyEventHub_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyEventHub_tweets, 
    LOCATION = ‘<topicname>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
 
    INPUT_OPTIONS =  
      ‘CONSUMER_GROUP: FirstConsumerGroup’, 
          
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: ()’ 
);
```

### <a name="example-6---iot-hub"></a>Voor beeld 6-IOT-hub

Type: invoer<br>
Parameters:

- Invoeralias 
- IoT Hub 
- Eindpunt 
- Naam van gedeeld toegangsbeleid 
- Sleutel voor gedeeld toegangs beleid 
- Consumentengroep 
- Serialisatie-indeling voor gebeurtenissen 
- Encoding 
- Gebeurteniscompressietype 

Syntaxis:

```sql
CREATE DATABASE SCOPED CREDENTIAL IoTHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyIoTHub_tweets 
WITH 
(     
  LOCATION = ' iot://iot_hub_name.azure-devices.net’, 
  CREDENTIAL = IoTHubCredName 
);  

CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyIoTHub_tweets, 
    LOCATION = ‘<name>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘ENDPOINT: Messaging, 
      CONSUMER_GROUP: ‘FirstConsumerGroup’ 
); 
```

### <a name="example-7---azure-synapse-analytics-formerly-sql-data-warehouse"></a>Voor beeld 7-Azure Synapse Analytics (voorheen SQL Data Warehouse)

Type: uitvoer<br>
Parameters:
- Uitvoeralias 
- Database 
- server 
- Gebruikersnaam 
- Wachtwoord 
- Tabel 
- Faserings gebied (voor kopiëren) 

Syntaxis:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTable 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
CREATE EXTERNAL STREAM MySQLTableOutput 
WITH ( 
   DATA_SOURCE = MyTargetSQLTable, 
   LOCATION = ‘<TableName>’ 
   --Note: Do not need external file format in this case 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     STAGING_AREA: staging_area_data_source’, 
); 
```


### <a name="example-8---table-storage"></a>Voor beeld 8-tabel opslag

Type: uitvoer<br>
Parameters:
- Uitvoeralias 
- Storage-account 
- Sleutel van het opslag account 
- Tabelnaam 
- Partitiesleutel 
- Rij-sleutel 
- Batchgrootte 

Syntaxis:

```sql
CREATE DATABASE SCOPED CREDENTIAL TableStorageCredName 
WITH IDENTITY = ‘Storage account Key’, 
SECRET = '<storage_account_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY: <column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


### <a name="example-9---service-bus-topic-same-properties-as-queue"></a>Voor beeld van 9-Service Bus onderwerp (dezelfde eigenschappen als de wachtrij)

Type: uitvoer<br>
Parameters:
- Uitvoeralias 
- Service Bus naam ruimte 
- Onderwerpnaam 
- Naam van onderwerps beleid 
- Sleutel van het onderwerpbeleid 
- Eigenschappen kolommen 
- Kolom systeem eigenschappen 
- Serialisatie-indeling voor gebeurtenissen 
- Encoding 

Syntaxis:

```sql
CREATE DATABASE SCOPED CREDENTIAL serviceBusCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyServiceBus_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = serviceBusCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM MyServiceBusOutput 
WITH ( 
   DATA_SOURCE = MyServiceBus_tweets, 
   LOCATION = ‘<topic_name>’, 
   EXTERNAL_FILE_FORMAT = myFileFormat 
       OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY_COLUMN: , 
     PROPERTY_COLUMNS: (), 
     SYSTEM_PROPERTY_COLUMNS: ()’ 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
           
); 
```


### <a name="example-10---cosmos-db"></a>Voor beeld 10-Cosmos DB

Type: uitvoer<br>
Parameters:
- Uitvoeralias 
- Account-id 
- Accountsleutel 
- Database 
- Containernaam 
- Document-ID 


Syntaxis:

```sql
CREATE DATABASE SCOPED CREDENTIAL cosmosDBCredName 
WITH IDENTITY = ‘Storage Account Key’, 
SECRET = '<accountKey>'; 
 
CREATE EXTERNAL DATA SOURCE MyCosmosDB_tweets 
WITH 
(     
  LOCATION = ' cosmosdb://accountid.documents.azure.com:443/ database’, 
  CREDENTIAL = cosmosDBCredName 
); 
 
CREATE EXTERNAL STREAM MyCosmosDBOutput 
WITH ( 
   DATA_SOURCE = MyCosmosDB_tweets, 
   LOCATION = ‘<container/documentID>’ 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop', 
     --Note: Do not need external file format in this case 
          
);
```



### <a name="example-11---power-bi"></a>Voor beeld 11-Power BI

Type: uitvoer<br>
Parameters:
- Uitvoeralias 
- Naam van de gegevensset 
- Tabelnaam 


Syntaxis:

```sql
CREATE DATABASE SCOPED CREDENTIAL PBIDBCredName 
WITH IDENTITY = ‘Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyPbi_tweets 
WITH 
( 
  LOCATION = 'pbi://dataset/’, 
  CREDENTIAL = PBIDBCredName 
 
); 
 
CREATE EXTERNAL STREAM MyPbiOutput 
WITH ( 
   DATA_SOURCE = MyPbi_tweets, 
   LOCATION = 'tableName', 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop' 
        
);
```

### <a name="example-12---azure-function"></a>Voor beeld 12-Azure-functie

Type: uitvoer<br>
Parameters:
- Function App 
- Functie 
- Sleutel 
- Maximale Batch grootte 
- Maximum aantal batches 

Syntaxis:

```sql
CREATE DATABASE SCOPED CREDENTIAL AzureFunctionCredName 
WITH IDENTITY = ‘Function Key’, 
SECRET = '<function_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: 'Drop'      
     PARTITION_KEY: ‘<column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


## <a name="see-also"></a>Zie ook

- [EXTERNE stroom wijzigen (Transact-SQL)](alter-external-stream-transact-sql.md) 
- [EXTERNE stroom verwijderen (Transact-SQL)](drop-external-stream-transact-sql.md) 

