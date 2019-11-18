---
title: Voor beeld van Azure CLI-script-de hostnaam, poorten en sleutels voor Azure cache ophalen voor redis
description: 'Azure CLI-Script voorbeeld: ophalen van de hostnaam, poorten en sleutels voor een Azure-Cache voor Redis-exemplaar'
author: yegu-ms
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: 604ec6de3b95a4bc289176d54d9c7b0a6c42eae6
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122510"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>De hostnaam, poorten en sleutels voor Azure Cache voor Redis ophalen

In dit scenario leert u hoe u de hostnaam, poorten en sleutels die worden gebruikt om verbinding met een Azure-Cache voor Redis-exemplaar te halen.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Uitleg van het script

Dit script worden de volgende opdrachten gebruikt om op te halen van de hostnaam, sleutels en poorten van een Azure-Cache voor Redis-exemplaar. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [AZ redis show](https://docs.microsoft.com/cli/azure/redis) | Gegevens van een Azure-Cache voor Redis-exemplaar niet ophalen. |
| [AZ redis list-keys](https://docs.microsoft.com/cli/azure/redis) | Toegangssleutel ophalen voor een Azure-Cache voor Redis-exemplaar. |


## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende Azure-Cache voor Redis CLI-voorbeeldscripts vindt u de [Azure Cache voor Redis-documentatie](../cli-samples.md).
