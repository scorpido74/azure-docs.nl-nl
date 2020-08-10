---
title: De hostnaam, poorten en sleutels opvragen van een exemplaar van Azure Cache voor Redis - Azure CLI
description: Dit voorbeeld met Azure CLI-code laat zien hoe u de details opvraagt van de hostnaam, poorten en sleutels van een exemplaar van Azure Cache voor Redis.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9fef834e550f144075ec9cc72cac3b11cc1e99e4
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504208"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>De hostnaam, poorten en sleutels opvragen van een exemplaar van Azure Cache voor Redis

In dit scenario leert u hoe u de details opvraagt van de hostnaam, poorten en sleutels die worden gebruikt om verbinding te maken met een exemplaar van Azure Cache voor Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om de hostnaam, sleutels en poorten op te vragen van een exemplaar van Azure Cache voor Redis. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | Details opvragen van een exemplaar van Azure Cache voor Redis. |
| [az redis list-keys](https://docs.microsoft.com/cli/azure/redis) | Toegangssleutels voor een exemplaar van Azure Cache voor Redis opvragen. |


## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van CLI-scripts voor Azure Cache voor Redis vindt u in de [documentatie van Azure Cache voor Redis](../cli-samples.md).
