---
title: Een Premium Azure-cache voor Redis maken met clustering - Azure CLI
description: In dit voorbeeld van Azure CLI-code ziet u hoe u een Azure-cache voor 6 GB Premium-laag voor Redis maakt met ingeschakeldvoor clustering en twee shards.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 68203fe2e054f32ce5764fe4f1b07013b0806104
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75411152"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>Een Premium Azure-cache voor Redis maken met clustering

In dit scenario leert u hoe u een Azure-cache voor 6 GB Premium-laag voor Redis maakt met ingeschakelde clustering en twee shards.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten om een resourcegroep en een Premium-laag Azure Cache voor Redis te maken met ingeschakeld clustering. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az redis maken](https://docs.microsoft.com/cli/azure/redis) | Azure-cache maken voor bijvoorbeeld Redis. |


## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende Azure-cache voor Redis CLI-scriptvoorbeelden is te vinden in de [Azure Cache for Redis-documentatie.](../cli-samples.md)
