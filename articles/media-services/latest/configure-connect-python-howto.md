---
title: Verbinding maken met Azure Media Services v3 API - Python
description: In dit artikel wordt uitgelegd hoe u verbinding maken met Media Services v3 API met Python.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 98a8cdf4120cf56184eb5735249640e3423acdf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888458"
---
# <a name="connect-to-media-services-v3-api---python"></a>Verbinding maken met Media Services v3 API - Python

In dit artikel ziet u hoe u verbinding maken met de Azure Media Services v3 Python SDK met behulp van de aanmeldingsmethode voor serviceprincipal.

## <a name="prerequisites"></a>Vereisten

- Python downloaden van [python.org](https://www.python.org/downloads/)
- Zorg ervoor dat `PATH` u de omgevingsvariabele instelt
- [Een Azure Media Services-account maken](create-account-cli-how-to.md). Zorg ervoor dat u de naam van de brongroep en de naam van het Media Services-account onthoudt.
- Volg de stappen in het onderwerp [Access API's.](access-api-cli-how-to.md) Neem de abonnements-ID, toepassings-ID (client-ID), de verificatiesleutel (geheim) en de tenant-id op die u in de latere stap nodig hebt.

> [!IMPORTANT]
> [Naamgevingsconventies bekijken](media-services-apis-overview.md#naming-conventions).

## <a name="install-the-modules"></a>De modules installeren

Als u met Azure Media Services wilt werken met Python, moet u deze modules installeren.

* De `azure-mgmt-resource` module, die Azure-modules voor Active Directory bevat.
* De `azure-mgmt-media` module, die de mediaservices-entiteiten omvat.

Open een opdrachtregelgereedschap en gebruik de volgende opdrachten om de modules te installeren.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Verbinding maken met de Python-client

1. Een bestand maken `.py` met een extensie
1. Het bestand openen in uw favoriete editor
1. Voeg de code die volgt toe aan het bestand. De code importeert de vereiste modules en maakt het Active Directory-object dat u nodig hebt om verbinding te maken met Media Services.

      De waarden van de variabelen instellen op de waarden die u hebt gekregen van [Access API's](access-api-cli-how-to.md)

      ```
      import adal
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
      from azure.mgmt.media import AzureMediaServices
      from azure.mgmt.media.models import MediaService

      RESOURCE = 'https://management.core.windows.net/'
      # Tenant ID for your Azure Subscription
      TENANT_ID = '00000000-0000-0000-000000000000'
      # Your Service Principal App ID
      CLIENT = '00000000-0000-0000-000000000000'
      # Your Service Principal Password
      KEY = '00000000-0000-0000-000000000000'
      # Your Azure Subscription ID
      SUBSCRIPTION_ID = '00000000-0000-0000-000000000000'
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = 'amsv3account'
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = 'AMSv3ResourceGroup'

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials,
          RESOURCE,
          CLIENT,
          KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print (client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. Het bestand uitvoeren

## <a name="next-steps"></a>Volgende stappen

- Python [SDK gebruiken](https://aka.ms/ams-v3-python-sdk).
- Raadpleeg de [Python-naslagdocumentatie](https://aka.ms/ams-v3-python-ref) van Media Services.
