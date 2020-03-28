---
title: Download de hostnaam, poorten, sleutels - Azure Cache for Redis - Azure CLI
description: In dit voorbeeld van Azure CLI-code ziet u hoe u de hostnaam, poorten en sleutels voor een Azure-cache voor bijvoorbeeld Redis ophalen.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 199c5707e9ecd887af64e271184e151ce0966745
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75411308"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Download de hostnaam, poorten en sleutels voor Azure Cache voor Redis

In dit scenario leert u hoe u de hostnaam, poorten en sleutels ophaalt die worden gebruikt om verbinding te maken met een Azure-cache voor bijvoorbeeld Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten om de hostnaam, sleutels en poorten van een Azure Cache voor redis-instantie op te halen. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | Gegevens ophalen van een Azure-cache voor bijvoorbeeld Redis. |
| [az redis lijst-toetsen](https://docs.microsoft.com/cli/azure/redis) | Toegangssleutels ophalen voor een Azure-cache voor bijvoorbeeld Redis. |


## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende Azure-cache voor Redis CLI-scriptvoorbeelden is te vinden in de [Azure Cache for Redis-documentatie.](../cli-samples.md)
