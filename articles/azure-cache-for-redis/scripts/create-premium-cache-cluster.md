---
title: Een Premium Azure-cache maken voor redis met clustering-Azure CLI
description: Dit Azure CLI-code voorbeeld laat zien hoe u een Azure-cache van 6 GB maakt voor redis met clustering ingeschakeld en twee Shards.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 68203fe2e054f32ce5764fe4f1b07013b0806104
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75411152"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>Een Premium Azure-cache maken voor redis met clustering

In dit scenario leert u hoe u een Azure-cache voor de Premium-laag van 6 GB maakt voor redis waarbij Clustering is ingeschakeld en twee Shards.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt voor het maken van een resource groep en een Azure-cache voor de Premium-laag voor redis waarbij Clustering is ingeschakeld. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [AZ redis Create](https://docs.microsoft.com/cli/azure/redis) | Maak een Azure-cache voor het redis-exemplaar. |


## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Extra Azure cache voor redis CLI-voorbeeld scripts vindt u in de [Azure-cache voor redis-documentatie](../cli-samples.md).
