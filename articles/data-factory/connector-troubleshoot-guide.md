---
title: Problemen met Azure Data Factory-connectors oplossen
description: Meer informatie over het oplossen van verbindingsproblemen in Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/09/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 9f3a13a097d7cce87aead4ec2d76ce7cbbb1a206
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75778223"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Problemen met Azure Data Factory-connectors oplossen

In dit artikel worden veelvoorkomende probleemoplossingsmethoden voor connectors in Azure Data Factory onderzocht.
  

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code--azurebloboperationfailed"></a>Foutcode: AzureBlobOperation Failed

- **Bericht**:`Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Oorzaak:** Blob-opslagbewerking raakte probleem.

- **Aanbeveling**: Controleer de fout in details. Raadpleeg het helpdocument https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codesblob: . Neem indien nodig contact op met het opslagteam.


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>Foutcode: AzureBlobServiceNotReturnExpectedDataLength

- **Bericht**:`Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>Foutcode: AzureBlobNotSupportMultipleFilesIntoSingleBlob

- **Bericht**:`Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>Foutcode: AzureStorageOperationFailedConcurrentWrite

- **Bericht**:`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Foutbericht: De grootte van het verzoek is te groot

- **Symptomen:** U kopieert gegevens naar Azure Cosmos DB met standaard batchgrootte schrijven en hitfout *" De grootte van het verzoek is te**groot**"*.

- **Oorzaak:** Cosmos DB beperkt de grootte van één enkele aanvraag tot 2 MB. De formule is, Aanvraaggrootte = Enkele documentgrootte * Schrijf batchgrootte. Als de grootte van uw document groot is, resulteert het standaardgedrag in een te grote aanvraaggrootte. U de grootte van de schrijfbatch afstemmen.

