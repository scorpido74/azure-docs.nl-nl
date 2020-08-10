---
title: Een exemplaar van Azure Cache voor Redis verwijderen met Azure CLI
description: Dit voorbeeld van Azure CLI-code laat zien hoe u een exemplaar van Azure Cache voor Redis verwijdert met de opdracht az redis delete.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: b1e044c6f61e51de1d8f2ba07f64918307109e46
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494720"
---
# <a name="delete-an-azure-cache-for-redis"></a>Een exemplaar van Azure Cache voor Redis verwijderen

In dit scenario leert u hoe u een exemplaar van Azure Cache voor Redis verwijdert.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om een exemplaar van Azure Cache voor Redis verwijderen. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az redis delete](https://docs.microsoft.com/cli/azure/redis) | Exemplaar van Azure Cache voor Redis verwijderen. |


## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van CLI-scripts voor Azure Cache voor Redis vindt u in de [documentatie van Azure Cache voor Redis](../cli-samples.md).
