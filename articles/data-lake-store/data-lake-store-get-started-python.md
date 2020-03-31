---
title: 'Python: Accountbeheerbewerkingen op Azure Data Lake Storage Gen1 | Microsoft Documenten'
description: Meer informatie over het gebruik van Python SDK om te werken met Azure Data Lake Storage Gen1-accountbeheerbewerkingen.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c71136ecb57fac460514b5f4815ba19cc22d86cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76290611"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>Accountbeheerbewerkingen op Azure Data Lake Storage Gen1 met Python
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST-API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Meer informatie over het gebruik van de Python SDK voor Azure Data Lake Storage Gen1 om basisaccountbeheerbewerkingen uit te voeren, zoals het maken van een Data Lake Storage Gen1-account, het aanbieden van de Data Lake Storage Gen1-accounts, enz. Zie [Bestandssysteembewerkingen](data-lake-store-data-operations-python.md)op Data Lake Storage Gen1 met Python voor instructies over het uitvoeren van bestandssysteembewerkingen op Data Lake Storage Gen1.

## <a name="prerequisites"></a>Vereisten

* **Python.** U kunt Python [hier](https://www.python.org/downloads/) downloaden. In dit artikel wordt Python 3.6.2 gebruikt.

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Een Azure-brongroep**. Zie [Een Azure-resourcegroep maken](../azure-resource-manager/management/manage-resource-groups-portal.md) voor instructies.

## <a name="install-the-modules"></a>De modules installeren

Als u met Data Lake Storage Gen1 wilt werken met Python, moet u drie modules installeren.

* Module `azure-mgmt-resource`, die Azure-modules bevat voor Active Directory enzovoort.
* De `azure-mgmt-datalake-store` module, die de beheerbewerkingen van Azure Data Lake Storage Gen1-accountbeheer omvat. Zie Naslaginformatie over de [Azure Data Lake Storage Gen1 Management-module voor](/python/api/azure-mgmt-datalake-store/)meer informatie over deze module .
* De `azure-datalake-store` module, die de bestandssysteembewerkingen van Azure Data Lake Storage Gen1 bevat. Zie [naslaginformatie](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/)over deze module voor meer informatie over deze module .

Gebruik de volgende opdrachten om de modules te installeren.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

1. Gebruik de gewenste IDE om een nieuwe Python-toepassing te maken, bijvoorbeeld **mysample.py**.

2. Voeg het volgende codefragment toe om de vereiste modules te importeren

    ```
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
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

## <a name="create-client-and-data-lake-storage-gen1-account"></a>Client- en Data Lake Storage Gen1-account maken

In het volgende fragment wordt eerst de Data Lake Storage Gen1-accountclient gemaakt. Het maakt gebruik van het clientobject om een Data Lake Storage Gen1-account te maken. Ten slotte maakt het fragment een clientobject voor het bestandssysteem.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'

    ## Create Data Lake Storage Gen1 account management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

    ## Create a Data Lake Storage Gen1 account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>De Data Lake Storage Gen1-accounts weergeven

    ## List the existing Data Lake Storage Gen1 accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-storage-gen1-account"></a>Het Data Lake Storage Gen1-account verwijderen

    ## Delete an existing Data Lake Storage Gen1 account
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>Volgende stappen
* [Bestandssysteembewerkingen op Data Lake Storage Gen1 met Python](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>Zie ook

* [Azure-datalake-store Python (Filesystem) referentie](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core)
* [Open Source Big Data-toepassingen die compatibel zijn met Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)
