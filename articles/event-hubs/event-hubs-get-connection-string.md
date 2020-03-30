---
title: Verbindingstekenreeks krijgen - Azure Event Hubs | Microsoft Documenten
description: In dit artikel vindt u instructies voor het verkrijgen van een verbindingstekenreeks waarmee clients verbinding kunnen maken met Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: spelluru
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/19/2019
ms.author: spelluru
ms.openlocfilehash: 77a768f907ad989a457ee498f26ad0f6e004f786
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264932"
---
# <a name="get-an-event-hubs-connection-string"></a>Een verbindingstekenreeks voor gebeurtenishubs

Als u gebeurtenishubs wilt gebruiken, moet u een naamruimte voor gebeurtenishubs maken. Een naamruimte is een scopingcontainer voor meerdere gebeurtenishubs of Kafka-onderwerpen. Deze naamruimte geeft u een unieke [FQDN.](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) Zodra een naamruimte is gemaakt, u de verbindingstekenreeks verkrijgen die nodig is om met gebeurtenishubs te communiceren.

De verbindingstekenreeks voor Azure Event Hubs bevat de volgende componenten die zijn ingesloten,

* FQDN = de FQDN van de eventhubs-naamruimte die u hebt gemaakt (de naamruimte van EventHubs, gevolgd door servicebus.windows.net)
* SharedAccessKeyName = de naam die u hebt gekozen voor de SAS-sleutels van uw toepassing
* SharedAccessKey = de gegenereerde waarde van de sleutel.

De sjabloon verbindingstekenreeks ziet eruit als
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Een voorbeeldvan een verbindingstekenreeks kan er uitzien als`Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

Dit artikel leidt u door verschillende manieren van het verkrijgen van de verbinding string.

## <a name="get-connection-string-from-the-portal"></a>Verbindingstekenreeks ophalen van de portal
1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 
2. Selecteer **Alle services** in het linkernavigatiemenu. 
3. Selecteer **Gebeurtenishubs** in de sectie **Analytics.** 
4. Selecteer in de lijst met gebeurtenishubs uw gebeurtenishub.
6. Selecteer op de pagina **Naamruimte van gebeurtenishubs** de optie **Beleid voor gedeelde toegang** in het linkermenu.

    ![Menu-item Voor beleid voor gedeelde toegang](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. Selecteer een **beleid voor gedeelde toegang** in de lijst met beleidsregels. De standaardnaam heeft de naam: **RootManageSharedAccessPolicy**. U een beleid toevoegen met de juiste machtigingen (lezen, schrijven) en dat beleid gebruiken. 

    ![Beleid voor gedeelde toegangsbeleid voor gebeurtenishubs](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Selecteer de **knop kopiëren** naast het **veld Verbindingstekenreeks primaire toets.** 

    ![Gebeurtenishubs - verbindingstekenreeks](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>De verbindingstekenreeks met Azure PowerShell openen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U de [Get-AzEventHubKey](/powershell/module/az.eventhub/get-azeventhubkey) gebruiken om de verbindingstekenreeks voor de specifieke beleids-/regelnaam zoals hieronder weergegeven te krijgen:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>De verbindingstekenreeks met Azure CLI opmaken
U het volgende gebruiken om de verbindingstekenreeks voor de naamruimte op te halen:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

U het volgende ook gebruiken om de verbindingstekenreeks voor een EventHub-entiteit op te halen:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --eventhub-name dummyeventhub --name RootManageSharedAccessKey
```

Zie [Azure CLI voor gebeurtenishubs voor](/cli/azure/eventhubs)meer informatie over Azure CLI-opdrachten voor gebeurtenishubs.

## <a name="next-steps"></a>Volgende stappen

U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

* [Overzicht van gebeurtenishubs](event-hubs-what-is-event-hubs.md)
* [Een Event Hub maken](event-hubs-create.md)
