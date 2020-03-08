---
title: Verbindingsreeks - Azure Event Hubs ophalen | Microsoft Docs
description: In dit artikel vindt u instructies voor het ophalen van een verbindingsreeks die clients verbinding maken met Azure Event Hubs kunnen gebruiken.
services: event-hubs
documentationcenter: na
author: spelluru
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/19/2019
ms.author: spelluru
ms.openlocfilehash: 744151a1ce8cde630e26c17ccf06569ebd0efb61
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78365342"
---
# <a name="get-an-event-hubs-connection-string"></a>Een Event Hubs-verbindingsreeks ophalen

Voor het gebruik van Event Hubs, moet u een Event Hubs-naamruimte maken. Een naam ruimte is een container voor het bereik van meerdere Event hubs of Kafka-onderwerpen. Deze naam ruimte geeft u een unieke [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Zodra een naamruimte is gemaakt, kunt u de verbindingsreeks die nodig zijn voor communicatie met Event Hubs kunt verkrijgen.

De verbindingsreeks voor Azure Event Hubs bevat de volgende onderdelen die zijn ingesloten in het

* FQDN = de FQDN van de Event hubs-naam ruimte die u hebt gemaakt (deze bevat de naam van de Event hubs-naam ruimte gevolgd door servicebus.windows.net)
* SharedAccessKeyName = de naam die u hebt gekozen voor de SAS-sleutels van uw toepassing
* SharedAccessKey = de gegenereerde waarde van de sleutel.

De connection string sjabloon ziet eruit als
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Een voor beeld connection string kan er als volgt uitzien `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

In dit artikel leidt u door de verschillende manieren voor het verkrijgen van de verbindingsreeks.

## <a name="get-connection-string-from-the-portal"></a>Verbindingsreeks ophalen uit de portal
1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 
2. Selecteer **alle services** in het navigatie menu links. 
3. Selecteer **Event hubs** in het gedeelte **Analytics** . 
4. Selecteer uw Event Hub in de lijst met Event hubs.
6. Selecteer op de pagina **Event hubs naam ruimte** de optie **beleid voor gedeelde toegang** in het menu links.

    ![Menu-item voor Shared Access policies](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. Selecteer een **gedeeld toegangs beleid** in de lijst met beleids regels. De standaard naam is: **RootManageSharedAccessPolicy**. U kunt een beleid met de juiste machtigingen toevoegen (lezen, schrijven) en dat beleid gebruiken. 

    ![Beleid voor gedeelde toegang Event Hubs](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Selecteer de knop **kopiëren** naast het veld **verbindings reeks-primaire sleutel** . 

    ![Event Hubs-connection string ophalen](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>De verbindingsreeks met Azure PowerShell ophalen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt de [Get-AzEventHubNamespaceKey](/powershell/module/az.eventhub/get-azeventhubkey) gebruiken om de Connection String op te halen voor de naam van het specifieke beleid/de regel, zoals hieronder wordt weer gegeven:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Ophalen van de verbindingsreeks met Azure CLI
U kunt het volgende gebruiken om op te halen van de verbindingsreeks voor de naamruimte:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

U kunt ook het volgende gebruiken om de connection string voor een EventHub-entiteit op te halen:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --eventhub-name dummyeventhub --name RootManageSharedAccessKey
```

Zie voor meer informatie over Azure CLI-opdrachten voor Event Hubs [Azure CLI voor Event hubs](/cli/azure/eventhubs).

## <a name="next-steps"></a>Volgende stappen

U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

* [Event Hubs-overzicht](event-hubs-what-is-event-hubs.md)
* [Een Event Hub maken](event-hubs-create.md)
