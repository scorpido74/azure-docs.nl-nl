---
title: Problemen met Azure Data Factory connectors oplossen
description: Meer informatie over het oplossen van connector problemen in Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/26/2019
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 218031830a7516dfd539e1c0b9b665807822f38d
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533154"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Problemen met Azure Data Factory connectors oplossen

In dit artikel worden algemene probleemoplossings methoden voor connectors in Azure Data Factory besproken.

## <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Fout bericht: de externe server heeft een fout geretourneerd: (403) verboden

- **Symptomen**: de Kopieer activiteit mislukt met de volgende fout: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Oorzaak**: een mogelijke oorzaak is dat de service-principal of beheerde identiteit die u gebruikt, geen toegang heeft tot de bepaalde map/het bestand.

- **Oplossing**: verleen de bijbehorende machtigingen voor alle mappen en submappen die u wilt kopiëren. Raadpleeg [dit document](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Fout bericht: kan geen toegangs Token ophalen met behulp van de Service-Principal. ADAL-fout: service_unavailable

- **Symptomen**: de Kopieer activiteit mislukt met de volgende fout:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Oorzaak**: wanneer de STS (Service token server) die eigendom is van Azure Active Directory niet beschikbaar is, dat wil zeggen, een HTTP-fout 503 wordt geretourneerd. 

- **Oplossing**: Voer de Kopieer activiteit na enkele minuten opnieuw uit.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Fout bericht: de omvang van de aanvraag is te groot

- **Symptomen**: u kunt gegevens naar Azure Cosmos DB kopiëren met de standaard grootte voor schrijven en de fout melding ' de grootte van de ***aanvraag is te groot**'* .

- **Oorzaak**: Cosmos DB beperkt de grootte van één aanvraag tot 2 MB. De formule is, aanvraag grootte = één document grootte * Batch grootte schrijven. Als de grootte van uw document groot is, wordt het standaard gedrag veroorzaakt door te grote aanvraag grootte. U kunt de grootte van de schrijf batch afstemmen.

- **Oplossing**: in Sink voor kopieer activiteiten kunt u de waarde ' Batch grootte schrijven ' verlagen (de standaard waarde is 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Fout bericht: schending van een unieke index beperking

- **Symptomen**: wanneer u gegevens naar Cosmos DB kopieert, raakt u de volgende fout melding:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Oorzaak**: er zijn twee mogelijke oorzaken:

    - Als u **Invoegen** als schrijf gedrag gebruikt, betekent dit dat de bron gegevens rijen/objecten met dezelfde id hebben.

    - Als u **Upsert** als schrijf gedrag gebruikt en u een andere unieke sleutel instelt op de container, betekent dit dat de bron gegevens rijen/objecten hebben met verschillende id's, maar dezelfde waarde hebben voor de gedefinieerde unieke sleutel.

- **Oplossing**: 

    - Stel voor Cause1 **Upsert** in als schrijf gedrag.
    - Zorg ervoor dat elk document voor oorzaak 2 een andere waarde heeft voor de gedefinieerde unieke sleutel.

### <a name="error-message-request-rate-is-large"></a>Fout bericht: de aanvraag frequentie is groot

- **Symptomen**: wanneer u gegevens naar Cosmos DB kopieert, raakt u de volgende fout melding:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Oorzaak**: de gebruikte aanvraag eenheden is groter dan de beschik bare ru die is geconfigureerd in Cosmos db. Meer informatie over hoe Cosmos DB RU van [hieruit](../cosmos-db/request-units.md#request-unit-considerations)berekent.

- **Oplossing**: Hier volgen twee oplossingen:

    1. **Verhoog de container ru** naar een grotere waarde in Cosmos DB, waardoor de prestaties van de Kopieer activiteit worden verbeterd, hoewel er meer kosten in Cosmos DB ontstaan. 

    2. Verklein **writeBatchSize** naar een kleinere waarde (zoals 1000) en stel **parallelCopies** in op een kleinere waarde, zoals 1, waardoor de prestaties van het kopiëren worden verergerd dan de huidige, maar er worden geen kosten in rekening gebracht in Cosmos db.

### <a name="column-missing-in-column-mapping"></a>Kolom ontbreekt in kolom toewijzing

- **Symptomen**: bij het importeren van het schema voor Cosmos DB voor kolom toewijzing, ontbreken sommige kolommen. 

- **Oorzaak**: ADF leidt het schema af van de eerste tien Cosmos DB documenten. Als sommige kolommen/eigenschappen geen waarde hebben in deze documenten, wordt deze niet gedetecteerd met ADF. dit wordt niet weer gegeven.

- **Oplossing**: u kunt de query zoals hieronder afstemmen om kolom af te dwingen om weer te geven in de resultatenset met een lege waarde: (Stel dat er geen kolom ' onmogelijk ' is in de eerste tien documenten). U kunt ook de kolom voor toewijzing hand matig toevoegen.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Fout bericht: de GuidRepresentation voor de lezer is CSharpLegacy

- **Symptomen**: bij het kopiëren van gegevens uit Cosmos DB MongoAPI/MongoDb met het veld uuid, raakt u de volgende fout melding:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Oorzaak**: er zijn twee manieren om uuid in BSON-UuidStardard en UuidLegacy aan te duiden. UuidLegacy wordt standaard gebruikt om gegevens te lezen. U krijgt een fout als uw UUID-gegevens in MongoDB UuidStandard zijn.

- **Oplossing**: Voeg In MongoDb Connection String de optie "**uuidRepresentation = Standard**" toe. Zie [MongoDB Connection String](connector-mongodb.md#linked-service-properties)voor meer informatie.

## <a name="sftp"></a>SFTP

### <a name="error-message-invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>Fout bericht: er is een ongeldige SFTP-referentie gegeven voor het verificatie type SshPublicKey

- **Symptomen**: gebruik `SshPublicKey`-verificatie en klik op de volgende fout:

    ```
    Invalid Sftp credential provided for 'SshPublicKey' authentication type
    ```

- **Oorzaak**: er zijn drie mogelijke oorzaken:

    1. Als u de gebruikers interface van ADF voor het maken van een gekoppelde SFTP-service gebruikt, betekent dit dat de persoonlijke sleutel die u wilt gebruiken, een onjuiste indeling heeft. U kunt een PKCS # 8-indeling van de persoonlijke SSH-sleutel gebruiken, terwijl de ADF alleen de traditionele SSH-sleutel indeling ondersteunt. Meer in het bijzonder, het verschil tussen PKCS # 8-indeling en traditionele sleutel indeling is PKCS # 8-sleutel inhoud begint met ' *-----beginnen met VERsleutelde persoonlijke sleutel-----* ', terwijl de traditionele sleutel indeling begint met ' *-----Start RSA-sleutel-----* '.
    2. Als u Azure Key Vault gebruikt om de inhoud van de persoonlijke sleutel op te slaan of een programmatische manier te gebruiken voor het maken van de gekoppelde SFTP-service, betekent deze fout dat de inhoud van de persoonlijke sleutel onjuist is, waarschijnlijk niet met base64 gecodeerd.
    3. Ongeldige inhoud voor referentie of persoonlijke sleutel.

- **Oplossing**: 

    - Voer voor oorzaak #1 de volgende opdrachten uit om de sleutel te converteren naar een traditionele sleutel notatie en vervolgens te gebruiken in de gebruikers interface van ADF.

        ```
        # Decrypt the pkcs8 key and convert the format to traditional key format
        openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file

        chmod 600 traditional_format_key_file

        # Re-encrypte the key file using passphrase
        ssh-keygen -f traditional_format_key_file -p
        ```

    - Voor een oorzaak #2 kan een dergelijke teken reeks worden gegenereerd door de klant onder de volgende twee manieren te gebruiken:
    - Met het hulp programma base64 Convert van derden: plak de volledige inhoud van de persoonlijke sleutel naar hulpprogram ma's zoals [Base64 coderen en decoderen](https://www.base64encode.org/), Codeer deze in een base64-indelings teken reeks, plak deze teken reeks in een sleutel kluis of gebruik deze waarde voor het programmatisch maken van een gekoppelde SFTP-service.
    - Code C# gebruiken:

        ```c#
        byte[] keyContentBytes = File.ReadAllBytes(privateKeyPath);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Controleer bij oorzaak #3 controleren of het sleutel bestand of het wacht woord juist is met behulp van andere hulpprogram ma's om te controleren of u dit kunt gebruiken om toegang te krijgen tot de SFTP-server.
  

## <a name="azure-sql-data-warehouse--azure-sql-database--sql-server"></a>Azure SQL Data Warehouse \ Azure SQL Database \ SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Fout code: SqlFailedToConnect

- **Bericht**: `Cannot connect to SQL database: '%server;', Database: '%database;', User: '%user;'. Please check the linked service configuration is correct, and make sure the SQL database firewall allows the integration runtime to access.`

- **Oorzaak**: als het fout bericht ' SQLException ' bevat, genereert SQL database de fout die aangeeft dat een bepaalde bewerking is mislukt.

- **Aanbeveling**: Zoek op SQL-fout code in dit referentie document voor meer informatie: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Als u meer hulp nodig hebt, neemt u contact op met de ondersteuning van Azure SQL.

- **Oorzaak**: als het fout bericht ' client met IP-adres '... ' bevat heeft geen toegang tot de server, en u probeert verbinding te maken met Azure SQL database. dit wordt meestal veroorzaakt door een probleem met de Azure SQL database-firewall.

- **Aanbeveling**: Schakel in configuratie van Azure SQL Server firewall de optie ' Azure-Services en-bronnen toestaan voor toegang tot deze server ' in. Verwijzings document: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure.


### <a name="error-code--sqloperationfailed"></a>Fout code: SqlOperationFailed

- **Bericht**: `A database operation failed. Please search error to get more details.`

- **Oorzaak**: als het fout bericht ' SQLException ' bevat, genereert SQL database de fout die aangeeft dat een bepaalde bewerking is mislukt.

- **Aanbeveling**: Zoek op SQL-fout code in dit referentie document voor meer informatie: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Als u meer hulp nodig hebt, neemt u contact op met de ondersteuning van Azure SQL.

- **Oorzaak**: als het fout bericht ' PdwManagedToNativeInteropException ' bevat, wordt dit meestal veroorzaakt door een niet-overeenkomende waarde voor de grootte van de bron-en Sink-kolom.

- **Aanbeveling**: Controleer de grootte van de kolommen bron en Sink. Als u meer hulp nodig hebt, neemt u contact op met de ondersteuning van Azure SQL.

- **Oorzaak**: als het fout bericht ' InvalidOperationException ' bevat, wordt dit meestal veroorzaakt door ongeldige invoer gegevens.

- **Aanbeveling**: als u wilt identificeren in welke rij het probleem zich voordoet, schakelt u de functie fout tolerantie in op Kopieer activiteit, waarmee u problematische rij (en) kunt omleiden naar een opslag voor verdere onderzoek. Verwijzings document: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlunauthorizedaccess"></a>Fout code: SqlUnauthorizedAccess

- **Bericht**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Oorzaak**: de referentie is onjuist of het aanmeldings account heeft geen toegang tot SQL database.

- **Aanbeveling**: Controleer of het aanmeldings account voldoende machtigingen heeft om toegang te krijgen tot de SQL database.


### <a name="error-code--sqlopenconnectiontimeout"></a>Fout code: SqlOpenConnectionTimeout

- **Bericht**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Oorzaak**: kan SQL database tijdelijke fout zijn.

- **Aanbeveling**: Probeer het opnieuw om de gekoppelde Service Connection String bij te werken met een grotere time-outwaarde voor de verbinding.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Fout code: SqlAutoCreateTableTypeMapFailed

- **Bericht**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(colunm name:'%colunmName;') in auto-create table.`

- **Oorzaak**: het automatisch maken van de tabel kan niet voldoen aan de bron vereiste.

- **Aanbeveling**: werk het kolom Type bij in toewijzingen of maak de Sink-tabel hand matig op doel server.


### <a name="error-code--sqldatatypenotsupported"></a>Fout code: SqlDataTypeNotSupported

- **Bericht**: `A database operation failed. Please check the SQL errors.`

- **Oorzaak**: als het probleem zich voordoet op de SQL-bron en de fout is gerelateerd aan SqlDateTime overflow, ligt de gegevens waarde boven het logische type bereik (1/1/1753 12:00:00 uur 12/31/9999 11:59:59 pm).

- **Aanbeveling**: Converteer het type naar een teken reeks in de bron-SQL-query of wijzig het kolom Type in de kolom Copy-activiteit in een teken reeks.

- **Oorzaak**: als het probleem zich voordoet op SQL-Sink en de fout is gerelateerd aan SqlDateTime overflow, ligt de gegevens waarde boven het toegestane bereik in de Sink-tabel.

- **Aanbeveling**: werk het bijbehorende kolom Type bij naar het type ' DATETIME2 ' in de Sink-tabel.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Fout code: SqlInvalidDbStoredProcedure

- **Bericht**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Oorzaak**: de opgegeven opgeslagen procedure is ongeldig. Dit kan worden veroorzaakt doordat de opgeslagen procedure geen gegevens retourneert.

- **Aanbeveling**: Valideer de opgeslagen procedure door SQL-hulpprogram ma's. Zorg ervoor dat de opgeslagen procedure gegevens kan retour neren.


### <a name="error-code--sqlinvaliddbquerystring"></a>Fout code: SqlInvalidDbQueryString

- **Bericht**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Oorzaak**: de opgegeven SQL-query is ongeldig. Dit kan worden veroorzaakt doordat de query geen gegevens retourneert

- **Aanbeveling**: Valideer de SQL-query door SQL-hulpprogram ma's. Zorg ervoor dat de query gegevens kan retour neren.


### <a name="error-code--sqlinvalidcolumnname"></a>Fout code: SqlInvalidColumnName

- **Bericht**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Oorzaak**: de kolom is niet gevonden. Mogelijke configuratie is onjuist.

- **Aanbeveling**: Controleer de kolom in de query ' Structure ' in dataset en ' mappings ' in de activiteit.


### <a name="error-code--sqlbatchwritetimeout"></a>Fout code: SqlBatchWriteTimeout

- **Bericht**: `Timeout in SQL write opertaion.`

- **Oorzaak**: kan SQL database tijdelijke fout zijn.

- **Aanbeveling**: als probleem reproduceren, neemt u contact op met de Azure SQL-ondersteuning.


### <a name="error-code--sqlbatchwriterollbackfailed"></a>Fout code: SqlBatchWriteRollbackFailed

- **Bericht**: `Timeout in SQL write operation and rollback also fail.`

- **Oorzaak**: kan SQL database tijdelijke fout zijn.

- **Aanbeveling**: Probeer het opnieuw om de gekoppelde Service Connection String bij te werken met een grotere time-outwaarde voor de verbinding.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Fout code: SqlBulkCopyInvalidColumnLength

- **Bericht**: `SQL Bulk Copy failed due to received an invalid column length from the bcp client.`

- **Oorzaak**: het bulksgewijs kopiëren van SQL is mislukt omdat er een ongeldige kolom lengte is ontvangen van de BCP-client.

- **Aanbeveling**: als u wilt identificeren in welke rij het probleem zich voordoet, schakelt u de functie fout tolerantie in op Kopieer activiteit, waarmee u problematische rij (en) kunt omleiden naar een opslag voor verdere onderzoek. Verwijzings document: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlconnectionisclosed"></a>Fout code: SqlConnectionIsClosed

- **Bericht**: `The connection is closed by SQL database.`

- **Oorzaak**: SQL-verbinding wordt gesloten door SQL database als hoge gelijktijdige uitvoering en server verbinding beëindigen.

- **Aanbeveling**: externe server sluit de SQL-verbinding. Probeer het opnieuw. Neem contact op met de ondersteuning van Azure SQL als probleem reproduceren.

### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Fout bericht: de conversie is mislukt tijdens het converteren van een teken reeks naar een unieke id

- **Symptomen**: bij het kopiëren van gegevens uit een gegevens bron in tabel vorm (zoals SQL Server) naar Azure SQL data warehouse met behulp van gefaseerde kopie en poly Base, wordt de volgende fout weer gegeven:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Oorzaak**: Azure SQL Data Warehouse poly Base kan een lege teken reeks niet converteren naar GUID.

- **Oplossing**: Stel in Sink voor kopieer activiteiten onder poly base-instellingen de optie**type standaard gebruiken**in op ONWAAR.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Fout bericht: verwacht gegevens type: decimaal (x, x), foutieve waarde

- **Symptomen**: wanneer u gegevens uit tabellaire gegevens bron (zoals SQL Server) naar SQL DW kopieert met behulp van gefaseerde kopie en poly Base, bereikt u de volgende fout:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Oorzaak**: Azure SQL Data Warehouse poly Base kan geen lege teken reeks (null-waarde) invoegen in een decimale kolom.

- **Oplossing**: Stel in Sink voor kopieer activiteiten onder poly base-instellingen de optie**type standaard gebruiken**in op ONWAAR.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>Fout bericht: Java-uitzonderings bericht: HdfsBridge:: CreateRecordReader

- **Symptomen**: u kopieert gegevens naar Azure SQL data warehouse met poly base en de volgende fout wordt weer gegeven:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Oorzaak**: de mogelijke oorzaak is dat het schema (totale kolom breedte) te groot is (groter dan 1 MB). Controleer het schema van de SQL DW-doel tabel door de grootte van alle kolommen toe te voegen:

    - Int-> 4 bytes
    - Bigint-> 8 bytes
    - Varchar (n), char (n), binary (n), varbinary (n)-> n bytes
    - Nvarchar (n), nchar (n)-> n * 2 bytes
    - Datum-> 6 bytes
    - Datetime/(2), smalldatetime-> 16 bytes
    - Date time Offset-> 20 bytes
    - Decimaal-> 19 bytes
    - Zwevende > 8 bytes
    - Money-> 8 bytes
    - Smallmoney-> 4 bytes
    - Real-> 4 bytes
    - Smallint-> 2 bytes
    - Tijd-> 12 bytes
    - Tinyint-> 1 byte

- **Oplossing**: Verklein de kolom breedte kleiner dan 1 MB

- Of gebruik bulksgewijze invoeg aanpak door poly Base uit te scha kelen

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Fout bericht: er is niet voldaan aan de voor waarde die is opgegeven met een HTTP-header (n)

- **Symptomen**: u gebruikt SQL-query om gegevens uit Azure SQL Data Warehouse op te halen en de volgende fout te vinden:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Oorzaak**: Azure SQL Data Warehouse treffer probleem bij het uitvoeren van een query op de externe tabel in azure Storage.

- **Oplossing**: Voer dezelfde query uit in SSMS en controleer of hetzelfde resultaat wordt weer geven. Als dit het geval is, opent u een ondersteuningsticket voor Azure SQL Data Warehouse en geeft u de SQL DW-server en databasenaam op om verder te gaan met het oplossen van het probleem.
            

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code--azurebloboperationfailed"></a>Fout code: AzureBlobOperationFailed

- **Bericht**: `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Oorzaak**: probleem met het aanraken van Blob-opslag.

- **Aanbeveling**: Raadpleeg de fout voor meer informatie. Ga naar het Help-document van blob: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes. Neem contact op met het opslag team als u hulp nodig hebt.



## <a name="azure-data-lake-gen2"></a>Azure Data Lake Gen2

### <a name="error-code--adlsgen2operationfailed"></a>Fout code: AdlsGen2OperationFailed

- **Bericht**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Oorzaak**: ADLS Gen2 genereert de fout die aangeeft dat de bewerking is mislukt.

- **Aanbeveling**: Raadpleeg het gedetailleerde fout bericht dat wordt gegenereerd door ADLS Gen2. Als dit wordt veroorzaakt door een tijdelijke fout, probeer het dan opnieuw. Als u meer hulp nodig hebt, neemt u contact op met de ondersteuning van Azure Storage en geeft u de aanvraag-ID op in een fout bericht.

- **Oorzaak**: wanneer het fout bericht ' verboden ' bevat, is voor de service-principal of beheerde identiteit die u gebruikt mogelijk niet voldoende machtigingen voor toegang tot de ADLS Gen2.

- **Aanbeveling**: Raadpleeg het Help-document: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication.

- **Oorzaak**: wanneer het fout bericht ' InternalServerError ' bevat, wordt de fout geretourneerd door ADLS Gen2.

- **Aanbeveling**: dit wordt mogelijk veroorzaakt door een tijdelijke fout, probeer het opnieuw. Als het probleem zich blijft voordoen, neemt u contact op met Azure Storage ondersteuning en geeft u de aanvraag-ID op in het fout bericht.


## <a name="next-steps"></a>Volgende stappen

Probeer deze bronnen voor meer informatie over probleem oplossing:

*  [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-Video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)
            
