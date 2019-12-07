---
title: Verbinding maken met Azure Media Services v3 API-python
description: In dit artikel wordt beschreven hoe u verbinding maakt met Media Services v3 API met python.
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
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74888458"
---
# <a name="connect-to-media-services-v3-api---python"></a>Verbinding maken met Media Services v3 API-python

In dit artikel wordt beschreven hoe u verbinding maakt met de Azure Media Services v3 python SDK met behulp van de aanmeldings methode voor de Service-Principal.

## <a name="prerequisites"></a>Vereisten

- Python downloaden via [python.org](https://www.python.org/downloads/)
- Zorg ervoor dat u de omgevings variabele `PATH` instelt
- [Een Azure Media Services-account maken](create-account-cli-how-to.md). Zorg ervoor dat u de naam van de resource groep en de naam van het Media Services account vergeet.
- Volg de stappen in het onderwerp [toegangs-api's](access-api-cli-how-to.md) . Noteer de abonnements-ID, toepassings-ID (client-ID), de verificatie sleutel (geheim) en de Tenant-ID die u in de volgende stap nodig hebt.

> [!IMPORTANT]
> Bekijk [naam conventies](media-services-apis-overview.md#naming-conventions).

## <a name="install-the-modules"></a>De modules installeren

Als u wilt werken met Azure Media Services met behulp van python, moet u deze modules installeren.

* De module `azure-mgmt-resource`, met inbegrip van Azure-modules voor Active Directory.
* De module `azure-mgmt-media`, die de Media Services entiteiten bevat.

Open een opdracht regel programma en gebruik de volgende opdrachten om de modules te installeren.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Verbinding maken met de python-client

1. Een bestand met een `.py`-extensie maken
1. Open het bestand in uw favoriete editor
1. Voeg de volgende code toe aan het bestand. Met de code worden de vereiste modules geïmporteerd en wordt het Active Directory-referentie object gemaakt dat u nodig hebt om verbinding te maken met Media Services.

      De waarden van de variabelen instellen op de waarden die u hebt ontvangen van [Access-api's](access-api-cli-how-to.md)

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

- [Python-SDK](https://aka.ms/ams-v3-python-sdk)gebruiken.
- Raadpleeg de [Python-naslagdocumentatie](https://aka.ms/ams-v3-python-ref) van Media Services.
