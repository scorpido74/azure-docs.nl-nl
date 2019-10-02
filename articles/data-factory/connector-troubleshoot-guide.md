---
title: Problemen met Azure Data Factory connectors oplossen | Microsoft Docs
description: Meer informatie over het oplossen van connector problemen in Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/26/2019
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: bea5191063cf673f6b1395d46a15536e80b0aa30
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143503"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Problemen met Azure Data Factory connectors oplossen

In dit artikel worden algemene probleemoplossings methoden voor connectors in Azure Data Factory besproken.

## <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Foutbericht: De externe server heeft een fout geretourneerd: (403) verboden

- **Symptomen**: De Kopieer activiteit mislukt met de volgende fout: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Oorzaak**: Een mogelijke oorzaak is dat de service-principal of beheerde identiteit die u gebruikt, geen toegang heeft tot de bepaalde map of het bestand.

- **Resolutie**: Verleen de bijbehorende machtigingen voor alle mappen en submappen die u wilt kopiëren. Raadpleeg [dit document](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Foutbericht: Kan geen toegangs Token ophalen met behulp van de Service-Principal. ADAL-fout: service_unavailable

- **Symptomen**: de Kopieer activiteit mislukt met de volgende fout:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Oorzaak**: Wanneer de STS (Service token server) die eigendom is van Azure Active Directory niet beschikbaar is, dat wil zeggen, te druk om aanvragen te verwerken, wordt een HTTP-fout 503 geretourneerd. 

- **Resolutie**: Voer de Kopieer activiteit na enkele minuten opnieuw uit.

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Foutbericht: De conversie is mislukt tijdens het converteren van een teken reeks naar een unieke id

- **Symptomen**: Wanneer u gegevens uit de gegevens bron in tabel vorm (zoals SQL Server) kopieert naar Azure SQL Data Warehouse met behulp van gefaseerde kopie en poly Base, wordt de volgende fout weer gegeven:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Oorzaak**: Azure SQL Data Warehouse poly Base kan geen lege teken reeks converteren naar GUID.

- **Resolutie**: Stel in Sink voor kopieer activiteit onder poly base-instellingen de optie**type standaard gebruiken**in op ONWAAR.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Foutbericht: Verwacht gegevens type: DECIMAAL (x, x), foutieve waarde

- **Symptomen**: Wanneer u gegevens kopieert vanuit tabellaire gegevens bron (zoals SQL Server) naar SQL DW met behulp van gefaseerde kopie en poly Base, wordt de volgende fout weer gegeven:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Oorzaak**: Azure SQL Data Warehouse poly Base kan geen lege teken reeks (null-waarde) invoegen in een decimale kolom.

- **Resolutie**: Stel in Sink voor kopieer activiteit onder poly base-instellingen de optie**type standaard gebruiken**in op ONWAAR.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>Foutbericht: Java-uitzonderings bericht: HdfsBridge:: CreateRecordReader

- **Symptomen**: U kunt gegevens met poly Base naar Azure SQL Data Warehouse kopiëren en de volgende fout raken:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Oorzaak**: De mogelijke oorzaak is dat het schema (totale kolom breedte) te groot is (groter dan 1 MB). Controleer het schema van de SQL DW-doel tabel door de grootte van alle kolommen toe te voegen:

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

- **Resolutie**: Kolom breedte verkleinen tot minder dan 1 MB

- Of gebruik bulksgewijze invoeg aanpak door poly Base uit te scha kelen

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Foutbericht: Er is niet voldaan aan de voor waarde die is opgegeven met de HTTP-koptekst (en).

- **Symptomen**: U gebruikt SQL-query om gegevens uit Azure SQL Data Warehouse te halen en de volgende fout te vinden:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Oorzaak**: Azure SQL Data Warehouse treffer probleem bij het uitvoeren van een query op de externe tabel in Azure Storage.

- **Resolutie**: Voer dezelfde query uit in SSMS en controleer of hetzelfde resultaat wordt weer geven. Als dit het geval is, opent u een ondersteunings ticket om Azure SQL Data Warehouse en geeft u uw SQL DW-server en-database naam op om verder te kunnen oplossen.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Foutbericht: De aanvraag grootte is te groot

- **Symptomen**: U kunt gegevens naar Azure Cosmos DB kopiëren met de standaard schrijf Batch grootte en de fout melding de grootte van de *'**aanvraag is te groot**'* .

- **Oorzaak**: Cosmos DB beperkt de grootte van één aanvraag tot 2 MB. De formule is, aanvraag grootte = één document grootte * Batch grootte schrijven. Als de grootte van uw document groot is, wordt het standaard gedrag veroorzaakt door te grote aanvraag grootte. U kunt de grootte van de schrijf batch afstemmen.

- **Resolutie**: In Sink voor kopieer activiteiten vermindert u de waarde ' Batch grootte schrijven ' (de standaard waarde is 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Foutbericht: Schending van de unieke index beperking

- **Symptomen**: Wanneer u gegevens naar Cosmos DB kopieert, raakt u de volgende fout melding:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception &#61; Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Oorzaak**: Er zijn twee mogelijke oorzaken:

    - Als u **Invoegen** als schrijf gedrag gebruikt, betekent dit dat de bron gegevens rijen/objecten met dezelfde id hebben.

    - Als u **Upsert** als schrijf gedrag gebruikt en u een andere unieke sleutel instelt op de container, betekent dit dat de bron gegevens rijen/objecten hebben met verschillende id's, maar dezelfde waarde hebben voor de gedefinieerde unieke sleutel.

- **Resolutie**: 

    - Stel voor Cause1 **Upsert** in als schrijf gedrag.
    - Zorg ervoor dat elk document voor oorzaak 2 een andere waarde heeft voor de gedefinieerde unieke sleutel.

### <a name="error-message-request-rate-is-large"></a>Foutbericht: Aanvraagsnelheid is hoog

- **Symptomen**: Wanneer u gegevens naar Cosmos DB kopieert, raakt u de volgende fout melding:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Oorzaak**: De gebruikte aanvraag eenheden zijn groter dan de beschik bare RU die is geconfigureerd in Cosmos DB. Meer informatie over hoe Cosmos DB RU van [hieruit](../cosmos-db/request-units.md#request-unit-considerations)berekent.

- **Resolutie**: Hier volgen twee oplossingen:

    1. **Verhoog de container ru** naar een grotere waarde in Cosmos DB, waardoor de prestaties van de Kopieer activiteit worden verbeterd, hoewel er meer kosten in Cosmos DB ontstaan. 

    2. Verklein **writeBatchSize** naar een kleinere waarde (zoals 1000) en stel **parallelCopies** in op een kleinere waarde, zoals 1, waardoor de prestaties van het kopiëren worden verergerd dan de huidige, maar er worden geen kosten in rekening gebracht in Cosmos db.

### <a name="column-missing-in-column-mapping"></a>Kolom ontbreekt in kolom toewijzing

- **Symptomen**: Bij het importeren van het schema voor Cosmos DB voor kolom toewijzing, ontbreken sommige kolommen. 

- **Oorzaak**: Bij ADF wordt het schema van de eerste tien Cosmos DB documenten afafgeleid. Als sommige kolommen/eigenschappen geen waarde hebben in deze documenten, wordt deze niet gedetecteerd met ADF. dit wordt niet weer gegeven.

- **Resolutie**: U kunt de query op de onderstaande manier afstemmen om kolom af te dwingen om weer te geven in de resultatenset met een lege waarde: (ervan uitgaand dat de kolom ' onmogelijk ' ontbreekt in de eerste tien documenten). U kunt ook de kolom voor toewijzing hand matig toevoegen.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Foutbericht: De GuidRepresentation voor de lezer is CSharpLegacy

- **Symptomen**: Bij het kopiëren van gegevens uit Cosmos DB MongoAPI/MongoDB met het veld UUID, raakt u de volgende fout melding:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Oorzaak**: Er zijn twee manieren om UUID te vertegenwoordigen in BSON-UuidStardard en UuidLegacy. UuidLegacy wordt standaard gebruikt om gegevens te lezen. U krijgt een fout als uw UUID-gegevens in MongoDB UuidStandard zijn.

- **Resolutie**: Voeg in MongoDB connection string de optie '**uuidRepresentation = Standard**' toe. Zie [MongoDB Connection String](connector-mongodb.md#linked-service-properties)voor meer informatie.

## <a name="sftp"></a>SFTP

### <a name="error-message-invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>Foutbericht: Er is een ongeldige SFTP-referentie gegeven voor het verificatie type SshPublicKey

- **Symptomen**: U gebruikt `SshPublicKey` verificatie en u kunt de volgende fout melding aanraken:

    ```
    Invalid Sftp credential provided for 'SshPublicKey' authentication type
    ```

- **Oorzaak**: Er zijn drie mogelijke oorzaken:

    1. Als u de gebruikers interface van ADF voor het maken van een gekoppelde SFTP-service gebruikt, betekent dit dat de persoonlijke sleutel die u wilt gebruiken, een onjuiste indeling heeft. U kunt een PKCS # 8-indeling van de persoonlijke SSH-sleutel gebruiken, terwijl de ADF alleen de traditionele SSH-sleutel indeling ondersteunt. Meer in het bijzonder, het verschil tussen PKCS # 8-indeling en traditionele sleutel indeling is PKCS # 8-sleutel inhoud begint met ' *-----beginnen met VERsleutelde persoonlijke sleutel-----* ', terwijl de traditionele sleutel indeling begint met ' *-----Start RSA-sleutel-----* '.
    2. Als u Azure Key Vault gebruikt om de inhoud van de persoonlijke sleutel op te slaan of een programmatische manier te gebruiken voor het maken van de gekoppelde SFTP-service, betekent deze fout dat de inhoud van de persoonlijke sleutel onjuist is, waarschijnlijk niet met base64 gecodeerd.
    3. Ongeldige inhoud voor referentie of persoonlijke sleutel.

- **Resolutie**: 

    - Voer voor oorzaak #1 de volgende opdrachten uit om de sleutel te converteren naar een traditionele sleutel notatie en vervolgens te gebruiken in de gebruikers interface van ADF.

        ```
        # Decrypt the pkcs8 key and convert the format to traditional key format
        openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file

        chmod 600 traditional_format_key_file

        # Re-encrypte the key file using passphrase
        ssh-keygen -f traditional_format_key_file -p
        ```

    - Voor een oorzaak #2 kan een dergelijke teken reeks worden gegenereerd door de klant onder de volgende twee manieren te gebruiken:
    - Met het hulp programma base64 Convert van derden: plak de volledige inhoud van de persoonlijke sleutel naar hulpprogram ma's zoals [Base64 coderen en](https://www.base64encode.org/)decoderen, Codeer deze in een base64-indelings teken reeks, plak deze teken reeks in een sleutel kluis of gebruik deze waarde voor het programmatisch maken van een gekoppelde SFTP-service.
    - Code C# gebruiken:

        ```c#
        byte[] keyContentBytes = File.ReadAllBytes(privateKeyPath);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Controleer bij oorzaak #3 controleren of het sleutel bestand of het wacht woord juist is met behulp van andere hulpprogram ma's om te controleren of u dit kunt gebruiken om toegang te krijgen tot de SFTP-server.

## <a name="next-steps"></a>Volgende stappen

Probeer deze bronnen voor meer informatie over probleem oplossing:

*  [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-Video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)



