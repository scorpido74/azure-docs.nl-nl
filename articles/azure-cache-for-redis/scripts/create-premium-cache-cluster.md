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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75411152"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>Maken van een Premium Azure Cache voor Redis met clustering

In dit scenario leert u hoe u een Premium-laag van 6 GB Azure-Cache maken voor Redis Clustering is ingeschakeld en twee shards.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

Dit script worden de volgende opdrachten gebruikt om een resourcegroep te maken en een Premium-laag Azure Redis Cache met clustering inschakelen. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [AZ redis maken](https://docs.microsoft.com/cli/azure/redis) | Azure Cache voor Redis-exemplaar maken. |


## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende Azure-Cache voor Redis CLI-voorbeeldscripts vindt u de [Azure Cache voor Redis-documentatie](../cli-samples.md).
