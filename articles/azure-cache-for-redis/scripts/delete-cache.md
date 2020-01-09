---
title: Een Azure-cache verwijderen voor redis-Azure CLI
description: Dit Azure CLI-code voorbeeld laat zien hoe u een Azure-cache voor redis-exemplaar verwijdert met de opdracht AZ redis Delete.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: e5178ab4069cc5ffa8607c5feea6ffac86284a5b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75411082"
---
# <a name="delete-an-azure-cache-for-redis"></a>Verwijderen van een Azure-Cache voor Redis

In dit scenario leert u hoe u een Azure-Cache verwijderen voor Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

Met dit script maakt gebruik van de volgende opdrachten om te verwijderen van een Azure-Cache voor Redis-exemplaar. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [AZ redis delete](https://docs.microsoft.com/cli/azure/redis) | Azure Cache voor Redis-exemplaar verwijderen. |


## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende Azure-Cache voor Redis CLI-voorbeeldscripts vindt u de [Azure Cache voor Redis-documentatie](../cli-samples.md).
