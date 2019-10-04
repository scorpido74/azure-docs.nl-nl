---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 03ed1b28b4cabc054301e11c2b4d0f9e632abe02
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839120"
---
U kunt de [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) opdracht gebruiken om de opslag wachtrijen in uw account weer te geven, zoals in het volgende voor beeld:

```azurecli-interactive
az storage queue list --output tsv
```

De uitvoer van deze opdracht bevat een wachtrij met de naam `outqueue`. Dit is de wachtrij die is gemaakt toen de functie werd uitgevoerd.

Gebruik vervolgens de [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) opdracht om de berichten in deze wachtrij weer te geven, zoals in dit voor beeld:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

De geretourneerde teken reeks moet hetzelfde zijn als het bericht dat u hebt verzonden om de functie te testen.

> [!NOTE]  
> In het vorige voor beeld wordt de geretourneerde teken reeks van base64 gedecodeerd. De reden hiervoor is dat de bindingen van de wachtrij opslag worden geschreven naar en gelezen van Azure Storage als [Base64-teken reeksen](../articles/azure-functions/functions-bindings-storage-queue.md#encoding).