---
title: Azure CLI-voorbeeldscript - een Azure-Cache verwijderen voor Redis
description: Azure CLI-voorbeeldscript - een Azure-Cache verwijderen voor Redis
author: yegu-ms
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: a2884fd326b6091680b8d81a905f3ee3320a2740
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121198"
---
# <a name="delete-an-azure-cache-for-redis"></a>Verwijderen van een Azure-Cache voor Redis

In dit scenario leert u hoe u een Azure-Cache verwijderen voor Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

Met dit script maakt gebruik van de volgende opdrachten om te verwijderen van een Azure-Cache voor Redis-exemplaar. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [AZ redis delete](https://docs.microsoft.com/cli/azure/redis) | Azure Cache voor Redis-exemplaar verwijderen. |


## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende Azure-Cache voor Redis CLI-voorbeeldscripts vindt u de [Azure Cache voor Redis-documentatie](../cli-samples.md).
