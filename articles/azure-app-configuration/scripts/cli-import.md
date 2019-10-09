---
title: Azure CLI-voorbeeldscript - importeren in een Azure-app-configuratiearchief | Microsoft Docs
description: Bevat informatie en voorbeeldscripts voor het importeren in een Azure-app-configuratiearchief
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
ms.openlocfilehash: cd1e54fc6cfbf254da010c03dfaa859a0ee8213c
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029809"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importeren in een Azure-app-configuratiearchief

Met dit voorbeeldscript importeert u sleutelwaarden in een Azure-app-configuratiearchief.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

U moet eerst de CLI-extensie voor het Azure App Configuration-archief installeren door de volgende opdracht uit te voeren:

        az extension add -n appconfig

## <a name="sample-script"></a>Voorbeeldscript

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

Dit script maakt gebruik van de volgende opdrachten om een app-configuratiearchief te importeren. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az appconfig import](/cli/azure/ext/appconfig/appconfig/kv#ext-appconfig-az-appconfig-kv-import) | Resource in een app-configuratiearchief importeren. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Meer voorbeelden van CLI-scripts voor een App Configuration-archief vindt u in de [documentatie over Azure App-configuratie](../cli-samples.md).
