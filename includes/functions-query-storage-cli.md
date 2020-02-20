---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4fe159660421113e0f0ac0586ae7e4a22d5bcda7
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77474137"
---
### <a name="query-the-storage-queue"></a>Query uitvoeren op de opslag wachtrij

U kunt de [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) opdracht gebruiken om de opslag wachtrijen in uw account weer te geven, zoals in het volgende voor beeld:

```azurecli-interactive
az storage queue list --output tsv
```

De uitvoer van deze opdracht bevat een wachtrij met de naam `outqueue`, die de wachtrij is die is gemaakt toen de functie werd uitgevoerd.

Gebruik vervolgens de opdracht [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) om de berichten in deze wachtrij weer te geven, zoals in dit voor beeld:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

De geretourneerde teken reeks moet hetzelfde zijn als het bericht dat u hebt verzonden om de functie te testen.

> [!NOTE]  
> In het vorige voor beeld wordt de geretourneerde teken reeks van base64 gedecodeerd. De reden hiervoor is dat de bindingen van de wachtrij opslag worden geschreven naar en gelezen van Azure Storage als [Base64-teken reeksen](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding).