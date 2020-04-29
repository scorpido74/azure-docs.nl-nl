---
title: De hostnaam, poorten en sleutels ophalen-Azure cache voor redis-Azure CLI
description: Dit Azure CLI-code voorbeeld laat zien hoe u de hostnaam, poorten en sleutels voor een Azure-cache kunt ophalen voor een redis-exemplaar.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 199c5707e9ecd887af64e271184e151ce0966745
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75411308"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>De hostnaam, poorten en sleutels voor Azure cache ophalen voor redis

In dit scenario leert u hoe u de hostnaam, poorten en sleutels kunt ophalen die worden gebruikt om verbinding te maken met een Azure-cache voor redis-exemplaar.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om de hostnaam, sleutels en poorten van een Azure-cache op te halen voor redis-exemplaar. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [AZ redis show](https://docs.microsoft.com/cli/azure/redis) | Details ophalen van een Azure-cache voor redis-exemplaar. |
| [AZ redis List-Keys](https://docs.microsoft.com/cli/azure/redis) | De toegangs sleutels voor een Azure-cache ophalen voor een redis-exemplaar. |


## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Extra Azure cache voor redis CLI-voorbeeld scripts vindt u in de [Azure-cache voor redis-documentatie](../cli-samples.md).
