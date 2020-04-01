---
title: Voorbeeld van Azure CLI-script - Importeren in een app-configuratiearchief
titleSuffix: Azure App Configuration
description: Azure CLI-script gebruiken - Configuratie importeren in Azure App-configuratie
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 71d6aafa82f647b9c6164ee9a06b43ed7e9a66af
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77523592"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importeren in een Azure-app-configuratiearchief

Met dit voorbeeldscript worden de instellingen voor sleutelwaarde geïmporteerd in een Azure App Configuration Store.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest de Azure CLI lokaal te installeren en te gebruiken, moet u in dit artikel de Azure CLI-versie 2.0 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie De Azure [CLI installeren](/cli/azure/install-azure-cli)als u de Azure CLI wilt installeren.

## <a name="sample-script"></a>Voorbeeldscript

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten om te importeren in een app-configuratiearchief. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [az appconfig kv importeren](/cli/azure/appconfig/kv#az-appconfig-kv-import) | Importeert naar een app-configuratieopslagbron. |

## <a name="next-steps"></a>Volgende stappen

Zie de [Azure CLI-documentatie](/cli/azure)voor meer informatie over de Azure CLI.

Aanvullende CLI-scriptvoorbeelden voor app-configuratie zijn te vinden in de [bemonsteringen van Azure App Configuration CLI.](../cli-samples.md)
