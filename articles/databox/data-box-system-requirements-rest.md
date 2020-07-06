---
title: Vereisten voor de opslag van Microsoft Azure Data Box BLOB | Microsoft Docs
description: Meer informatie over de ondersteunde versies voor Api's, Sdk's en client bibliotheken voor Azure Data Box Blob-opslag
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 71e0ebf7d7851ae65a6fba67a1695d755fd98bb1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "61436491"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Vereisten voor Blob Storage Azure Data Box

In dit artikel vindt u een overzicht van de versies van de Azure-Api's, Azure-client bibliotheken en hulpprogram ma's die worden ondersteund met de Data Box Blob-opslag. Data Box Blob-opslag biedt functionaliteit voor het beheer van blobs met Azure-consistente semantiek. In dit artikel wordt ook een overzicht gegeven van de bekende Azure Data Box verschillen in Blob Storage van de Azure Storage services.

We raden u aan de informatie zorgvuldig te controleren voordat u verbinding maakt met de Data Box Blob-opslag en vervolgens naar de gewenste gegevens te verwijzen.


## <a name="storage-differences"></a>Verschillen in opslag

|     Functie                                             |     Azure Storage                                     |     Data Box-blobopslag |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure File Storage                                   |    Door de cloud gebaseerde SMB-bestands shares worden ondersteund              |    Niet ondersteund      |
|    Service versleuteling voor Data-at-rest                  |    256-bits AES-versleuteling                             |    256-bits AES-versleuteling |
|    Type opslagaccount                                 |    Algemene en Azure Blob Storage-accounts    |    Alleen voor algemeen gebruik v1|
|    Blobnaam                                            |    1.024 tekens (2.048 bytes)                     |    880 tekens (1.760 bytes)|
|    Maximale grootte van blok-BLOB                              |    4,75 TB (100 MB X 50.000 blokken)                   |    4,75 TB (100 MB x 50.000 blokken) voor Azure Data Box v 1,8.|
|    Maximum grootte voor pagina-blobs                               |    8 TB                                               |    1 TB                   |
|    Pagina formaat pagina-BLOB                                  |    512 bytes                                          |    4 kB                   |

## <a name="supported-api-versions"></a>Ondersteunde API-versies

De volgende versies van Azure Storage service-Api's worden ondersteund met Data Box Blob-opslag:

Azure Data Box 1,8 en hoger

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [17-04-2017](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05)|
## <a name="supported-azure-client-libraries"></a>Ondersteunde Azure-client bibliotheken

Voor Data Box Blob Storage zijn er specifieke client bibliotheken en specifieke vereisten voor eindpunt achtervoegsels. De Data Box Blob Storage-eind punten hebben geen volledige pariteit met de nieuwste versie van de Azure Blob Storage REST API. Zie de [ondersteunde versies voor Azure Data Box 1,8 en hoger](#supported-api-versions). U moet rekening houden met de versie die compatibel is met de REST API voor de opslag-client bibliotheken.

### <a name="azure-data-box-18-onwards"></a>Azure Data Box 1,8 en hoger

| Clientbibliotheek     |Data Box versie van Blob Storage die wordt ondersteund     | Koppeling   |     Eindpunt specificatie      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    9.2.0                                           |    Nuget-pakket:https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0    <br>GitHub release:https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0                                                                                                                                                                                               |    app.config-bestand                 |
|    Java                |    7.0.0                                           |    Maven-pakket:https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>GitHub release:https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0                                                                                                                                                                              |    Verbindingsteken reeks instellen         |
|    Node.js             |    2.8.3                                           |    NPM-koppeling: https://www.npmjs.com/package/azure-storage (uitvoeren: `npm install azure-storage@2.7.0` )   <br>GitHub release:https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3                                                                                                                                                                        |    Declaratie van service-exemplaar    |
|    C++                 |    5.2.0                                           |    Nuget-pakket:https://www.nuget.org/packages/wastorage.v140/5.2.0   <br>GitHub release:https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0                                                                                                                                                                                                     |    Verbindingsteken reeks instellen         |
|    PHP                 |    1.2.0                                           |    GitHub release:<br>Algemenehttps://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common   <br>Blobcachehttps://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob      <br>Installeren via Composer (Zie de details hieronder voor meer informatie).                                                                                                             |    Verbindingsteken reeks instellen         |
|    Python              |    1.1.0                                           |    GitHub release:<br>Algemenehttps://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>Blobcachehttps://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob                                                                                                                                                                          |    Declaratie van service-exemplaar    |
|    Ruby                |    1.0.1                                           |    RubyGems-pakket:<br>Algemenehttps://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>Blobcachehttps://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>GitHub release:<br>Algemenehttps://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>Blobcachehttps://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    Verbindingsteken reeks instellen         |



### <a name="install-php-client-via-composer---current"></a>PHP-client installeren via Composer-actueel

Installeren via Composer: (Maak een BLOB als voor beeld).
1. Maak een bestand met de naam composer.jsin de hoofdmap van het project met de volgende code:

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Down load `composer.phar` de hoofdmap van het project.

3. Uitvoeren: PHP Composer. Phar-installatie.

### <a name="endpoint-declaration"></a>Eindpunt declaratie

Een Azure Data Box Blob Storage-eind punt bestaat uit twee delen: de naam van een regio en het Data Box domein. In de Data Box Blob Storage SDK is het standaard eindpunt `\<serial no. of the device>.microsoftdatabox.com` .  Ga voor meer informatie over het eind punt van de BLOB-service naar [verbinding maken via data Box Blob-opslag](data-box-deploy-copy-data-via-rest.md).
 
## <a name="examples"></a>Voorbeelden

### <a name="net"></a>.NET

Voor Data Box Blob-opslag is het achtervoegsel van het eind punt opgegeven in het `app.config` bestand:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Voor Data Box Blob-opslag is het achtervoegsel van het eind punt opgegeven in de installatie van connection string:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Voor Data Box Blob-opslag is het eind punt achtervoegsel opgegeven in het declaratie-exemplaar:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Voor Data Box Blob-opslag is het achtervoegsel van het eind punt opgegeven in de installatie van connection string:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Voor Data Box Blob-opslag is het achtervoegsel van het eind punt opgegeven in de installatie van connection string:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Voor Data Box Blob-opslag is het eind punt achtervoegsel opgegeven in het declaratie-exemplaar:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Voor Data Box Blob-opslag is het achtervoegsel van het eind punt opgegeven in de installatie van connection string:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Volgende stappen

* [Uw Azure Data Box implementeren](data-box-deploy-ordered.md)
