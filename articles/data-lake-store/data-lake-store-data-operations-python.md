---
title: 'Python: Bestandssysteembewerkingen op Azure Data Lake Storage Gen1 | Microsoft Documenten'
description: Meer informatie over het gebruik van Python SDK om te werken met het Data Lake Storage Gen1-bestandssysteem.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 17b53c508f0c4c5ba8fa257fcdc692cdaa45d470
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76294215"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-python"></a>Bestandssysteembewerkingen op Azure Data Lake Storage Gen1 met Python
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST-API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

In dit artikel leert u hoe u Python SDK gebruiken om bestandssysteembewerkingen uit te voeren op Azure Data Lake Storage Gen1. Zie [Accountbeheerbewerkingen](data-lake-store-get-started-python.md)op Data Lake Storage Gen1 met Python voor instructies over het uitvoeren van accountbeheerbewerkingen op Data Lake Storage Gen1.

## <a name="prerequisites"></a>Vereisten

* **Python.** U kunt Python [hier](https://www.python.org/downloads/) downloaden. In dit artikel wordt Python 3.6.2 gebruikt.

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Storage Gen1-account**. Volg de instructies bij [Aan de slag met Azure Data Lake Storage Gen1 met behulp van de Azure-portal.](data-lake-store-get-started-portal.md)

## <a name="install-the-modules"></a>De modules installeren

Als u met Data Lake Storage Gen1 wilt werken met Python, moet u drie modules installeren.

* Module `azure-mgmt-resource`, die Azure-modules bevat voor Active Directory enzovoort.
* De `azure-mgmt-datalake-store` module, die de beheerbewerkingen van Azure Data Lake Storage Gen1-accountbeheer omvat. Zie voor meer informatie over deze module de referentie van de [azure-mgmt-datalake-store module.](/python/api/azure-mgmt-datalake-store/)
* De `azure-datalake-store` module, die de bestandssysteembewerkingen van Azure Data Lake Storage Gen1 bevat. Zie voor meer informatie over deze module de referentie van de [azure-datalake-store-bestandssysteemmodule](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/).

Gebruik de volgende opdrachten om de modules te installeren.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

1. Gebruik de gewenste IDE om een nieuwe Python-toepassing te maken, bijvoorbeeld **mysample.py**.

2. Voeg de volgende regels toe om de vereiste modules te importeren.

   ```
   ## Use this only for Azure AD service-to-service authentication
   from azure.common.credentials import ServicePrincipalCredentials

   ## Use this only for Azure AD end-user authentication
   from azure.common.credentials import UserPassCredentials

   ## Use this only for Azure AD multi-factor authentication
   from msrestazure.azure_active_directory import AADTokenCredentials

   ## Required for Azure Data Lake Storage Gen1 account management
   from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
   from azure.mgmt.datalake.store.models import DataLakeStoreAccount

   ## Required for Azure Data Lake Storage Gen1 filesystem management
   from azure.datalake.store import core, lib, multithread

   ## Common Azure imports
   from azure.mgmt.resource.resources import ResourceManagementClient
   from azure.mgmt.resource.resources.models import ResourceGroup

   ## Use these as needed for your application
   import logging, getpass, pprint, uuid, time
   ```

3. Sla de wijzigingen in mysample.py op.

## <a name="authentication"></a>Authentication

In deze sectie bespreken we de verschillende manieren om te verifiëren met Azure AD. De beschikbare opties zijn:

* Zie Verificatie van eindgebruikers met [Gegevensmeeropslag Gen1 met Python](data-lake-store-end-user-authenticate-python.md)voor verificatie door eindgebruikers voor de eindgebruiker.
* Zie [Service-to-service-verificatie met Data Lake Storage Gen1 met Python](data-lake-store-service-to-service-authenticate-python.md)voor service-to-service-verificatie voor uw toepassing.

## <a name="create-filesystem-client"></a>Bestandssysteemclient maken

In het volgende fragment wordt eerst de Data Lake Storage Gen1-accountclient gemaakt. Het maakt gebruik van het clientobject om een Data Lake Storage Gen1-account te maken. Ten slotte maakt het fragment een clientobject voor het bestandssysteem.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(adlCreds, store_name=adlsAccountName)

## <a name="create-a-directory"></a>Een map maken

    ## Create a directory
    adlsFileSystemClient.mkdir('/mysampledirectory')

## <a name="upload-a-file"></a>Bestand uploaden


    ## Upload a file
    multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)


## <a name="download-a-file"></a>Bestand downloaden

    ## Download a file
    multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)

## <a name="delete-a-directory"></a>Een map verwijderen

    ## Delete a directory
    adlsFileSystemClient.rm('/mysampledirectory', recursive=True)

## <a name="next-steps"></a>Volgende stappen
* [Accountbeheerbewerkingen op Data Lake Storage Gen1 met Python](data-lake-store-get-started-python.md).

## <a name="see-also"></a>Zie ook

* [Naslaginformatie over Azure Data Lake Storage Gen1 Python (Bestandssysteem)](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core)
* [Open Source Big Data-toepassingen die compatibel zijn met Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)
