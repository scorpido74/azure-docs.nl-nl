---
title: Azure Event Grid systeem onderwerpen maken, weer geven en beheren met CLI
description: In dit artikel wordt beschreven hoe u Azure CLI gebruikt om systeem onderwerpen te maken, weer te geven en te verwijderen.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: spelluru
ms.openlocfilehash: 19a22a0a3b528a9a72fdd51c589e42bf2fba5ce7
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669933"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>Event Grid systeem onderwerpen maken, weer geven en beheren met behulp van Azure CLI
In dit artikel wordt beschreven hoe u systeem onderwerpen maakt en beheert met behulp van Azure CLI. Zie [systeem onderwerpen](system-topics.md)voor een overzicht van systeem onderwerpen.

## <a name="install-extension-for-azure-cli"></a>Extensie voor Azure CLI installeren
Voor Azure CLI hebt u de [extensie Event grid](/cli/azure/azure-cli-extensions-list)nodig.

In Cloud shell:

- Als u de extensie eerder hebt geïnstalleerd, werkt u deze bij:`az extension update -n eventgrid`
- Als u de extensie nog niet eerder hebt geïnstalleerd, installeert u deze:`az extension add -n eventgrid`

Voor een lokale installatie:

1. [Installeer de Azure cli](/cli/azure/install-azure-cli). Zorg ervoor dat u de nieuwste versie hebt door te controleren met AZ--version.
2. Eerdere versies van de uitbrei ding verwijderen:`az extension remove -n eventgrid`
3. Installeer de eventgrid-extensie met`az extension add -n eventgrid`

## <a name="create-a-system-topic"></a>Een systeem onderwerp maken

- Als u eerst een systeem onderwerp wilt maken in een Azure-bron en vervolgens een gebeurtenis abonnement voor dat onderwerp wilt maken, raadpleegt u de volgende onderwerpen:
    - [AZ eventgrid System-topic Create](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-create)

        ```azurecli
        az eventgrid system-topic create \
            -g myResourceGroup \
            --name systemtopic1 \
            --location westus2 \
            --topic-type microsoft.storage.storageaccounts \
            --source /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/storagegaccountname
        ```
    - [AZ eventgrid System-onderwerp Event-Subscription Create](/cli/azure/ext/eventgrid/eventgrid/system-topic/event-subscription?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-event-subscription-create)

        ```azurecli
        az eventgrid system-topic event-subscription create --name es1 \
            -g rg1 --system-topic-name systemtopic1 \
            --endpoint https://contoso.azurewebsites.net/api/f1?code=code         
          ```
- To create a system topic (implicitly) when creating an event subscription for an Azure source, use the [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription?view=azure-cli-latest#ext-eventgrid-az-eventgrid-event-subscription-create) method. Here's an example:
    
    ```azurecli
    storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
    endpoint=https://$sitename.azurewebsites.net/api/updates

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <event_subscription_name> \
      --endpoint $endpoint
    ```
    Zie [Abonneren op een opslag account](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account)voor een zelf studie met stapsgewijze instructies.

## <a name="view-all-system-topics"></a>Alle systeem onderwerpen weer geven
Als u alle systeem onderwerpen en Details van een geselecteerd systeem onderwerp wilt weer geven, gebruikt u de volgende opdrachten:

- [AZ eventgrid System-topic List](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-list)

    ```azurecli
    az eventgrid system-topic list   
     ```
- [AZ eventgrid System-topic show](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-show)

    ```azurecli
    az eventgrid system-topic show -g rg1 -n systemtopic1    
     ```

## <a name="delete-a-system-topic"></a>Een systeem onderwerp verwijderen
Als u een systeem onderwerp wilt verwijderen, gebruikt u de volgende opdracht: 

- [AZ eventgrid System-onderwerp Delete](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-delete)

    ```azurecli
    az eventgrid system-topic delete -g myResourceGroup --name systemtopic1  
     ```

## <a name="next-steps"></a>Volgende stappen
Zie de sectie [systeem onderwerpen in azure Event grid](system-topics.md) voor meer informatie over systeem onderwerpen en onderwerp typen die door Azure Event grid worden ondersteund. 
