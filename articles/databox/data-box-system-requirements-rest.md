---
title: Opslagvereisten voor Microsoft Azure Data Box Blob| Microsoft Documenten
description: Meer informatie over de ondersteunde versies voor API's, SDK's en clientbibliotheken voor Azure Data Box Blob-opslag
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 71e0ebf7d7851ae65a6fba67a1695d755fd98bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61436491"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Opslagvereisten voor Azure Data Box Blob

In dit artikel worden de versies van de Azure API's, Azure-clientbibliotheken en hulpprogramma's weergegeven die worden ondersteund met de Data Box Blob-opslag. Gegevensbox Blob-opslag biedt blobbeheerfunctionaliteit met Azure-consistente semantiek. In dit artikel worden ook de bekende Azure Data Box Blob-opslagverschillen van de Azure Storage-services samengevat.

We raden u aan de informatie zorgvuldig te bekijken voordat u verbinding maakt met de Data Box Blob-opslag en deze vervolgens zo nodig terugte verwijzen.


## <a name="storage-differences"></a>Opslagverschillen

|     Functie                                             |     Azure Storage                                     |     Data Box-blobopslag |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure File Storage                                   |    Ondersteunde cloudgebaseerde MKB-bestandsshares              |    Niet ondersteund      |
|    Serviceversleuteling voor gegevens in rust                  |    256-bits AES-versleuteling                             |    256-bits AES-versleuteling |
|    Type opslagaccount                                 |    Opslagaccounts voor algemene doeleinden en Azure-blobs    |    Alleen v1 voor algemeen gebruik|
|    Blobnaam                                            |    1.024 tekens (2.048 bytes)                     |    880 tekens (1.760 bytes)|
|    Blob maximale grootte blokkeren                              |    4,75 TB (100 MB X 50.000 blokken)                   |    4,75 TB (100 MB x 50.000 blokken) voor Azure Data Box v 1.8.|
|    Maximale grootte van paginablob                               |    8 TB                                               |    1 TB                   |
|    Paginablobpaginagrootte                                  |    512 bytes                                          |    4 kB                   |

## <a name="supported-api-versions"></a>Ondersteunde API-versies

De volgende versies van Azure Storage-service API's worden ondersteund met Data Box Blob-opslag:

Azure Data Box 1.8 en verder

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [17-04-2017](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05)|
## <a name="supported-azure-client-libraries"></a>Ondersteunde Azure-clientbibliotheken

Voor Gegevensbox Blob-opslag zijn er specifieke clientbibliotheken en specifieke vereisten voor eindpuntachtervoegsel. De databox Blob-opslageindpunten hebben niet volledige pariteit met de nieuwste versie van de Azure Blob Storage REST API, zie [de ondersteunde versies voor Azure Data Box 1.8 en .nl](#supported-api-versions). Voor de opslagclientbibliotheken moet u op de hoogte zijn van de versie die compatibel is met de REST API.

### <a name="azure-data-box-18-onwards"></a>Azure Data Box 1.8 en verder

| Clientbibliotheek     |Ondersteunde versie van Data Box Blob-opslag     | Koppeling   |     Eindpuntspecificatie      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    9.2.0                                           |    Nuget pakket:https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0    <br>GitHub release:https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0                                                                                                                                                                                               |    app.config-bestand                 |
|    Java                |    7.0.0                                           |    Maven pakket:https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>GitHub release:https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0                                                                                                                                                                              |    Instelling van verbindingstekenreeks         |
|    Node.js             |    2.8.3                                           |    NPM-koppeling: https://www.npmjs.com/package/azure-storage (Uitvoeren: `npm install azure-storage@2.7.0`)   <br>GitHub release:https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3                                                                                                                                                                        |    Aangifte van service-instantie    |
|    C++                 |    5.2.0                                           |    Nuget pakket:https://www.nuget.org/packages/wastorage.v140/5.2.0   <br>GitHub release:https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0                                                                                                                                                                                                     |    Instelling van verbindingstekenreeks         |
|    PHP                 |    1.2.0                                           |    GitHub release:<br>Gemeenschappelijk:https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common   <br>Blob:https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob      <br>Installeren via Componist (Zie de details hieronder voor meer informatie.)                                                                                                             |    Instelling van verbindingstekenreeks         |
|    Python              |    1.1.0                                           |    GitHub release:<br>Gemeenschappelijk:https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>Blob:https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob                                                                                                                                                                          |    Aangifte van service-instantie    |
|    Ruby                |    1.0.1                                           |    RubyGems pakket:<br>Gemeenschappelijk:https://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>Blob:https://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>GitHub release:<br>Gemeenschappelijk:https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>Blob:https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    Instelling van verbindingstekenreeks         |



### <a name="install-php-client-via-composer---current"></a>PHP-client installeren via Composer - actueel

Installeren via Composer: (neem blob als voorbeeld).
1. Maak een bestand met de naam composer.json in de hoofdmap van het project met de volgende code:

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Download `composer.phar` naar de projectroot.

3. Run: php composer.phar install.

### <a name="endpoint-declaration"></a>Eindpuntaangifte

Een eindpunt voor azure Data Box Blob-opslag bevat twee delen: de naam van een regio en het domein Data Box. In de SDK van de Gegevensbox `\<serial no. of the device>.microsoftdatabox.com`Blob-opslag is het standaardeindpunt .  Ga voor meer informatie over het eindpunt van blobservice naar [Verbinding maken via Data Box Blob-opslag](data-box-deploy-copy-data-via-rest.md).
 
## <a name="examples"></a>Voorbeelden

### <a name="net"></a>.NET

Voor De Blob-opslag van gegevensboxen wordt `app.config` het eindpuntachtervoegsel in het bestand opgegeven:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Voor De Blob-opslag van gegevensboxen wordt het eindpuntachtervoegsel opgegeven in de instelling van de verbindingstekenreeks:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Voor de opslag van De Klodder gegevens box wordt het eindpuntachtervoegsel opgegeven in de declaratie-instantie:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Voor De Blob-opslag van gegevensboxen wordt het eindpuntachtervoegsel opgegeven in de instelling van de verbindingstekenreeks:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Voor De Blob-opslag van gegevensboxen wordt het eindpuntachtervoegsel opgegeven in de instelling van de verbindingstekenreeks:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Voor de opslag van De Klodder gegevens box wordt het eindpuntachtervoegsel opgegeven in de declaratie-instantie:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Voor De Blob-opslag van gegevensboxen wordt het eindpuntachtervoegsel opgegeven in de instelling van de verbindingstekenreeks:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Volgende stappen

* [Uw Azure-gegevensvak implementeren](data-box-deploy-ordered.md)
