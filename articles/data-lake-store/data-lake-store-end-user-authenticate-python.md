---
title: 'Verificatie van eindgebruikers: Python met Azure Data Lake Storage Gen1 met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het bereiken van verificatie door eindgebruikers met Azure Data Lake Storage Gen1 met Azure Active Directory with Python
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c69f6c1f587285c5c52280c4c49008764d5b20d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265595"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Verificatie van eindgebruikers met Azure Data Lake Storage Gen1 met Python
> [!div class="op_single_selector"]
> * [Java gebruiken](data-lake-store-end-user-authenticate-java-sdk.md)
> * [.NET SDK gebruiken](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Python gebruiken](data-lake-store-end-user-authenticate-python.md)
> * [REST-API gebruiken](data-lake-store-end-user-authenticate-rest-api.md)
> 
> 

In dit artikel vindt u meer informatie over het gebruik van de Python SDK om verificatie van eindgebruikers uit te stellen met Azure Data Lake Storage Gen1. Verificatie door eindgebruikers kan verder worden opgesplitst in twee categorieën:

* Verificatie door eindgebruikers zonder meervoudige verificatie
* Verificatie door eindgebruikers met meervoudige verificatie

Beide opties worden besproken in dit artikel. Zie [Service-to-service-verificatie met Data Lake](data-lake-store-service-to-service-authenticate-python.md)Storage Gen1 met Python voor service-to-service-verificatie met Behulp van Data Lake Storage Gen1 met Python.

## <a name="prerequisites"></a>Vereisten

* **Python.** U kunt Python [hier](https://www.python.org/downloads/) downloaden. In dit artikel wordt Python 3.6.2 gebruikt.

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Maak een Azure Active Directory 'Native'-toepassing.** U moet de stappen in [verificatie van eindgebruikers met Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md)hebben voltooid met Azure Active Directory .

## <a name="install-the-modules"></a>De modules installeren

Als u met Data Lake Storage Gen1 wilt werken met Python, moet u drie modules installeren.

* Module `azure-mgmt-resource`, die Azure-modules bevat voor Active Directory enzovoort.
* De `azure-mgmt-datalake-store` module, die de beheerbewerkingen van Azure Data Lake Storage Gen1-accountbeheer omvat. Zie Naslaginformatie over de [Azure Data Lake Storage Gen1 Management-module voor](/python/api/azure-mgmt-datalake-store/)meer informatie over deze module .
* De `azure-datalake-store` module, die de bestandssysteembewerkingen van Azure Data Lake Storage Gen1 bevat. Zie naslaginformatie over de [filesysteemmodule azure-datalake-store](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/)voor meer informatie over deze module.

Gebruik de volgende opdrachten om de modules te installeren.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

1. Maak in de IDE van uw keuze bijvoorbeeld een nieuwe Python-toepassing **mysample.py.**

2. Voeg het volgende codefragment toe om de vereiste modules te importeren

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, pprint, uuid, time
    ```

3. Sla de wijzigingen in mysample.py op.

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Verificatie door eindgebruikers met meervoudige verificatie

### <a name="for-account-management"></a>Voor accountbeheer

Gebruik het volgende fragment om te verifiëren met Azure AD voor accountbeheerbewerkingen op een Data Lake Storage Gen1-account. Het volgende fragment kan worden gebruikt om een toepassing te verifiëren met Multi-Factor Authentication. Geef onderstaande waarden op voor een bestaande Azure **AD-native** toepassing.

    authority_host_url = "https://login.microsoftonline.com"
    tenant = "FILL-IN-HERE"
    authority_url = authority_host_url + '/' + tenant
    client_id = 'FILL-IN-HERE'
    redirect = 'urn:ietf:wg:oauth:2.0:oob'
    RESOURCE = 'https://management.core.windows.net/'
    
    context = adal.AuthenticationContext(authority_url)
    code = context.acquire_user_code(RESOURCE, client_id)
    print(code['message'])
    mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource = RESOURCE)

### <a name="for-filesystem-operations"></a>Voor bestandssysteembewerkingen

Gebruik dit om te verifiëren met Azure AD voor bestandssysteembewerkingen op een Data Lake Storage Gen1-account. Het volgende fragment kan worden gebruikt om een toepassing te verifiëren met Multi-Factor Authentication. Geef onderstaande waarden op voor een bestaande Azure **AD-native** toepassing.

    adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Verificatie door eindgebruikers zonder meervoudige verificatie

Dit is afgeschaft. Zie [Azure Authentication met Python SDK](/azure/python/python-sdk-azure-authenticate)voor meer informatie.
   
## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u verificatie van eindgebruikers gebruiken om te verifiëren met Azure Data Lake Storage Gen1 met Python. U nu de volgende artikelen bekijken waarin wordt gesproken over hoe u Python gebruiken om te werken met Azure Data Lake Storage Gen1.

* [Accountbeheerbewerkingen op Data Lake Storage Gen1 met Python](data-lake-store-get-started-python.md)
* [Gegevensbewerkingen op Data Lake Storage Gen1 met Python](data-lake-store-data-operations-python.md)