- **Resolutie**: Verlaag in de inhoud van de kopieeractiviteit de waarde 'Batchgrootte schrijven' (standaardwaarde is 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Foutbericht: schending van unieke indexbeperking

- **Symptomen:** Bij het kopiëren van gegevens naar Cosmos DB, raakt u de volgende fout:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Oorzaak**: Er zijn twee mogelijke oorzaken:

    - Als u **Invoegen** als schrijfgedrag gebruikt, betekent deze fout dat brongegevens rijen/objecten met dezelfde ID hebben.

    - Als u **Upsert** gebruikt als schrijfgedrag en u een andere unieke sleutel instelt voor de container, betekent deze fout dat u brongegevens rijen/objecten met verschillende id's hebt, maar dezelfde waarde voor de gedefinieerde unieke sleutel.

- **Resolutie**: 

    - Stel Voor oorzaak1 **Upsert** in als schrijfgedrag.
    - Controleer voor oorzaak 2 of elk document een andere waarde heeft voor gedefinieerde unieke sleutel.

### <a name="error-message-request-rate-is-large"></a>Foutbericht: het verzoekpercentage is groot

- **Symptomen:** Bij het kopiëren van gegevens naar Cosmos DB, raakt u de volgende fout:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Oorzaak:** De gebruikte aanvraageenheden zijn groter dan de beschikbare RU die is geconfigureerd in Cosmos DB. Lees hoe Cosmos DB RU berekent vanaf [hier](../cosmos-db/request-units.md#request-unit-considerations).

- **Resolutie**: Hier zijn twee oplossingen:

    1. **Verhoog de container-RU** naar een grotere waarde in Cosmos DB, wat de prestaties van de kopieeractiviteit zal verbeteren, hoewel er meer kosten in Cosmos DB worden gemaakt. 

    2. Verlaag **writeBatchSize** naar kleinere waarde (zoals 1000) en stel **parallelleKopieën** in op kleinere waarde zoals 1, waardoor de prestaties van de copy run slechter worden dan de huidige, maar niet meer kosten zullen opleveren in Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>Kolom ontbreekt in kolomtoewijzing

- **Symptomen:** Wanneer u schema importeert voor Cosmos DB voor kolomtoewijzing, ontbreken sommige kolommen. 

- **Oorzaak**: ADF leidt het schema af uit de eerste 10 Cosmos DB-documenten. Als sommige kolommen/eigenschappen geen waarde hebben in die documenten, worden ze niet gedetecteerd door ADF en worden ze dus niet weergegeven.

- **Oplossing:** U de query als hieronder afstemmen om kolom af te dwingen om te worden weergegeven in het resultaat ingesteld met lege waarde: (neem aan: onmogelijk kolom ontbreekt in de eerste 10 documenten). U de kolom ook handmatig toevoegen voor toewijzing.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Foutmelding: De GuidRepresentation voor de lezer is CSharpLegacy

- **Symptomen**: Bij het kopiëren van gegevens van Cosmos DB MongoAPI/MongoDB met UUID-veld, raakt u de volgende fout:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Oorzaak**: Er zijn twee manieren om UUID te vertegenwoordigen in BSON - UuidStardard en UuidLegacy. UuidLegacy wordt standaard gebruikt om gegevens te lezen. U zult fouten raken als uw UUID-gegevens in MongoDB UuidStandard zijn.

- **Resolutie**: Voeg in mongoDB-verbindingstekenreeks optie "**uuidRepresentation=standard**" toe. Zie [MongoDB-verbindingstekenreeks voor](connector-mongodb.md#linked-service-properties)meer informatie .
            

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code--adlsgen2operationfailed"></a>Foutcode: AdlsGen2OperationFailed

- **Bericht**:`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Oorzaak**: ADLS Gen2 gooit de fout die aangeeft dat de bewerking is mislukt.

- **Aanbeveling**: Controleer de gedetailleerde foutmelding van ADLS Gen2. Als het wordt veroorzaakt door een tijdelijke storing, probeer het dan opnieuw. Als u verdere hulp nodig hebt, neemt u contact op met azure storage-ondersteuning en geeft u de aanvraag-id per foutbericht op.

- **Oorzaak**: Wanneer het foutbericht 'Verboden' bevat, heeft de serviceprincipal of beheerde identiteit die u gebruikt mogelijk niet genoeg toestemming om toegang te krijgen tot de ADLS Gen2.

- **Aanbeveling**: Raadpleeg het https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authenticationHelp-document: .

- **Oorzaak**: Wanneer het foutbericht 'InternalServerError' bevat, wordt de fout geretourneerd door ADLS Gen2.

- **Aanbeveling**: Het kan worden veroorzaakt door tijdelijke storing, probeer opnieuw. Als het probleem blijft bestaan, neemt u contact op met azure storage-ondersteuning en geeft u de aanvraag-id per foutbericht op.


### <a name="error-code--adlsgen2invalidurl"></a>Foutcode: AdlsGen2Ongeldig URL

- **Bericht**:`Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>Foutcode: AdlsGen2InvalidFolderPath

- **Bericht**:`The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>Foutcode: AdlsGen2OperationFailedConcurrentWrite

- **Bericht**:`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code--adlsgen2timeouterror"></a>Foutcode: AdlsGen2TimeoutError

- **Bericht**:`Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Foutbericht: De externe server heeft een fout geretourneerd: (403) Verboden

- **Symptomen:** Kopieeractiviteit mislukt met de volgende fout: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Oorzaak:** Een mogelijke oorzaak is dat de serviceprincipal of beheerde identiteit die u gebruikt geen toestemming heeft om toegang te krijgen tot de bepaalde map/bestand.

- **Oplossing:** Geef bijbehorende machtigingen voor alle mappen en submappen die u moet kopiëren. Raadpleeg [dit document](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Foutbericht: kan geen toegangstoken krijgen met behulp van serviceprincipal. ADAL-fout: service_unavailable

- **Symptomen:** Kopieeractiviteit mislukt met de volgende fout:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Oorzaak:** wanneer de SERVICE Token Server (STS) die eigendom is van Azure Active Directory niet beschikbaar is, d.w.z. te druk om aanvragen te verwerken, wordt een HTTP-fout 503 geretourneerd. 

- **Resolutie:** Voer de kopieeractiviteit na enkele minuten opnieuw uit.
                  

## <a name="azure-sql-data-warehouseazure-sql-databasesql-server"></a>Azure SQL Data Warehouse/Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Foutcode: SqlFailedToConnect

- **Bericht**:`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Oorzaak**: Als het foutbericht 'SqlException' bevat, wordt in SQL Database de fout weergegeven die aangeeft dat een bepaalde specifieke bewerking is mislukt.

- **Aanbeveling:** Zoek op SQL-foutcode in dit https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errorsreferentiedocument voor meer details: . Als u verdere hulp nodig hebt, neemt u contact op met Azure SQL-ondersteuning.

- **Oorzaak**: Als het foutbericht 'Client met IP-adres'...' bevat is niet toegestaan om toegang te krijgen tot de server", en u probeert verbinding te maken met Azure SQL Database, meestal wordt het veroorzaakt door Azure SQL Database firewall probleem.

- **Aanbeveling:** Schakel in Azure SQL Server-firewallconfiguratie de optie 'Azure-services en -bronnen toestaan toegang tot deze server' in te schakelen. Referentiedocument: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure.


### <a name="error-code--sqloperationfailed"></a>Foutcode: SqlOperationFailed

- **Bericht**:`A database operation failed. Please search error to get more details.`

- **Oorzaak**: Als het foutbericht 'SqlException' bevat, wordt in SQL Database de fout weergegeven die aangeeft dat een bepaalde specifieke bewerking is mislukt.

- **Aanbeveling:** Als sql-fout niet duidelijk is, probeer dan de database te wijzigen naar het nieuwste compatibiliteitsniveau '150'. Het kan gooien nieuwste versie SQL-fouten. Raadpleeg het detaildocument: https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?view=sql-server-ver15#backwardCompat.
        Zoek voor het oplossen van SQL-problemen op SQL-foutcode in dit referentiedocument voor meer informatie: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Als u verdere hulp nodig hebt, neemt u contact op met Azure SQL-ondersteuning.

- **Oorzaak**: Als het foutbericht 'PdwManagedToNativeInteropException' bevat, wordt dit meestal veroorzaakt door een mismatch tussen de grootte van de bron- en sinkkolom.

- **Aanbeveling**: Controleer de grootte van zowel bron- als gootsteenkolommen. Als u verdere hulp nodig hebt, neemt u contact op met Azure SQL-ondersteuning.

- **Oorzaak**: Als het foutbericht 'OngeldigeOperatieuitzondering' bevat, wordt dit meestal veroorzaakt door ongeldige invoergegevens.

- **Aanbeveling**: Als u wilt bepalen welke rij het probleem tegenkomt, schakelt u de functie fouttolerantie in voor kopieeractiviteit, die problematische rij(s) naar de opslag kan leiden voor verder onderzoek. Referentiedocument: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlunauthorizedaccess"></a>Foutcode: SqlUnauthorizedAccess

- **Bericht**:`Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Oorzaak:** De referentie is onjuist of het aanmeldingsaccount heeft geen toegang tot SQL Database.

- **Aanbeveling:** Controleer of het inlogaccount voldoende toestemming heeft om toegang te krijgen tot de SQL-database.


### <a name="error-code--sqlopenconnectiontimeout"></a>Foutcode: SqlOpenConnectionTimeout

- **Bericht**:`Open connection to database timeout after '%timeoutValue;' seconds.`

- **Oorzaak:** Kan SQL Database tijdelijke fout zijn.

- **Aanbeveling:** Probeer de tekenreeks gekoppelde serviceverbinding opnieuw bij te werken met een grotere time-outwaarde voor de verbinding.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Foutcode: SqlAutoCreateTableTypeMap Mislukt

- **Bericht**:`Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Oorzaak:** tabel voor het maken van auto's kan niet voldoen aan de bronvereisten.

- **Aanbeveling:** Werk het kolomtype bij in 'toewijzingen' of maak handmatig de sinktabel in doelserver.


### <a name="error-code--sqldatatypenotsupported"></a>Foutcode: SqlDataTypeNotSupported

- **Bericht**:`A database operation failed. Check the SQL errors.`

- **Oorzaak**: Als het probleem optreedt op SQL-bron en de fout gerelateerd is aan SqlDateTime-overloop, is de gegevenswaarde groter dan het logicatypebereik (1/1/1753 12:00:00 AM - 12/31/9999 11:59:59 PM).

- **Aanbeveling:** Cast het type naar tekenreeks in de bron SQL-query of in kolomtoewijzing voor kopieeractiviteit wijzigt u het kolomtype in 'Tekenreeks'.

- **Oorzaak:** Als het probleem optreedt op SQL-sink en de fout is gerelateerd aan SqlDateTime-overloop, is de gegevenswaarde groter dan het toegestane bereik in de sinktabel.

- **Aanbeveling**: Werk het bijbehorende kolomtype bij naar het type 'datetime2' in de tabel wastafel.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Foutcode: SqlInvalidDbStoredProcedure

- **Bericht**:`The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Oorzaak**: De opgegeven opgeslagen procedure is niet geldig. Het kan worden veroorzaakt door dat de opgeslagen procedure geen gegevens retourneert.

- **Aanbeveling:** Valideer de opgeslagen procedure door SQL Tools. Zorg ervoor dat de opgeslagen procedure gegevens kan retourneren.


### <a name="error-code--sqlinvaliddbquerystring"></a>Foutcode: SqlInvalidDbQueryString

- **Bericht**:`The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Oorzaak:** de opgegeven SQL-query is niet geldig. Het kan worden veroorzaakt door dat de query geen gegevens retourneert

- **Aanbeveling:** Valideer de SQL Query door SQL Tools. Zorg ervoor dat de query gegevens kan retourneren.


### <a name="error-code--sqlinvalidcolumnname"></a>Foutcode: SqlInvalidColumnName

- **Bericht**:`Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Oorzaak**: Kan kolom niet vinden. Mogelijke configuratie verkeerd.

- **Aanbeveling:** Controleer de kolom in de query, 'structuur' in de gegevensset en 'toewijzingen' in activiteit.


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>Foutcode: SqlColumnNameMismatchByCaseSensitive

- **Bericht**:`Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>Foutcode: SqlBatchWriteTimeout

- **Bericht**:`Timeouts in SQL write operation.`

- **Oorzaak:** Kan SQL Database tijdelijke fout zijn.

- **Aanbeveling**: Probeer het opnieuw. Als het probleem repro is, neemt u contact op met Azure SQL-ondersteuning.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Foutcode: SqlBatchWriteTransactionFailed

- **Bericht**:`SQL transaction commits failed`

- **Oorzaak**: Als uitzonderingsgegevens voortdurend een time-out van de transactie geven, is de netwerklatentie tussen de runtime van de integratie en de database hoger dan de standaarddrempel waardewaarde als 30 seconden.

- **Aanbeveling**: Update Sql linked service connection string with 'connection timeout' value equals to 120 or hoger and rerun the activity.

- **Oorzaak**: Als uitzonderingsdetails met tussenpozen vertellen dat sqlconnection is verbroken, kan het gewoon een tijdelijke netwerkfout of sql-database-sideissue zijn

- **Aanbeveling:** Probeer de activiteit opnieuw en bekijk sql database-side metrics.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Foutcode: SqlBulkCopyInvalidColumnLength

- **Bericht**:`SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Oorzaak:** SQL Bulk Copy is mislukt omdat een ongeldige kolomlengte van de bcp-client wordt ontvangen.

- **Aanbeveling**: Als u wilt bepalen welke rij het probleem tegenkomt, schakelt u de functie fouttolerantie in voor kopieeractiviteit, die problematische rij(s) naar de opslag kan leiden voor verder onderzoek. Referentiedocument: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlconnectionisclosed"></a>Foutcode: SqlConnectionIsClosed

- **Bericht**:`The connection is closed by SQL Database.`

- **Oorzaak:** SQL-verbinding wordt gesloten door SQL Database wanneer de verbinding met een hoge gelijktijdige run en de server wordt beëindigd.

- **Aanbeveling:** Externe server heeft de SQL-verbinding gesloten. Probeer het opnieuw. Als het probleem repro is, neemt u contact op met Azure SQL-ondersteuning.


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>Foutcode: SqlCreateTableFailedUnsupportedType

- **Bericht**:`Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Foutbericht: Conversie is mislukt bij het converteren van een tekentekenreeks naar een unieke id

- **Symptomen:** Wanneer u gegevens uit tabelgegevensbron (zoals SQL Server) kopieert naar Azure SQL Data Warehouse met gefaseerde kopie en PolyBase, raakt u de volgende fout:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Oorzaak:** Azure SQL Data Warehouse PolyBase kan lege tekenreeks niet converteren naar GUID.

- **Resolutie:** Stel in Groep van kopieeractiviteit onder Polybase-instellingen de optie "**gebruik type standaard**" in op false.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Foutbericht: verwacht gegevenstype: DECIMAL(x,x), Waarde aan overtreding

- **Symptomen:** Wanneer u gegevens uit tabelgegevensbron (zoals SQL Server) kopieert naar SQL DW met gefaseerde kopie en PolyBase, raakt u de volgende fout:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Oorzaak**: Azure SQL Data Warehouse Polybase kan geen lege tekenreeks (null-waarde) in de decimale kolom invoegen.

- **Resolutie:** Stel in Groep van kopieeractiviteit onder Polybase-instellingen de optie "**gebruik type standaard**" in op false.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>Foutbericht: Java-uitzonderingsbericht:HdfsBridge::CreateRecordReader

- **Symptomen:** U kopieert gegevens naar Azure SQL Data Warehouse met PolyBase en slaat de volgende fout:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Oorzaak**: De mogelijke oorzaak is dat het schema (totale kolombreedte) te groot is (groter dan 1 MB). Controleer het schema van de doel-SQL DW-tabel door de grootte van alle kolommen toe te voegen:

    - Int -> 4 bytes
    - Bigint -> 8 bytes
    - Varchar(n),char(n),binair(n), varbinary(n) -> n bytes
    - Nvarchar(n), nchar(n) -> n*2 bytes
    - Datum -> 6 bytes
    - Datumtijd/(2), smalldatetijd -> 16 bytes
    - Datumtijdverschuiving -> 20 bytes
    - Decimaal -> 19 bytes
    - Float -> 8 bytes
    - Geld -> 8 bytes
    - Kleingeld -> 4 bytes
    - Real -> 4 bytes
    - Smallint -> 2 bytes
    - Tijd -> 12 bytes
    - Tinyint -> 1 byte

- **Resolutie:** de kolombreedte verkleinen tot minder dan 1 MB

- Of gebruik bulkinsert-benadering door Polybase uit te schakelen

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Foutbericht: niet voldaan aan de voorwaarde die is opgegeven met HTTP voorwaardelijke koptekst(en)

- **Symptomen:** U gebruikt SQL-query om gegevens uit Azure SQL Data Warehouse te halen en de volgende fout te raken:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Oorzaak:** Azure SQL Data Warehouse hit issue querying the external table in Azure Storage.

- **Oplossing:** Voer dezelfde query uit in SSMS en controleer of u hetzelfde resultaat ziet. Als dit het geval is, opent u een ondersteuningsticket voor Azure SQL Data Warehouse en geeft u de SQL DW-server en databasenaam op om verder te gaan met het oplossen van het probleem.
            

## <a name="delimited-text-format"></a>Afgebakende tekstnotatie

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Foutcode: DelimitedTextColumnNameNotAllowNull

- **Bericht**:`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Oorzaak**: Wanneer de eerste rij 'firstRowAsHeader' in activiteit wordt ingesteld, wordt de eerste rij gebruikt als kolomnaam. Deze fout betekent dat de eerste rij lege waarde bevat. Bijvoorbeeld: 'ColumnA,,ColumnB'.

- **Aanbeveling:** Controleer de eerste rij en stel de waarde vast als er een lege waarde is.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Foutcode: DelimitedTextMoreColumnsThanDefined

- **Bericht**:`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **Oorzaak**: Het aantal kolomtypen in de problematische rij is groter dan het aantal kolomtypen van de eerste rij. Het kan worden veroorzaakt door gegevens probleem of onjuiste kolom scheidingsteken / offerte char instellingen.

- **Aanbeveling:** Haal het aantal rijen in een foutbericht op, controleer de kolom van de rij en repareer de gegevens.

- **Oorzaak**: Als het verwachte aantal kolomen '1' in het foutbericht is, is het mogelijk dat u verkeerde compressie- of indelingsinstellingen hebt opgegeven, waardoor ADF uw bestand(en) ten onrechte heeft ontleden.

- **Aanbeveling:** Controleer de indelingsinstellingen om te controleren of deze overeenkomt met uw bronbestand(en).

- **Oorzaak:** Als uw bron een map is, is het mogelijk dat de bestanden onder de opgegeven map een ander schema hebben.

- **Aanbeveling:** Zorg ervoor dat de bestanden onder de opgegeven map een identiek schema hebben.


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>Foutcode: DelimitedTextIncorrectRowDelimiter

- **Bericht**:`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>Foutcode: DelimitedTextTooLargeColumnCount

- **Bericht**:`Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>Foutcode: DelimitedTextInvalidSettings

- **Bericht**:`%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Foutcode: DynamicsCreateServiceClientError

- **Bericht**:`This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **Oorzaak:** dit is een tijdelijk probleem aan de kant van de Dynamics-server.

- **Aanbeveling**: De pijplijn opnieuw uitvoeren. Als blijven falen, proberen om de parallellisme te verminderen. Als het nog steeds mislukt, neem dan contact op met dynamics support.



## <a name="json-format"></a>JSON-indeling

### <a name="error-code--jsoninvalidarraypathdefinition"></a>Foutcode: JsonInvalidArrayPathDefinition

- **Bericht**:`Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>Foutcode: JsonEmptyJObjectData

- **Bericht**:`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>Foutcode: JsonNullValueInPathDefinition

- **Bericht**:`Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>Foutcode: JsonUnsupportedHierarchicalComplexValue

- **Bericht**:`The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>Foutcode: JsonConflictPartitionDiscoverySchema

- **Bericht**:`Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>Foutcode: JsonInvalidDataFormat

- **Bericht**:`Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>Foutcode: JsonInvalidDataMixedArrayAndObject

- **Bericht**:`Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`



## <a name="parquet-format"></a>Parquet-indeling

### <a name="error-code--parquetjavainvocationexception"></a>Foutcode: ParketJavaInvocationException

- **Bericht**:`An error occurred when invoking java, message: %javaException;.`

- **Oorzaak**: Wanneer de foutmelding 'java.lang.OutOfMemory', 'Java heap space' en 'doubleCapacity' bevat, is het meestal een probleem met geheugenbeheer in de oude versie van integratieruntime.

- **Aanbeveling:** Als u Self-hosted Integration Runtime gebruikt en de versie eerder is dan 3.20.7159.1, stel u voor om te upgraden naar de nieuwste versie.

- **Oorzaak**: Wanneer de foutmelding 'java.lang.OutOfMemory' bevat, heeft de inburgeringsruntime niet genoeg resource om het bestand(en) te verwerken.

- **Aanbeveling**: Beperk de gelijktijdige uitvoering en de inlooptijd van de integratie. Voor Self-hosted Integration Runtime u worden opgeschaald naar een krachtige machine met geheugen gelijk aan of groter dan 8 GB.

- **Oorzaak**: Wanneer een foutbericht 'NullPointerReference' bevat, is het mogelijk een tijdelijke fout.

- **Aanbeveling**: Probeer het opnieuw. Als het probleem blijft bestaan, neem dan contact op met de ondersteuning.


### <a name="error-code--parquetinvalidfile"></a>Foutcode: ParketInvalidEFile

- **Bericht**:`File is not a valid parquet file.`

- **Oorzaak**: Parketdossier probleem.

- **Aanbeveling**: Controleer of de invoer een geldig parketbestand is.


### <a name="error-code--parquetnotsupportedtype"></a>Foutcode: ParquetNotSupportedType

- **Bericht**:`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Oorzaak:** de parketindeling wordt niet ondersteund in Azure Data Factory.

- **Aanbeveling**: Controleer de brongegevens. Raadpleeg het document: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs.


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Foutcode: ParquetMissedDecimalPrecisionScale

- **Bericht**:`Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Oorzaak**: Probeer de getalprecisie en -schaal te ontzien, maar dergelijke informatie wordt niet verstrekt.

- **Aanbeveling**: 'Bron' geeft de juiste precisie en schaal niet terug. Controleer de precisie en schaal van de uitgiftekolom.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Foutcode: ParketOngeldigDecimalPrecisionScale

- **Bericht**:`Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Oorzaak:** het schema is ongeldig.

- **Aanbeveling:** Controleer de precisie en schaal van de uitgiftekolom.


### <a name="error-code--parquetcolumnnotfound"></a>Foutcode: ParketColumnNotFound

- **Bericht**:`Column %column; does not exist in Parquet file.`

- **Oorzaak**: Bronschema is mismatch met gootsteenschema.

- **Aanbeveling**: Controleer de 'mappings' in 'activiteit'. Controleer of de bronkolom kan worden toegewezen aan de rechter gootsteenkolom.


### <a name="error-code--parquetinvaliddataformat"></a>Foutcode: ParketInvalidEDataFormat

- **Bericht**:`Incorrect format of %srcValue; for converting to %dstType;.`

- **Oorzaak:** de gegevens kunnen niet worden omgezet in type dat is opgegeven in mappings.source

- **Aanbeveling**: Controleer de brongegevens of geef het juiste gegevenstype voor deze kolom op in kolomtoewijzing voor kopieeractiviteit. Raadpleeg het document: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs.


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Foutcode: ParketDataCountNotMatchColumnCount

- **Bericht**:`The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Oorzaak**: Overeenkomen met aantal bronkolomen en sink-kolomtelling

- **Aanbeveling**: Het aantal bronkolomen is dubbel controleren als het aantal sinkkolomen in 'mapping'.


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Foutcode: ParquetDataTypeNotMatchColumnType

- **Bericht**: het gegevenstype %srcType; is niet overeenkomen met het type %dstType van kolomtype; bij kolom '%columnIndex;'.

- **Oorzaak**: Gegevens uit de bron kunnen niet worden omgezet in getypt gedefinieerd in gootsteen

- **Aanbeveling:** Geef een correct type op in mapping.sink.


### <a name="error-code--parquetbridgeinvaliddata"></a>Foutcode: ParquetBridgeInvalidData

- **Bericht**:`%message;`

- **Oorzaak**: Gegevenswaarde boven beperking

- **Aanbeveling**: Probeer het opnieuw. Als het probleem aanhoudt, neem dan contact met ons op.


### <a name="error-code--parquetunsupportedinterpretation"></a>Foutcode: ParquetUnsupportedInterpretation

- **Bericht**:`The given interpretation '%interpretation;' of parquet format is not supported.`

- **Oorzaak**: Niet ondersteund scenario

- **Aanbeveling**: 'ParquetInterpretFor' mag geen 'sparkSql' zijn.


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Foutcode: ParquetUnsupportFileLevelCompressionOption

- **Bericht**:`File level compression is not supported for Parquet.`

- **Oorzaak**: Niet ondersteund scenario

- **Aanbeveling**: Verwijder 'CompressionType' in payload.



## <a name="general-copy-activity-error"></a>Algemene kopieactiviteitsfout

### <a name="error-code--jrenotfound"></a>Foutcode: JreNotFound

- **Bericht**:`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Oorzaak:** De runtime voor zelfgehoste integratie kan Java Runtime niet vinden. De Java Runtime is vereist voor het lezen van bepaalde bron.

- **Aanbeveling:** Controleer uw integratie runtime-omgeving, het referentiedocument:https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Foutcode: WildcardPathSinkNotSupported

- **Bericht**:`Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Oorzaak:** Sink-gegevensset ondersteunt geen wildcard.

- **Aanbeveling:** Controleer de sink-gegevensset en repareer het pad zonder wildcardwaarde.


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>Foutcode: Ongeldig makenmetlegewaarde toewijzenMetlegewaarde

- **Bericht**:`One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>Foutcode: Ongeldig makenmetnameNaamPathAndOrdinal toewijzen

- **Bericht**:`Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>Foutcode: DuplicatedOrdinal toewijzen

- **Bericht**:`Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>Foutcode: Ongeldig ordinalForSinkColumn toewijzen

- **Bericht**:`Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>Volgende stappen

Probeer deze bronnen voor meer hulp bij het oplossen van problemen:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Functieaanvragen gegevensfabriek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter informatie over Data Factory](https://twitter.com/hashtag/DataFactory)
            
