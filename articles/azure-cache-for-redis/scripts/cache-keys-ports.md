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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75411308"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>De hostnaam, poorten en sleutels voor Azure Cache voor Redis ophalen

In dit scenario leert u hoe u de hostnaam, poorten en sleutels die worden gebruikt om verbinding met een Azure-Cache voor Redis-exemplaar te halen.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Uitleg van het script

Dit script worden de volgende opdrachten gebruikt om op te halen van de hostnaam, sleutels en poorten van een Azure-Cache voor Redis-exemplaar. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [AZ redis show](https://docs.microsoft.com/cli/azure/redis) | Gegevens van een Azure-Cache voor Redis-exemplaar niet ophalen. |
| [AZ redis list-keys](https://docs.microsoft.com/cli/azure/redis) | Toegangssleutel ophalen voor een Azure-Cache voor Redis-exemplaar. |


## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende Azure-Cache voor Redis CLI-voorbeeldscripts vindt u de [Azure Cache voor Redis-documentatie](../cli-samples.md).
