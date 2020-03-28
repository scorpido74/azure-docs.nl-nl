---
title: Een Azure-cache voor Redis verwijderen - Azure CLI
description: In dit voorbeeld van azure CLI-code ziet u hoe u een Azure-cache voor redis-instantie verwijdert met behulp van de opdracht az redis delete.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: e5178ab4069cc5ffa8607c5feea6ffac86284a5b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75411082"
---
# <a name="delete-an-azure-cache-for-redis"></a>Een Azure-cache voor Redis verwijderen

In dit scenario leert u hoe u een Azure-cache voor Redis verwijdert.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten om een Azure-cache voor de instantie Redis te verwijderen. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az redis verwijderen](https://docs.microsoft.com/cli/azure/redis) | Azure-cache verwijderen voor bijvoorbeeld Redis. |


## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende Azure-cache voor Redis CLI-scriptvoorbeelden is te vinden in de [Azure Cache for Redis-documentatie.](../cli-samples.md)
