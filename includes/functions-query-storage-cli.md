---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4fe159660421113e0f0ac0586ae7e4a22d5bcda7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77474137"
---
### <a name="query-the-storage-queue"></a>De opslagwachtrij opvragen

U [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) de opdracht gebruiken om de opslagwachtrijen in uw account weer te geven, zoals in het volgende voorbeeld:

```azurecli-interactive
az storage queue list --output tsv
```

De uitvoer van deze opdracht `outqueue`bevat een wachtrij met de naam , de wachtrij die is gemaakt toen de functie werd uitgevoerd.

Gebruik vervolgens [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) de opdracht om de berichten in deze wachtrij weer te geven, zoals in dit voorbeeld:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

De geretourneerde tekenreeks moet hetzelfde zijn als het bericht dat u hebt verzonden om de functie te testen.

> [!NOTE]  
> In het vorige voorbeeld wordt de geretourneerde tekenreeks van basis64 gedecodeerd. Dit komt omdat de wachtrijopslagbindingen naar Azure Storage schrijven en lezen als [base64-tekenreeksen.](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)