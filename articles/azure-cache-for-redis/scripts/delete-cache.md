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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75411082"
---
# <a name="delete-an-azure-cache-for-redis"></a>Een Azure-cache voor redis verwijderen

In dit scenario leert u hoe u een Azure-cache kunt verwijderen voor redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om een Azure-cache te verwijderen voor een redis-exemplaar. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [AZ redis Delete](https://docs.microsoft.com/cli/azure/redis) | Verwijder Azure cache voor redis-instantie. |


## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Extra Azure cache voor redis CLI-voorbeeld scripts vindt u in de [Azure-cache voor redis-documentatie](../cli-samples.md).
