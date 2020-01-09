---
title: Voor beeld van Azure CLI-script-exporteren vanuit een Azure-app configuratie archief
titleSuffix: Azure App Configuration
description: Bevat informatie en voorbeeldscripts voor het exporteren vanuit een Azure-app-configuratiearchief
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 28066fcea50180189cff7f9af790908c52a4a261
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433599"
---
# <a name="export-from-an-azure-app-configuration-store"></a>Exporteren vanuit een Azure-app-configuratiearchief

Met dit voorbeeldscript exporteert u sleutelwaarden vanuit een Azure-app-configuratiearchief.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

U moet eerst de CLI-extensie voor het Azure App Configuration-archief installeren door de volgende opdracht uit te voeren:

        az extension add -n appconfig

## <a name="sample-script"></a>Voorbeeldscript

```azurecli-interactive
#!/bin/bash

# Export all key-values
az appconfig kv export --name myTestAppConfigStore --file ~/Export.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om vanuit een app-configuratie archief te exporteren. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [AZ appconfig KV export](/cli/azure/ext/appconfig/appconfig/kv#ext-appconfig-az-appconfig-kv-export) | Exporteert vanuit een app-configuratie archiefbestand. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Extra CLI-script voorbeelden voor configuratie van apps vindt u in de voor beelden van de [Azure-app configuratie-cli](../cli-samples.md).
